# Permissive RLS Policy Combined with Missing Ownership Checks Allows Any User to Read, Modify, or Delete Any Other User's Prompts

**Project:** [pinkpixel-dev/promptzy](https://github.com/pinkpixel-dev/promptzy)
**Reported by:** Tanjot Singh ([@Tanjot-Singh-cyber](https://github.com/Tanjot-Singh-cyber))
**Status:** Draft — pending submission
**Severity:** High (CVSS 3.1: 8.2 — see scoring below)
**Class:** Broken Access Control / Insecure Direct Object Reference (IDOR)
**CWE:** CWE-863 (Incorrect Authorization), CWE-639 (Authorization Bypass Through User-Controlled Key)

---

## Summary

The `prompts` table's Row Level Security policy is defined as `FOR ALL USING (true)`, with no ownership predicate. Combined with a `user_id` column that is an arbitrary, client-settable text string (not tied to real Supabase Auth identity), and application code that only applies `user_id` filtering on the *read* path (and even then, purely client-side), any client holding the application's public Supabase anon key can:

- **Read** any other user's prompts, including those marked private (`ispublic = false`)
- **Overwrite** any other user's prompt content, by ID, via `upsert`
- **Delete** any other user's prompt, by ID — this function applies **no `user_id` filter at all**

No authentication beyond possession of the public anon key (which ships embedded in every deployment's client bundle) is required for any of the above.

---

## Details

### 1. Root cause — permissive RLS policy
`supabase-setup.sql`

```sql
ALTER TABLE prompts ENABLE ROW LEVEL SECURITY;
...
CREATE POLICY "Allow all operations for now" ON prompts FOR ALL USING (true);
```

This single policy covers `SELECT`, `INSERT`, `UPDATE`, and `DELETE` with no ownership check. The script's own comments describe this as temporary ("Temporary allow all for setup... once confirmed, remove the temporary policy"), but no replacement, stricter policy exists anywhere in the repository, and this is the exact script referenced in `README.md`'s setup instructions.

### 2. `user_id` is not a real identity
`src/lib/supabasePromptStore.ts`

```sql
user_id TEXT NOT NULL,
```

`user_id` is a free-form string, not bound to Supabase Auth (`auth.uid()`). The README instructs users to "Set a custom user ID in settings to sync across browsers" — i.e., it is fully client-chosen and has no cryptographic or session-based binding to any actual account.

### 3. Read path — client-side filtering only

```js
.eq('user_id', userId);
```

Reads are scoped to `user_id` only in the application's own query construction. This is a convenience filter, not a security boundary — any direct call to the Supabase REST API with the public anon key, omitting or altering this filter, returns all users' prompts regardless of `ispublic` status.

### 4. Write path — `upsert` with no ownership check

```js
const { error } = await client
  .from('prompts')
  .upsert(promptToSave, { onConflict: 'id' });
```

`upsert` with `onConflict: 'id'` means supplying an existing prompt's `id` overwrites that row's content in place, regardless of who originally created it. No check exists anywhere in this function confirming the caller owns the target `id`.

### 5. Delete path — no ownership check at all

```js
export const deletePromptFromSupabase = async (id: string): Promise<boolean> => {
  ...
  const { error } = await client
    .from('prompts')
    .delete()
    .eq('id', id);
  ...
};
```

This function filters **only** by `id`. There is no `user_id` (or any other ownership) check anywhere in the delete path, at either the application layer or the database layer (since RLS is `USING (true)`).

---

## Impact

Any client holding the application's public Supabase anon key — present in every deployment's client bundle — can, for any prompt in the database regardless of owner:

- Read the full content of private (`ispublic = false`) prompts belonging to other users
- Overwrite any other user's prompt content via `upsert`
- Delete any other user's prompt entirely, with no ownership check at any layer

This affects every self-hosted deployment using the documented `supabase-setup.sql` setup script, which is the current script referenced in `README.md`.

---

## Reproduction Steps

1. Deploy promptzy following `supabase-setup.sql` (the current, README-referenced setup script).
2. As User A, create a prompt (public or private) and note its `id`.
3. As an unrelated client holding only the public Supabase anon key (no valid session/account required for User A's data):
   - **Read:** query `prompts` via the Supabase REST endpoint without a `user_id` filter, or with a different `user_id` value, and observe User A's prompt returned.
   - **Overwrite:** call `upsert` on the `prompts` table with User A's `id` and different `content`; observe User A's prompt is silently replaced.
   - **Delete:** call `delete` on the `prompts` table filtered only by User A's `id`; observe the prompt is removed with no ownership check.

---

## Affected Files

- `supabase-setup.sql` (RLS policy, section 6)
- `src/lib/supabasePromptStore.ts` (read at line ~286, upsert at line ~358, delete at line ~373)

---

## Suggested Fix

1. **RLS policy** must scope every operation to an authenticated identity, e.g.:
   ```sql
   CREATE POLICY "Users can only access their own prompts" ON prompts
     FOR ALL USING (auth.uid()::text = user_id) WITH CHECK (auth.uid()::text = user_id);
   ```
   This requires migrating `user_id` off the current free-form client-chosen string and onto real Supabase Auth identities (`auth.uid()`).

2. Until real auth is implemented, at minimum the application-layer `upsert` and `delete` calls should never be trusted as the sole enforcement mechanism — RLS must independently enforce ownership regardless of client-side query construction, since the anon key is public by design.

---

## CVSS 3.1 Scoring

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

| Metric | Value | Rationale |
|---|---|---|
| Attack Vector | Network | Exploited via Supabase REST API |
| Attack Complexity | Low | No special conditions; anon key is public by design |
| Privileges Required | None | No app account or session needed |
| User Interaction | None | Fully automatable |
| Scope | Unchanged | Impact stays within the same Supabase project |
| Confidentiality | High | Full read access to all users' private prompts |
| Integrity | High | Any user's prompt can be overwritten via upsert |
| Availability | High | Any user's prompt can be deleted with no ownership check |

---

## Disclosure Timeline

| Date | Event |
|---|---|
| TBD | Vulnerability identified via source code review |
| TBD | To be reported via private GitHub Security Advisory to pinkpixel-dev/promptzy |
| TBD | Maintainer response |
| TBD | Patch released |
| TBD | CVE requested and assigned |

---

*This report is intended for private disclosure to the maintainer prior to any public discussion, per responsible disclosure practice.*
