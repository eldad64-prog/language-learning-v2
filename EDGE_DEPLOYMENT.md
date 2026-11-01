# Supabase Edge Function deployment

The Supabase project and `admin` Edge Function are already configured and deployed. Supabase injects its reserved `SUPABASE_URL`, `SUPABASE_ANON_KEY`, and `SUPABASE_SERVICE_ROLE_KEY` environment variables automatically; do not try to set them manually.

After the GitHub Pages site is live, set only the allowed custom secret:

```bash
supabase secrets set APP_ORIGIN="https://eldad64-prog.github.io"
```

Never commit a service-role key to GitHub or place it in browser code. Configure the first owner account out-of-band in Supabase SQL Editor using its verified auth UUID, as documented in `supabase-schema.sql`.

Set APP_ORIGIN to the exact HTTPS origin serving V2 (scheme and hostname, no trailing slash). The function rejects requests from every other browser origin; update this secret if the V2 host changes. The endpoint verifies the JWT using Supabase Auth and checks the immutable `app_metadata.role = owner_admin` claim before any privileged action. It supports owner-only user listing, approval/rejection/suspension, content draft editing, batch publication, and aggregate dashboard metrics. Review and test policies and rate limits before production use.


## Incremental achievements migration

After applying the main schema, apply `supabase-achievements-migration.sql` and `supabase-rate-limits-migration.sql` if those objects are not already present. The Edge Function evaluates and awards learner achievements from server-verified progress and test records; the browser cannot grant itself achievements. Persistent database-backed limits are set to 20 achievement sync requests and 60 admin actions per user per minute. The rate-limit RPC is executable only by `service_role`.
