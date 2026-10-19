# Career Compass MVP

A browser-based prototype for a personalized career preparation workspace.

## Run it
- Open `index.html` in a modern browser, or serve the folder with any static web server.
- No build step, external JavaScript library, analytics, or API is used.
- Data is saved in browser `localStorage` and survives refreshes/restarts in the same browser profile.
- Use **Privacy & Settings → Export backup** to download a JSON backup. Import can restore it.

## Included
- Workstream selector with multiple selections and custom target role text
- Home dashboard
- Study Tracker
- Prep Tracker
- Interview Tracker / opportunity pipeline
- Network contacts, leads and follow-up dates
- Career checklist
- Profile setup, search/filter, edit/delete, export/import

## Important storage and privacy limitations
This is a local-first prototype, not a hosted multi-user service:
- Data is not synced across devices or browsers.
- Clearing browser data, using private browsing, or browser storage eviction may remove data.
- Anyone who can access the same browser profile may be able to access this app's saved data.
- There is no authentication, cloud backup, team sharing, server-side access control, or formal privacy/compliance review.
- Do not enter highly sensitive personal, financial, medical, legal, or confidential employer data into this prototype.

## How to share the prototype
For a demo, host the folder on a static host such as Netlify, Vercel, or GitHub Pages. Anyone can open the public URL, but each person's data is stored in their own browser. A static hosted copy is not a shared database.

For a real public launch with retained user accounts and cross-device sync, implement a backend before inviting users to enter personal information.

## Recommended production architecture
- Frontend: Next.js or another maintained web framework.
- Authentication: Supabase Auth (email verification; optional OAuth; secure sessions).
- Database: PostgreSQL via Supabase.
- Authorization: Row Level Security (RLS) on every user-owned table. Derive ownership from authenticated identity, never from a client-supplied user ID.
- File storage: private buckets for resumes and documents, signed URLs, strict per-user policies, malware/file-type/size checks.
- AI features: call model providers only through a server-side endpoint; never expose provider secrets in frontend code. Send only necessary user-approved content and explain provider retention terms.
- Privacy: export and delete account/data, consent for AI processing, retention policy, least privilege, no public-by-default contacts or notes, secure backups, incident response and a privacy notice.
- Security: HTTPS, dependency updates, rate limits, CSRF/session protections as appropriate, input validation, server logs without sensitive document contents, secrets in deployment environment variables.

## Production data model starter
Suggested tables:
- `profiles` (user_id primary key, name, workstreams, target_roles, preferences)
- `tasks` (id, user_id, title, description, category, status, priority, due_at)
- `study_items` (id, user_id, title, skill, status, minutes, resource_url, notes)
- `prep_items` (id, user_id, title, category, status, due_at, notes)
- `companies` (id, user_id, name, research_notes)
- `applications` (id, user_id, company_id, role, job_url, stage, location, follow_up_at, notes)
- `interviews` (id, user_id, application_id, round, scheduled_at, notes, outcome)
- `contacts` (id, user_id, name, company, role, connection_type, profile_url, notes, follow_up_at)
- `interactions` (id, user_id, contact_id, occurred_at, summary, next_action)
- `documents` (id, user_id, storage_path, document_type, created_at)

Every user-owned table should have RLS enabled and policies that enforce `auth.uid() = user_id` for select/insert/update/delete. For linked records, policies must also validate ownership of referenced rows. Test policies using two separate user accounts before launch. Do not rely on hiding UI elements as a security control.

## Launch checklist
1. Create a project in Supabase and configure authentication.
2. Implement schema, indexes, RLS policies and private storage policies.
3. Connect the app through server/client SDKs with the correct public URL and anon key; never ship the service-role key.
4. Add account sign-up, sign-in, password recovery, session management and account deletion.
5. Add backup/restore strategy and test user isolation.
6. Publish privacy notice, terms and AI data-processing disclosures.
7. Deploy frontend to a host and configure environment variables, HTTPS, domain and monitoring.
8. Test with two accounts to verify no user can access another user's records or files.
9. Only then invite people to use it with real personal data.

This MVP is a useful product prototype, but it is not yet the production-secure, multi-user app requested. The cloud/authentication work above is required for that.
