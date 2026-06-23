# Supabase Setup

**Project ref:** `keywwvhzlgryfbqhbpim`  
**Dashboard:** https://supabase.com/dashboard/project/keywwvhzlgryfbqhbpim  
**SQL editor:** https://supabase.com/dashboard/project/keywwvhzlgryfbqhbpim/sql/new  
**Project URL:** `https://keywwvhzlgryfbqhbpim.supabase.co`

The anon key is embedded directly in `index.html`. It has `anon` role permissions only — safe to expose in client-side code. For admin access, use the dashboard.

---

## Tables

### `tasks` — Kanban taskboard

Shared across the whole team. Changes are broadcast via Postgres real-time so any open browser tab updates live.

```sql
create table if not exists public.tasks (
  id         text primary key,
  text       text not null,
  col        text not null default 'todo',     -- 'todo' | 'doing' | 'done'
  owner      text not null default 'All',      -- 'All' | 'Arjun' | 'Josh' | 'Jamie' | 'Antonio'
  track      text default '',                  -- e.g. 'Track 01 · Lock Foundations'
  c          text default '#FF5C00',           -- hex accent colour for the card
  position   integer default 0,               -- sort order within column
  created_at timestamptz default now()
);
```

### `waitlist` — Landing page signups

Collects name, email, and type (creator or brand) from the Landing Page draft.

```sql
create table if not exists public.waitlist (
  id         uuid primary key default gen_random_uuid(),
  name       text not null,
  email      text not null,
  type       text not null default 'creator',  -- 'creator' | 'brand'
  created_at timestamptz default now()
);
```

---

## Row Level Security (RLS)

Both tables have RLS enabled. Copy-paste each block into the SQL editor.

### `tasks` — full access for anon (read + write)

The taskboard is internal-only (shared via the Vercel URL, no public auth). Full anon access is intentional.

```sql
alter table public.tasks enable row level security;

create policy "public_access" on public.tasks
  for all to anon, authenticated
  using (true)
  with check (true);
```

### `waitlist` — insert only for anon, no reads

Protects signup data from being scraped via the anon key.

```sql
alter table public.waitlist enable row level security;

create policy "allow_insert" on public.waitlist
  for insert to anon
  with check (true);
```

To view waitlist entries, use the Supabase dashboard Table Editor — anon clients can't read back the data.

---

## Real-time

The `tasks` table must be added to the `supabase_realtime` publication for live sync to work.

```sql
alter publication supabase_realtime add table public.tasks;
```

Verify it's active:
```sql
select tablename
from pg_publication_tables
where pubname = 'supabase_realtime';
-- should include 'tasks'
```

---

## Full setup script (fresh project)

Run this once in the SQL editor to create everything from scratch:

```sql
-- Tasks table
create table if not exists public.tasks (
  id         text primary key,
  text       text not null,
  col        text not null default 'todo',
  owner      text not null default 'All',
  track      text default '',
  c          text default '#FF5C00',
  position   integer default 0,
  created_at timestamptz default now()
);
alter table public.tasks enable row level security;
create policy "public_access" on public.tasks
  for all to anon, authenticated
  using (true) with check (true);
alter publication supabase_realtime add table public.tasks;

-- Waitlist table
create table if not exists public.waitlist (
  id         uuid primary key default gen_random_uuid(),
  name       text not null,
  email      text not null,
  type       text not null default 'creator',
  created_at timestamptz default now()
);
alter table public.waitlist enable row level security;
create policy "allow_insert" on public.waitlist
  for insert to anon
  with check (true);
```

---

## How the taskboard seeding works

On first load, `loadTasks()` checks Supabase. If the table is empty, it inserts all `SEED_TASKS` from `index.html`. On subsequent loads, it merges any new seed tasks (by id) without overwriting existing board state. The merge is tracked via a localStorage key `revelry-seed-v3`.

To force a full reset from seed data, use the **Reset** button on the taskboard — it deletes all rows and re-inserts the seed set.
