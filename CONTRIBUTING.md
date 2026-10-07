# Contributing to Kerbside Connect

Thanks for helping improve Kerbside Connect.

## Development principles

- Keep the app mobile-first and usable on small screens.
- Do not expose precise user location in public reports.
- Escape user-submitted text before rendering it as HTML.
- Do not add database columns, RLS policies or Storage policies based on assumptions.
- Prefer small, focused changes that can be tested independently.
- Preserve the offline/PWA experience when changing the frontend.
- Avoid storing unnecessary personal information.

## Before changing the app

1. Read the current README.md.
2. Check SUPABASE_SCHEMA.md before changing database-related code.
3. Check SECURITY.md before changing authentication, uploads, moderation or permissions.
4. Test the affected feature on a mobile-sized screen.
5. Check both online and offline behaviour where relevant.

## Change checklist

- [ ] Clear purpose
- [ ] Report submission still works
- [ ] Search and filters still work
- [ ] Map and location privacy still work
- [ ] Saved reports and sharing still work
- [ ] Authentication still works if affected
- [ ] No secrets or private data added
- [ ] User content remains escaped
- [ ] PWA/service-worker behaviour remains intact
- [ ] Documentation updated when needed

## Backend changes

Backend changes must be based on the confirmed live Supabase schema. Never guess column names, foreign keys, RLS policies, Storage buckets or roles.

For production database work, follow the migration order documented in SUPABASE_SCHEMA.md.
