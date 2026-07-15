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

Created `tests/test_watchlist.py` and added
`test_add_to_watchlist_nonexistent_film_raises()`. I used
`test_add_to_collection_nonexistent_film_raises()` from
`tests/test_collection.py` as the model and followed the same in-memory app
fixture, sample-user fixture, application-context structure, and
`pytest.raises()` assertion pattern.

The watchlist branch still uses integer film IDs, so the test uses a
nonexistent integer ID rather than the UUID value used by the collection test.

**How I verified:**

Ran `pytest tests/test_watchlist.py -v` and confirmed the new test passed. Then
ran `pytest tests/ -v` to confirm the full test suite still passed after the
rename, deduplication, and missing-test changes.


---

## Comment 4 — Default visibility

**My position:**

I would change the default from `public=True` to `public=False`.

**Reasoning:**

A watchlist reflects a user’s personal interests and future viewing plans, so I think privacy should be the safer default. Users may add films casually without realizing that the entry is visible to others. Defaulting to private reduces the chance of accidental sharing and gives users explicit control over when they make an item public.

This choice optimizes for user trust and informed consent. A user can still choose to make a watchlist entry public, but that action should be intentional rather than automatic.

**Tradeoff acknowledged:**

The tradeoff is that a private default may reduce social engagement and make the feature feel less community-oriented. A `public=True` default could encourage discovery, recommendations, and interaction between users with less friction. However, I believe requiring an explicit opt-in for visibility is a better balance because it avoids exposing user activity unexpectedly.


---

## Comment 5 — Sort order

**My position:**

I would change the watchlist sort order from alphabetical by film title to date added, with the most recently added film appearing first.

**Reasoning:**

A watchlist is primarily a queue of films the user intends to watch. In that context, the most recently added items are often the most relevant because they reflect the user’s latest interests. Sorting by `date_added` descending also matches the behavior of `get_collection()`, which makes the two features more consistent and predictable.

Alphabetical sorting is useful when a user already knows the title they are looking for, but it removes the history of when items were saved. For a growing watchlist, users are more likely to want quick access to films they added recently than to scan the list alphabetically.

**Engagement with reviewer's point:**

I agree with the maintainer’s reasoning that date-added order better matches how users interact with a watchlist. The feature represents saved intent rather than a static catalog, so recency provides more meaningful ordering than title. Alphabetical sorting could still be offered later as an optional filter or sort control, but I would use newest-first as the default.

---

## Comment 6 — Rebase

**What conflicted:**

After running `git fetch origin` and `git rebase origin/main`, Git reported a conflict because the `main` branch had already migrated film IDs from integers to UUIDs while my feature branch still referenced the older integer-based implementation. During the rebase, I also encountered a conflict in `.gitignore`.

**How I resolved it:**

I resolved the `.gitignore` conflict by keeping the required ignore rules. I then updated the watchlist implementation to match the UUID-based models from `main`. This included restoring the `WatchlistEntry` model, changing `film_id` references from integer IDs to UUID strings, updating relationships in `models.py`, and modifying the watchlist test to use a UUID value for the nonexistent film instead of an integer.

**How I verified no conflict remains:**

I searched the project to ensure no watchlist code still referenced integer film IDs, confirmed there were no remaining Git conflict markers, ran the full test suite with `pytest tests/ -v`, and verified that the rebased branch contains no merge commits.
---

## PR Description

<!-- Written at the end — feature overview, design decisions, manual testing steps -->