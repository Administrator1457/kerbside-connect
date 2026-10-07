# Security

## Kerbside Connect security approach

Kerbside Connect is designed to minimise unnecessary personal information and to keep public community reports separate from account ownership until the production database supports verified ownership.

### For users

- Do not include passwords, private contact details, house numbers, faces, vehicle registration plates or other sensitive information in reports.
- Do not upload photographs containing private or sensitive information.
- Do not attempt to bypass report expiry, moderation or access controls.
- Flag reports that appear unsafe, abusive, misleading or inappropriate.

### For security researchers

Please do not publicly disclose a suspected vulnerability before it has been assessed and addressed.

When reporting a security issue, include:

- A clear description of the issue
- The affected page or feature
- Steps to reproduce
- The potential impact
- Any relevant screenshots or logs that do not contain personal information

Do not include passwords, authentication tokens, private user data or other secrets in a report.

### Production security requirements

Before public launch, the project should have:

- Supabase Row Level Security verified against the actual live schema
- Server-side validation and rate limiting
- Secure Storage policies for report images
- Auth redirect URLs restricted to approved production origins
- Moderator/admin permissions enforced server-side
- Report ownership enforced server-side
- Server-side report expiry
- A tested moderation and abuse-response process

Never treat hidden frontend buttons or client-side checks as an access-control boundary.
