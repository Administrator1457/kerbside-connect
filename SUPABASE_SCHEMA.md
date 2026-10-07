# Supabase production schema plan

This file documents the backend changes Kerbside Connect will eventually need. It is a plan, not confirmation that these columns or policies already exist.

## Reports

Existing frontend-used fields:

- `id`
- `Suburb`
- `Street`
- `Category` — array of categories
- `Lat`
- `lng` or `Lng`
- `Description`
- `Photo`
- `Date`
- `created_at`

Recommended additions after confirming the live schema:

- `user_id uuid references auth.users(id)`
- `status text` — for example `active`, `gone`, `removed`
- `updated_at timestamptz`
- `expires_at timestamptz`

Recommended indexes:

- `created_at`
- `expires_at`
- `status`
- `user_id`
- geographic index if the project later moves to PostGIS queries

## report_flags

Recommended fields:

- `id`
- `report_id` referencing `Reports.id`
- `user_id` referencing `auth.users.id`, nullable if anonymous flags remain supported
- `reason`
- `status` — `open`, `reviewed`, `dismissed`
- `created_at`
- `reviewed_at`
- `reviewed_by` referencing `auth.users.id`

## Row Level Security

The intended production policy model is:

1. Public users can read only active, non-expired reports.
2. Users can create reports subject to server-side validation/rate limits.
3. Authenticated users can update/delete only their own reports.
4. Moderators/admins can review, hide and restore reports.
5. Users can create flags but cannot read other users' moderation records.
6. Moderators/admins can read and resolve flags.

Exact SQL must be written against the actual live schema and Supabase roles before applying it.

## Images

The current app stores a compressed image as data in the Reports row. This is suitable for early testing but should not be the final production design.

Recommended production approach:

- Supabase Storage bucket for report photos
- authenticated upload rules
- size/type validation
- generated thumbnail where practical
- store only the public/signed object path in `Reports.Photo`

Do not invent a bucket name or policy until Storage configuration is confirmed.

## Server-side expiry

The frontend currently hides reports older than three days. Production should also enforce expiry in the database/query layer so old reports cannot remain publicly visible if a user bypasses the frontend.

## Moderation

A future private moderator interface should provide:

- flagged report queue
- report preview
- flag reason
- hide/restore action
- mark report as gone
- audit information
- basic abuse/rate-limit visibility

Admin controls must never be exposed solely by hiding buttons in the public frontend.


## Safe migration order

Apply production backend changes in this order after confirming the live schema:

1. Back up/export the existing Reports and report_flags data.
2. Add new nullable ownership/status/timestamp fields before making any field required.
3. Add indexes and verify existing queries still work.
4. Configure Supabase Storage for report images and test upload permissions separately.
5. Deploy server-side validation/rate limiting.
6. Enable Row Level Security and test anonymous read/create, authenticated ownership, and moderator access with separate test accounts.
7. Enable/verify Realtime for Reports if live updates are required.
8. Update the frontend to use the confirmed production fields and Storage paths.
9. Test expiry, moderation, flagging, account flows and image deletion.
10. Only then remove legacy base64 photo storage if existing records have been migrated safely.

### Required verification before SQL is applied

The following must be confirmed from the live Supabase project rather than guessed:

- Exact column names and data types for Reports
- Exact primary key type for Reports.id
- Exact column names and types for report_flags
- Existing foreign keys and constraints
- Existing RLS policies
- Existing Storage buckets and policies
- Whether Reports is enabled for Realtime
- Current Auth redirect URLs
- Whether anonymous report creation is intended

Do not run migration SQL until these values are known.
