# Kerbside Connect

Kerbside Connect is a mobile-first community app for discovering and reporting useful items left out during kerbside clean-ups.

## Current features

- Community clean-up reports stored in Supabase
- Approximate report locations for privacy
- Interactive OpenStreetMap/Leaflet map
- Search and category filters
- Nearby-distance sorting when location is enabled
- Optional Supabase email/password accounts
- Password reset flow
- Community report sharing
- Report flagging/moderation queue
- Device-saved reports
- Shareable deep links to individual reports
- Hardened photo rendering and input handling
- Three-day report expiry in the app
- Duplicate-report warning
- Client-side photo resizing/compression
- Live Supabase Realtime updates with a 60-second fallback refresh
- Installable PWA shell and offline status handling
- Council information links

## Supabase configuration

The current frontend uses:

- Supabase project URL configured in `index.html`
- Supabase publishable key configured in `index.html`
- `Reports` table
- `report_flags` table

The existing Reports fields are read defensively because the exact production schema has not yet been fully documented in this repository.

## Important production backend work

Before launch, the database should be hardened with Row Level Security, ownership fields, moderation status, server-side expiry, indexes, rate limiting, and proper image storage.

Do not add guessed columns or policies to the live database. Apply the backend changes only after confirming the actual Supabase schema.

## Privacy principles

- Precise device location is not stored by the frontend; submitted coordinates are rounded to approximately three decimal places.
- Location use is optional.
- Account creation is optional.
- Report photos and descriptions should not contain private or sensitive information.
- Community reports are not presented as council-confirmed information.

## Deployment

The app can be hosted as a static site. GitHub Pages, another static host, or a compatible web host can serve the repository. HTTPS is recommended because browser geolocation and PWA features require a secure context in normal deployment.

## Roadmap

See `SUPABASE_SCHEMA.md` for the proposed backend structure required for ownership, moderation, report status, expiry, and scalable image storage.
