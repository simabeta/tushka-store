# Tushka isolated deployment notes

- The live Supabase project uses the dedicated `tushka` schema. Existing application tables in `public` were not modified.
- The Data API schema list preserves `public` and `graphql_public` and adds `tushka`; `storage` remains managed through the Supabase Storage API.
- All Tushka database requests use `.schema('tushka')`. Auth signups include `app: 'tushka'`, and the Auth trigger ignores signups from other apps.
- Tushka Storage uses dedicated buckets `tushka-product-images`, `tushka-site-banners`, and `tushka-brand-assets`. Existing bucket IDs and policies were not edited.
- `.env`, local database files, `node_modules`, `.next`, and `.git` are excluded from this source package. Configure `NEXT_PUBLIC_SUPABASE_URL` and `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY` in the deployment environment. Never use a service-role key in browser code.
- The live database migrations were applied through Supabase and are recorded with the version IDs in the filenames below. Do not rename these migration files after deploying.
- Seed data currently includes 4 categories, 12 products, 12 image rows, 3 banners, 3 static pages, 5 sample reviews, and store settings. Replace sample contact information and reviews before public launch.
- No admin user has been promoted. After signing up with the Tushka site, use `supabase/admin-setup.sql.example` with your own verified account to grant the first store-admin role.
