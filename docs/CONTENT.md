# Content Guide — Adding Meetings and Tasks

All content lives in `index.html`. Find each section by searching for the comments `/* ── MEETINGS ──` and `/* ── TASKS ──` (or `SEED_TASKS`).

---

## Adding a Meeting (from Granola)

Meetings are stored in the `MEETINGS` array near the top of the `<script>` block. **Newest entry must be first** — it becomes the default meeting shown on load.

### Structure of one meeting entry

```javascript
{
  id: 'meeting-YYYY-MM-DD',         // unique, kebab-case, used as a key
  date: 'DD Mon YYYY',              // display date e.g. '23 Jun 2026'
  title: 'Short Meeting Title',
  summary: 'One sentence summary shown in the meeting switcher list.',
  stats: [                          // 3–4 headline numbers or facts
    { label: 'GMV Target', value: '$50K' },
    { label: 'Timeline',   value: '3 Months' },
    { label: 'Creators',   value: '5' },
  ],
  blocks: [                         // colour-coded content blocks (2–6)
    {
      color: 'orange',              // 'orange' | 'amber' | 'green' | 'purple' | 'red' | 'blue'
      title: 'Block Heading',
      bullets: [
        'First key point from this section of the meeting.',
        'Second point.',
      ],
    },
    // ... more blocks
  ],
  flags: [                          // co-founder flags / open questions (optional)
    'Flag or open issue to resolve.',
    'Another flag.',
  ],
}
```

### Colour options for blocks

| Value | When to use |
|---|---|
| `orange` | Strategy, product decisions |
| `amber` | Brand, identity, design |
| `green` | Operational, workstream, action |
| `purple` | Tech stack, build |
| `red` | Risk, conflict, flag |
| `blue` | Research, market, external |

### Example — adding a new meeting

1. Open `index.html` in any editor
2. Find `const MEETINGS = [`
3. Insert your new entry **before** the first existing entry:

```javascript
const MEETINGS = [
  {
    id: 'meeting-2026-07-01',
    date: '1 Jul 2026',
    title: 'Pilot Kickoff — Bon Emil Stream #1',
    summary: 'First live pilot stream with Bon Emil. Recap, metrics, learnings.',
    stats: [
      { label: 'GMV', value: '$2.4K' },
      { label: 'Viewers Peak', value: '180' },
      { label: 'Conversions', value: '12' },
    ],
    blocks: [
      {
        color: 'green',
        title: 'Stream Results',
        bullets: [
          '$2,400 GMV in 45 minutes across 3 pinned products.',
          'Peak concurrent viewers: 180. Avg watch time: 22 min.',
        ],
      },
      {
        color: 'orange',
        title: 'What Worked',
        bullets: [
          'Scarcity drop at minute 30 converted 8 buyers in 4 minutes.',
          'Otto alert timing felt natural — no audience friction.',
        ],
      },
    ],
    flags: [
      'Stripe payout delay caused confusion for Bon Emil — fix before stream #2.',
    ],
  },
  // ... existing meetings below
```

4. Save, commit, and deploy.

---

## Adding or Editing Tasks

Tasks are stored in the `SEED_TASKS` array. On first load, or after a **Reset** on the taskboard, these are pushed to Supabase.

### Structure of one task

```javascript
{
  id:    't71',          // unique — increment from the last id in the array
  text:  'Task description goes here',
  col:   'todo',         // 'todo' | 'doing' | 'done'
  owner: 'All',          // 'All' | 'Arjun' | 'Josh' | 'Jamie' | 'Antonio'
  track: 'Track 02 · Build the Shopfront',  // which action plan track (or '')
  c:     '#FF5C00',      // card accent colour — use a brand hex (see BRAND.md)
},
```

### Task colour conventions

| Hex | Meaning |
|---|---|
| `#FF5C00` (orange) | Product / build |
| `#FFB800` (amber) | Brand / design |
| `#6BCB77` (green) | Ops / admin |
| `#A78BFA` (purple) | Tech infrastructure |
| `#F87171` (red) | Urgent / blockers |

### Adding new tasks

Find `const SEED_TASKS = [` in `index.html`. Append your new tasks at the end of the array before the closing `]`. Use the next available `id` (e.g. if the last task is `t70`, your new task is `t71`).

> **Important:** Existing board state in Supabase is not overwritten. New seed tasks with new ids are merged in automatically on next page load (or on Reset). If you want to see them immediately on everyone's boards, use the **Reset** button on the taskboard — note this wipes all custom moves/edits.

---

## Editing the Action Plan

The Action Plan is rendered by the `actionPlan()` function in `index.html`. Each track is a JavaScript object in the `tracks` array inside that function:

```javascript
{
  n:     '01',
  title: 'Lock Foundations',
  owner: 'All',
  time:  'Week 1',
  color: G,           // use color constants: G (green), O (orange), A (amber), P (purple), R (red)
  steps: [
    'Step one description.',
    'Step two description.',
  ],
},
```

Edit the steps array or add new tracks directly in the function. No separate data file — just save and deploy.
