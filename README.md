# cose-di-bori

# Cose di Bori — Personal Life OS

A small personal system to manage tasks, expenses, workouts, and your ideal week — all in a single HTML file, no backend, hosted for free on GitHub Pages and synced across devices via [Supabase](https://supabase.com).

Every person who uses it has their **own** Supabase project, completely separate from mine: no data is ever shared between people who fork this.

## What it does

- **Tasks** — four sections (Today, Tomorrow, Backlog, Scheduled), drag & drop (works on phones too), priorities, automatic recognition of dates written in plain text ("friday at 4pm", "September 20th"...), automatic promotion of overdue tasks, importing whole lists at once, a calendar of completed tasks.
- **Projects** — bigger checklists with a progress bar, with the option to "push" a single item into the Task manager (Today/Tomorrow) while the project stays the source of overall progress.
- **Expenses** — quick 4-field entry (category, how much, when, what), natural date recognition ("yesterday", "3 days ago", "09/20"...), recurring subscriptions calculated automatically month by month, a pie chart by category, a privacy mode to hide amounts with one tap.
- **Workouts** — gym/running plan with weight progression, rest timer, progress chart.
- **Ideal week** — hourly drag & drop planning, color-coded blocks by category.

## How it works

A single static `index.html` file. No server: the browser talks directly to Supabase (database + authentication). Data also stays in `localStorage` as an immediate local copy, and syncs to Supabase in the background — so the app still works offline and updates itself across all your devices after logging in.

## How to set it up for yourself

You'll need a free Supabase account (takes a few minutes) and a GitHub account.

### 1. Create your Supabase project
Go to [supabase.com](https://supabase.com), create a free account and a new project.

### 2. Create the tables
In your project, open **SQL Editor** → New query, paste and run:

```sql
create table if not exists planner_state (
  id text primary key, data jsonb not null, updated_at timestamptz default now()
);
alter table planner_state enable row level security;
create policy "authenticated only" on planner_state for all to authenticated using (true) with check (true);

create table if not exists allenamento (
  key text primary key, value text, updated_at timestamptz default now()
);
alter table allenamento enable row level security;
create policy "authenticated only" on allenamento for all to authenticated using (true) with check (true);

create table if not exists tasks_state (
  id text primary key, data jsonb not null, updated_at timestamptz default now()
);
alter table tasks_state enable row level security;
create policy "authenticated only" on tasks_state for all to authenticated using (true) with check (true);

create table if not exists expenses_state (
  id text primary key, data jsonb not null, updated_at timestamptz default now()
);
alter table expenses_state enable row level security;
create policy "authenticated only" on expenses_state for all to authenticated using (true) with check (true);

create table if not exists subscriptions_state (
  id text primary key, data jsonb not null, updated_at timestamptz default now()
);
alter table subscriptions_state enable row level security;
create policy "authenticated only" on subscriptions_state for all to authenticated using (true) with check (true);
```

### 3. Lock down public sign-ups (important)
**Authentication → Sign In / Providers**, under "User Signups":
- turn off **"Allow new users to sign up"**
- turn off **"Allow anonymous sign-ins"**

Without this step, anyone who finds the app's public key (visible in the code — this is normal and actually necessary for the app to work) could create an account and access your data. With sign-ups closed, the only account that can ever exist is the one you create in the next step.

### 4. Create your user
**Authentication → Users → Add user** → enter your email and a password. This will be the only account you use to log into the app.

### 5. Connect the file to your Supabase project
Open `index.html` in a text editor and search (Ctrl+F) for `SUPABASE_URL` and `SUPABASE_ANON_KEY` (or `SUPABASE_KEY`) — they appear in two places in the file. Replace them with the values from **your** project, found under **Project Settings → API**:
- `SUPABASE_URL` → "Project URL"
- `SUPABASE_ANON_KEY` / `SUPABASE_KEY` → "anon public" key

### 6. Publish on GitHub Pages
- Create a public repository on GitHub
- Upload `index.html` to the root of the repository
- **Settings → Pages** → Source: "Deploy from a branch", Branch: `main`, folder `/ (root)` → Save
- Wait a couple of minutes: the link will appear on that same page

### 7. Log in
Open the published link, enter the email and password created in step 4. Done — from then on the session stays saved in each device's browser after the first login.

## About security

The Supabase key visible in the code is public out of necessity (the app runs entirely in the browser, with no server), but **by itself it's not enough to access anything**: without authentication, the database rules (Row Level Security) block every read and write. With sign-ups closed (step 3), the only way to authenticate is with your account's email and password — which no one else can create. Still, treat it like a real password: don't lose it, don't share it.

## Known limitations
- A personal, "homemade" project — no guarantees, no formal support
- If you edit the same data from two different devices at the same instant, the last save wins (no smart merging)
- Some sections (Expenses, Projects) are newer and less polished than others

## If you find it useful

If this project came in handy and you'd like to buy me a coffee: [paypal.me/elboreees](https://paypal.me/elboreees) — no obligation, of course.

## License

Do whatever you want with it — copy it, modify it, use it for your own purposes. No guarantees it'll work.
