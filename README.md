# Backcountry Athlete Tracker — PWA

**Dan Go High Performance Framework · 46 Outdoors Mountain-Strength Method**  
All 6 age groups · Phase-based periodization · Google Play / App Store-quality PWA

---

## What's In This Repo

| File | Purpose |
|------|---------|
| `index.html` | The entire app — all screens, all logic |
| `manifest.json` | Makes it installable as a home screen app |
| `sw.js` | Service worker — offline support |

---

## Before You Deploy — Edit These 3 Lines in index.html

Open `index.html` in any text editor. Find this block near the top and replace the placeholder values:

```js
const SUPABASE_URL  = 'YOUR_SUPABASE_URL';       // ← your project URL
const SUPABASE_KEY  = 'YOUR_SUPABASE_ANON_KEY';  // ← your anon/public key
const COACH_PIN     = '1234';                     // ← change to your PIN
```

---

## Step 1 — Supabase Setup (Free Database)

1. Create account at **supabase.com**
2. New Project → name it `bca-tracker`
3. Go to **SQL Editor → New Query** → paste and run:

```sql
create table athletes (
  id uuid default gen_random_uuid() primary key,
  nickname text unique not null,
  pin text not null,
  age int not null,
  age_group text not null,
  equip text not null,
  phase text not null,
  created_at timestamptz default now()
);

create table workout_logs (
  id uuid default gen_random_uuid() primary key,
  athlete_id uuid references athletes(id),
  nickname text,
  age_group text,
  phase text,
  day text,
  session text,
  exercises jsonb,
  notes text,
  equipment text,
  created_at timestamptz default now()
);

create table ruck_logs (
  id uuid default gen_random_uuid() primary key,
  athlete_id uuid references athletes(id),
  nickname text,
  age_group text,
  phase text,
  ruck_date date,
  duration int,
  distance numeric,
  pack_weight int,
  elevation int,
  avg_hr int,
  rpe int,
  notes text,
  created_at timestamptz default now()
);
```

4. Go to **Table Editor** → click each table → disable RLS (Row Level Security)
5. Go to **Settings → API** → copy your **Project URL** and **anon public key**

> **Already have the database set up?** Run this migration in SQL Editor to fix the `ruck_date` column type:
> ```sql
> ALTER TABLE ruck_logs ALTER COLUMN ruck_date TYPE date USING ruck_date::date;
> ```

---

## Step 2 — GitHub Pages Deployment

1. Create a new **public** GitHub repository named `bca-tracker`
2. Edit `index.html` with your Supabase credentials and coach PIN
3. Upload all 3 files: `index.html`, `manifest.json`, `sw.js`
4. Go to **Settings → Pages → Source: Deploy from branch → main → / (root) → Save**
5. Wait ~2 minutes. Your app is live at: `https://yourusername.github.io/bca-tracker`

---

## Step 3 — Share With Athletes

Send athletes the GitHub Pages URL. On their phone:
- **iPhone**: Open in Safari → Share button → **Add to Home Screen**
- **Android**: Open in Chrome → 3-dot menu → **Add to Home Screen**

It installs as an app icon. No App Store. No download.

---

## Athlete Flow

1. First open: enter nickname + age + equipment + phase → create 4-digit PIN
2. PIN is stored in Supabase — they can log in from **any device**
3. Log workouts (set-by-set reps + weight), rucks (full stats), view history
4. All data syncs to your Supabase database automatically

---

## Coach Dashboard

Access from any athlete's phone: **More → Coach Dashboard → enter coach PIN**

Shows:
- Total athletes, week's sessions, week's rucks
- Athletes by age group
- All logs filterable by type and age group
- Per-athlete phase and last active date

---

## Offline Behavior

- App loads and works fully offline (cached by service worker)
- Any logs saved while offline are queued locally
- Queue auto-syncs when connection returns
- Offline indicator shown at top of screen

---

## Updating the App

To push updates to all athletes:
1. Edit `index.html` locally
2. Upload the new version to GitHub (drag & drop in browser)
3. GitHub Pages re-deploys in ~2 minutes
4. Athletes get the update automatically on next open

---

## Free Tier Limits

| Service | Free Limit |
|---------|-----------|
| GitHub Pages | Unlimited |
| Supabase DB | 500MB storage, 50,000 MAU |
| Supabase API | 2 million requests/month |

For 100+ athletes this will never hit limits.
