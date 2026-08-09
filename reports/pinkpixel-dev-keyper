# Unwrapped DEK Storage Combined with Permissive RLS Policies Allows Unauthenticated Full Vault Compromise

**Project:** [pinkpixel-dev/keyper](https://github.com/pinkpixel-dev/keyper)
**Reported by:** Tanjot Singh ([@Tanjot-Singh-cyber](https://github.com/Tanjot-Singh-cyber))
**Status:** Disclosed via GitHub Security Advisory — awaiting maintainer response
**Severity:** Critical (CVSS 3.1: 9.8)
**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`
**CWE:** CWE-863 (Incorrect Authorization), CWE-311/312 (Missing/Cleartext Storage of Sensitive Information)
**Date reported:** August 2026

---

## Summary

The vault's Data Encryption Key (DEK) is generated independently of the user's master passphrase and stored in plaintext (base64-encoded) in `vault_config.raw_dek`. The passphrase is used only for a `bcrypt.compare()` check and has no cryptographic relationship to the DEK. Combined with Row Level Security (RLS) policies on the `credentials`, `vault_config`, and `categories` tables that use `USING (true)` with no ownership predicate, any client holding the Supabase anon key — which is necessarily public, as it ships embedded in the client application — can read any user's `raw_dek` and `secret_blob` directly via the Supabase REST API and fully decrypt all stored credentials, for any user, without ever knowing that user's passphrase.

This defeats the "zero-knowledge encryption" security model the application is built and marketed around.

---

## Details

### 1. Root cause — DEK not bound to passphrase
`src/services/SecureVault.ts`

`createNewVault()` generates a random 256-bit AES-GCM key independently of any passphrase:

```js
const dek = await crypto.subtle.generateKey(
  { name: "AES-GCM", length: 256 }, true, ["encrypt", "decrypt"]
);
```

This raw key is persisted directly to the database (`src/services/VaultManager.ts`, `createVault()`):

```js
const rawDEK = await secureVault.createNewVault();
await saveVaultConfig(rawDEK, bcryptHash);
```

On unlock (`unlockVault()` in `VaultManager.ts`), the passphrase is checked only against the bcrypt hash:

```js
const isValidPassphrase = await verifyPassphrase(masterPassphrase, config.bcrypt_hash);
...
await secureVault.unlockWithStoredDEK();
```

`unlockWithStoredDEK()` (`SecureVault.ts`) imports the stored raw bytes directly as the usable AES-GCM key, with no unwrap/decrypt step:

```js
this.dek = await crypto.subtle.importKey(
  "raw", dekBytes, { name: "AES-GCM" }, false, ["encrypt", "decrypt"]
);
```

The module's own docstring states this plainly:

> Server sees: ciphertexts + raw DEK + bcrypt hash
> Unlock: bcrypt verify passphrase → load DEK → decrypt secrets

**Note:** an older `wrapped_dek` architecture exists in the same codebase (`WrappedDEK` / `unlock()` / `testPassphrase()` in `SecureVault.ts`) that correctly derives a key-encrypting-key from the passphrase via Argon2id/PBKDF2 and uses it to encrypt the DEK before storage. This correct approach was replaced by the current `raw_dek` + bcrypt-only architecture for new vaults.

### 2. Exploitation path — permissive RLS
`supabase-setup.sql`, section 6

Every policy on `credentials`, `vault_config`, and `categories` is defined with no ownership check:

```sql
CREATE POLICY "vault_config_select_policy" ON vault_config
  FOR SELECT USING (true);
```

(identical pattern for INSERT/UPDATE/DELETE, and for the other two tables). This script is the current, actively maintained setup script referenced in `OVERVIEW.md` and `CHANGELOG.md`.

### 3. Combined impact

Any client using the application's public Supabase anon key can query `vault_config` and `credentials` for any `user_id` and retrieve:

- `raw_dek` — the actual usable AES-256 key, in the clear
- `secret_blob` — the encrypted credential data

With both in hand, decryption requires no interaction with the passphrase at all — bcrypt is never involved in decryption, only in the UI's login gate.

---

## Impact

Complete, unauthenticated compromise of all users' stored credentials in any self-hosted multi-user deployment following the documented setup process. An attacker with only the public anon key — present in every deployment's client bundle — can:

- Decrypt every credential stored by every user, without needing any user's passphrase
- Read, modify (`WITH CHECK (true)` on UPDATE/INSERT), and delete (`USING (true)` on DELETE) any user's data
- Do all of the above without valid application authentication and without any privileged database access

---

## Reproduction Steps

1. Deploy Keyper following `supabase-setup.sql` (the current, actively-referenced setup script).
2. As User A, create a vault and store a credential.
3. As an unauthenticated/unrelated client holding only the public Supabase anon key, query the `vault_config` and `credentials` tables via the Supabase REST endpoint, filtering by User A's `user_id`.
4. Use the returned `raw_dek` to decrypt the returned `secret_blob` directly via AES-GCM — no passphrase required at any point.

---

## Affected Files

- `supabase-setup.sql` (RLS policies, section 6)
- `src/services/SecureVault.ts` (`createNewVault`, `unlockWithStoredDEK`)
- `src/services/VaultManager.ts` (`createVault`, `unlockVault`)

---

## Suggested Fix

1. **RLS policies** must scope every operation to an authenticated identity (e.g. `user_id = auth.uid()`), which requires moving off the shared `'self-hosted-user'` string and onto real per-user Supabase auth identities.

2. **The DEK must not be stored raw.** It should be wrapped with a key derived from the passphrase before persistence, as the existing legacy `wrapped_dek` code path already does correctly (Argon2id/PBKDF2 → AES-GCM wrap). Bcrypt verification alone provides authentication of the passphrase but no confidentiality guarantee for the DEK.

---

## Disclosure Timeline

| Date | Event |
|---|---|
| August 2026 | Vulnerability identified via source code review |
| August 2026 | Reported via private GitHub Security Advisory to pinkpixel-dev/keyper |
| August 2026 | Courtesy notification sent to maintainer email |
| TBD | Maintainer response |
| TBD | Patch released |
| TBD | CVE requested and assigned |

---

*This report was privately disclosed to the maintainer prior to any public discussion, per responsible disclosure practice. No public exploitation of this issue is known to have occurred.*
