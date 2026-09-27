# Marketing Team Manager

A production-oriented internal SaaS workspace for marketing agencies: tasks, assignments, statuses, deadlines, shoot days, workload, objective performance metrics, activity history, notifications, and a responsive calendar.

## Stack

- React + Vite + JavaScript
- Tailwind CSS
- Supabase Auth + PostgreSQL + Row Level Security
- React Router
- Lucide React
- Recharts

## Requirements

- Node.js 18+ (Node 20+ recommended)
- A Supabase project

## Installation

```bash
npm install
cp .env.example .env.local
```

Set:

```env
VITE_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
VITE_SUPABASE_ANON_KEY=YOUR_SUPABASE_PUBLISHABLE_OR_ANON_KEY
```

Never put a Supabase service-role key in the frontend.

## Supabase setup

1. Open your Supabase project.
2. Open **SQL Editor**.
3. Run `supabase/schema.sql` in one go.
4. In **Authentication → Providers**, keep Email enabled.
5. Create your users from **Authentication → Users**.
6. The database trigger creates a `profiles` row automatically for each new Auth user.
7. Promote the first manager by running an update such as:

```sql
update public.profiles
set role = 'admin', full_name = 'Zeyad', job_title = 'Marketing Manager'
where email = 'zeyad@example.com';
```

## Demo team

The intended demo people are:

- Zeyad — Marketing Manager — admin
- Ahmed — Graphic Designer — member
- Mohamed — Video Editor — member
- Sara — Content Creator — member

Create those four Auth users first, then use `supabase/seed.sql` as a template for profile updates and sample shoot days.

Sample tasks:

- Instagram Campaign Design
- Edit Product Reel
- Write Reel Script
- Create Offer Post
- Prepare Shoot Equipment

## Running locally

```bash
npm run dev
```

Then open the Vite URL shown in the terminal.

## Production build

```bash
npm run build
npm run preview
```

## Permissions

Frontend route guards are for UX only. Database authorization is enforced by Supabase RLS. Admins have full task/shoot/team/activity access. Members can read assigned tasks, update permitted fields/status on assigned tasks, read shoot days, read their own profile, and read their own activity/notifications.

The member task-update trigger prevents reassignment, creator changes, priority changes, deadline changes, and title changes by non-admin users.

## Notifications

The schema includes a notifications table and the header displays an unread badge. To generate notifications automatically for your exact operational rules, add server-side/Edge Function jobs that create rows for assignments, approaching deadlines, overdue tasks, and status changes. The UI is already wired to receive realtime notification table changes.

## Architecture

```text
src/
  components/   reusable UI, route guards, layout, task/shoot components
  context/      authentication/session state
  hooks/        tasks, team, shoot-day data hooks
  lib/          Supabase client
  pages/        application routes
  utils/        permissions and formatting
  App.jsx
  main.jsx
supabase/
  schema.sql
  seed.sql
```

## First-admin workflow

Because Supabase Auth owns identities, do not hard-code an admin user into frontend code. Create the Auth user, let the profile trigger run, then promote the profile to `admin` from SQL Editor or an admin-only backend workflow.

## Error handling

User-facing forms avoid exposing raw database errors. Failed operations leave the current UI intact and show a friendly error state. For production observability, add your preferred server-side logging/monitoring without exposing secrets to the browser.
