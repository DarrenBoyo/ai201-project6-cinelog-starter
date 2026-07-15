# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

---

## Comment 1 — Rename

**What I did:**

Renamed `save_to_watchlist()` to `add_to_watchlist()` to match the project's verb-to-noun naming convention. Updated the function definition and every import and function call that referenced the old name.

**How I verified:**

Used a project-wide search to confirm there were no remaining references to `save_to_watchlist()`. Verified that all references now use `add_to_watchlist()`.

---

## Comment 2 — Deduplication


**What I did:**

Added a duplicate-entry check to `add_to_watchlist()`. Before creating a new `WatchlistEntry`, the service queries for an existing entry with the same `user_id` and `film_id`. If one exists, it raises `AlreadyInWatchlistError` instead of creating another database record. I followed the same pattern used by `add_to_collection()`.

**How I verified:**

Reviewed the query to confirm that deduplication uses both `user_id` and `film_id`. I also confirmed that the duplicate check runs before the new entry is added and committed. A dedicated duplicate test will be added while addressing Comment 3.


---

## Comment 3 — Missing test

**What I did:**

**How I verified:**

---

## Comment 4 — Default visibility

**My position:**

**Reasoning:**

**Tradeoff acknowledged:**

---

## Comment 5 — Sort order

**My position:**

**Reasoning:**

**Engagement with reviewer's point:**

---

## Comment 6 — Rebase

**What conflicted:**

**How I resolved it:**

**How I verified no conflict remains:**

---

## PR Description

<!-- Written at the end — feature overview, design decisions, manual testing steps -->