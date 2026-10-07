# Kerbside Connect — Supabase Production Setup

Updated 2026-10-07.

## Live project
- Project: Kerbside Connect
- Region: ap-southeast-2
- Database: PostgreSQL 17
- Status: ACTIVE_HEALTHY

## Production controls implemented
- `Reports` ownership via `user_id`
- Report lifecycle: `active`, `expired`, `removed`
- Server-side `expires_at` with a three-day default
- PostgreSQL Cron job `kerbside-expire-reports` every 15 minutes
- Public reads restricted to active, non-expired reports
- Authenticated users can update/delete only their own reports
- Moderators/admins can review and remove reports
- `report_flags` has moderation state and reviewer fields
- Dedicated `user_roles` table with `member`, `moderator`, and `admin`
- Moderator authorization is evaluated server-side through a private security-definer helper
- Report text length checks enforced in the database
- Direct report insertion is blocked; submissions go through the `submit-report` Edge Function
- Direct flag insertion is blocked; flags go through the `flag-report` Edge Function
- Security-definer helper execution is not exposed to `anon` or `authenticated`
- Supabase security advisor currently reports zero security lints

## Image storage
Bucket: `report-images`

- Public image delivery
- Maximum file size: 4 MB
- Allowed types: JPEG, PNG, WebP
- Browser no longer stores report photos as database base64
- `submit-report` uploads images server-side and stores the public Storage URL in `Reports.Photo`

## Server-side anti-spam
`submit-report` uses the caller IP for anonymous requests and authenticated user ID for signed-in requests.

- Anonymous: 5 report submissions/hour
- Authenticated: 10 report submissions/hour
- Flags: 20/hour per IP

Rate limiting is stored in a private database table and incremented atomically.

## Edge Functions
- `submit-report` — validates report input, rate limits requests, creates the report, uploads the image to Storage and returns the report ID/expiry.
- `flag-report` — validates and rate limits flags, verifies the report exists, and records the moderation flag.

Both functions use CORS handling and server-side secrets only.

## Moderator setup
Create the user's account through the app first. Then, using the Supabase SQL editor, an owner can promote the account:

```sql
insert into public.user_roles (user_id, role)
values ('USER_UUID_HERE', 'admin')
on conflict (user_id)
do update set role = excluded.role;
```

Use `moderator` for a normal moderation account. Never expose service/secret keys in the browser.

## Realtime
Postgres Changes is enabled for:
- `public.Reports`
- `public.report_flags`

The frontend still has a 60-second refresh fallback.

## Remaining release work
1. Confirm the live GitHub Pages deployment URL.
2. Run a real end-to-end report submission with a test account/device.
3. Confirm image upload and public image display.
4. Confirm flag → moderator review → removal flow.
5. Confirm an expired report disappears from public reads.
6. Configure the first production admin account.
7. Complete Android signing/Play Console setup.
8. Complete Apple signing/App Store Connect setup.

This document records the implemented production backend. It does not replace the final end-to-end release test.
