# Greece & Türkiye 2026 — Trip Tracker

A shared travel itinerary web app for the Greece & Türkiye summer 2026 trip.  
Live at **[greece2026.brockspot.com](https://greece2026.brockspot.com)**

---

## Stack

| Layer | Service | Purpose |
|---|---|---|
| Frontend | Single HTML file | No framework, no build step |
| Hosting | Netlify via GitHub | Auto-deploys on push to `main` |
| Database | Supabase (PostgreSQL) | Persists all trip data in real time |
| DNS | Namecheap → brockspot.com | CNAME `greece2026` → Netlify |

---

## How it works

All trip data lives in a single `itinerary` table in Supabase as one JSON blob (`id = 'trip'`). The frontend reads on load and patches on every edit with a 1.2s debounce. No login required — anyone with the URL can view and edit. Changes sync across all devices within seconds.

```
GitHub (index.html)
  ↓ auto-deploy on push
Netlify (greece2026.brockspot.com)
  ↓ reads/writes on every edit
Supabase (itinerary table, id = 'trip')
```

---

## Supabase setup

Table was created with:

```sql
CREATE TABLE itinerary (
  id TEXT PRIMARY KEY,
  data JSONB NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

INSERT INTO itinerary (id, data) VALUES ('trip', '{}');
```

---

## Features

- **Timeline view** — full trip from RDU departure to RDU return, displayed as a vertical timeline
- **Live sync** — edits save automatically to Supabase and appear for all travelers in real time
- **Add items** — insert a new flight, stay, or transit leg anywhere in the timeline, including between existing items
- **Delete items** — any leg (including base legs) can be removed via the edit panel; base legs are flagged hidden rather than deleted so they can be recovered by clearing `_hidden` in Supabase
- **Edit dates** — all legs have editable start and end dates that override the defaults
- **Missing end date warning** — Stay cards with no check-out date show a persistent red warning pill next to the status
- **Activities & plans** — sub-items on any leg for tours, restaurants, reservations, excursions; each takes name, address, date (validated within parent stay range), start/end time, confirmation number, notes
- **Status tracking** — each leg is marked confirmed or to book; a running "to book" count lives in the header
- **Field editing** — flight numbers, departure times, confirmation numbers, addresses, notes all editable per leg
- **Mediterranean theme** — Aegean beach background photo that rotates daily (6 photos, day-of-year based so all users see the same one); frosted glass cards; Aegean navy, aqua, and terracotta palette
- **Mobile-first** — modals open as bottom sheets on mobile; all tap targets minimum 44px; safe area inset support for notched phones

---

## Background photos

Six Unsplash photos rotate on a daily cycle (same photo for all users on a given day):

1. Navagio / Shipwreck Beach, Zakynthos
2. Myrtos Beach, Kefalonia
3. Santorini caldera & blue domes
4. Aegean crystal shallows (overhead)
5. Ölüdeniz Blue Lagoon, Turkey
6. Greek island beach & hillside

Photos load via Unsplash CDN. The page starts with the photo faded out and crossfades it in once loaded.

---

## Deployment

Netlify watches the `main` branch. To update the app, edit `index.html` and push:

```bash
git add index.html
git commit -m "your message"
git push
```

Netlify deploys in ~30 seconds. No build commands, no `package.json`.

---

## Itinerary — base legs

| # | Leg | Date | Status |
|---|---|---|---|
| 1 | RDU → JFK | May 25 | to book |
| 2 | JFK → ATH | May 25 (arr. May 26) | confirmed |
| 3 | Athens — Grand Hyatt (2 rooms) | May 26–29 | confirmed |
| 4 | Athens → Naxos (ferry or flight) | May 29 | to book |
| 5 | Naxos — VRBO | May 29 – Jun 3 | confirmed |
| 6 | Naxos → Thessaloniki (ferry + flight) | Jun 3 | to book |
| 7 | Thessaloniki — VRBO | Jun 3–5 (possibly Jun 6) | confirmed |
| 8 | Thessaloniki → Istanbul (Turkish Air via United) | Jun 6 | confirmed |
| 9 | Istanbul — accommodation | Jun 6–8/9 | to book |
| 10 | Istanbul → Athens | Jun 8 or 9 | to book |
| 11 | Athens → JFK | Jun 9 | confirmed |
| 12 | JFK → RDU | Jun 9 | confirmed |

---

## Changelog

### v1.4 — Background rotation & README
- Replaced static background with 6 curated Aegean beach photos
- Daily rotation (day-of-year based) — all users see the same photo each day
- Smooth crossfade on page load
- Added this README

### v1.3 — Date editing & stay warnings
- Added editable start/end date fields to every leg's edit panel
- Date overrides persist to Supabase and update the card label in real time
- Stay cards with no end date show a persistent red "no check-out date" warning pill
- Activity date validation respects overridden parent dates
- Fixed RDU → JFK departure to May 25; JFK → ATH updated to reflect overnight flight

### v1.2 — Mediterranean theme + mobile
- Full redesign with Aegean beach photo background and frosted glass cards
- Palette: Aegean navy, aqua, terracotta, warm sand
- Mobile-first: bottom sheet modals, 44px tap targets, safe area insets
- Delete added to all legs (base legs use hidden flag, custom legs fully removed)
- Activities edit and delete buttons always visible

### v1.1 — Activities, insert anywhere, delete
- Activities & plans sub-items on every leg (name, address, date, time, confirmation, notes)
- Insert new legs between any two existing items or at the top/bottom
- Delete custom legs; base legs had hover-only remove (superseded by v1.2)
- Insert zones always visible on touch devices
- Both VRBO stays corrected from Airbnb

### v1.0 — Initial build
- Supabase-backed itinerary tracker with real-time sync
- 12 base legs from RDU → ATH → NAX → SKG → IST → RDU
- Per-leg field editing, status tracking, notes
- Confirmed/to book status with running header count
- Netlify + GitHub + Supabase architecture
- Connected to greece2026.brockspot.com via Namecheap CNAME
