# Kerbside Connect Production Release

## Current release architecture

- Web/PWA: static app published through GitHub Pages.
- Backend: Supabase.
- Native packaging: Capacitor for Android and iOS.
- CI: GitHub Actions.

## Completed in repository

- Mobile-first web application.
- PWA manifest and service worker.
- Report creation, search, filtering, maps, saved reports and sharing.
- Authentication integration.
- Privacy, terms and community guidance.
- GitHub Pages deployment workflow.
- Android/iOS Capacitor packaging workflow.
- Automated JSON and JavaScript syntax checks.

## Required before public production launch

### Supabase

The live Supabase project must be connected and its actual schema inspected before migrations are applied.

Required production work:

1. Confirm exact Reports and report_flags columns/types.
2. Add ownership/status/expiry fields only where appropriate.
3. Enable and test Row Level Security.
4. Add server-side report validation and rate limiting.
5. Create a private Supabase Storage bucket for report photos.
6. Replace base64 photo persistence with Storage object paths.
7. Add server-side expiry handling.
8. Add moderator roles and protected moderation actions.
9. Protect flag/moderation data from public reads.
10. Test anonymous and authenticated access separately.
11. Test Realtime permissions.
12. Configure Auth redirect URLs for the final public domain.

### Public web launch

1. Enable GitHub Pages for the repository using GitHub Actions as the source.
2. Confirm the first Pages workflow succeeds.
3. Test the published HTTPS URL on Android and desktop.
4. Test PWA installation from Chrome/Android.
5. Test offline shell, location permission, report submission and sharing.
6. Configure a custom domain if desired.

### Android / Google Play

The repository contains a Capacitor build workflow. A release AAB must be signed with the app's production keystore before Google Play submission.

Required owner actions:

- Create/confirm the Google Play Console developer account.
- Create the production signing key/keystore and store it securely.
- Configure GitHub Actions signing secrets.
- Create the Play Console app listing.
- Complete Data Safety, privacy policy, content declarations and store listing.
- Upload the signed AAB.
- Complete internal testing before production rollout.

### Apple App Store

The repository contains an iOS archive workflow, but App Store distribution requires Apple signing credentials.

Required owner actions:

- Apple Developer account.
- Bundle ID au.kerbsideconnect.app.
- Distribution certificate and provisioning profile.
- App Store Connect app record.
- Signing secrets/certificates configured securely.
- Privacy details, screenshots, metadata and review submission.

## Important

Do not mark this release as production-ready until the live Supabase security policies, Storage policies, server-side validation, expiry and moderation controls have been tested against the actual project.
