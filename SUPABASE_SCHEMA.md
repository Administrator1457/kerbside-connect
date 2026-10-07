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
