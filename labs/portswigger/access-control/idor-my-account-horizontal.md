# Lab: Insecure Direct Object Reference (Horizontal Privilege Escalation)

## Observation:
The /my-account endpoint accepts a user identifier via the 'id' parameter.

## Request:
GET /my-account?id=wiener

## Issue:
The application does not verify whether the authenticated user is authorized to access the requested user account.

## Exploitation:
By modifying the 'id' parameter from 'wiener' to 'carlos', the server returns Carlos’s account information while authenticated as Wiener.

## Impact:
An authenticated user can access other users’ sensitive account data.

## Root Cause:
Missing server-side authorization check binding the requested object to the authenticated session user.
