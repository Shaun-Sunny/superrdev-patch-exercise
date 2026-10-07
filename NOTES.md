# NOTES

## Summary of changes
- **SQL precedence bug:** `AND`/`OR` without parentheses let archived tasks and wrong-status rows through the search. I parenthesised the two `LIKE` conditions in the repository query, `db/queries/search_tasks.sql` and both queries in the Oracle package, and added an `id DESC` tiebreak for stable paging.
- **Artificial delay:** removed a `Thread.sleep` in `TaskController` that made short or blank searches take up to 1s.
- **Input validation:** an invalid `status` now returns 400 instead of 500; `page` and `pageSize` are clamped (max 100).
- **Frontend:** the page resets to 1 when search or status changes; `useTasks` ignores stale responses, clears old errors and always resets `loading`; the error is shown before the loading state; search input is debounced (300ms).

## What I chose not to change
- Pagination still runs in memory after loading all matching rows. DB-level paging is the proper fix, but it is a bigger change than a focused patch.
- `%` and `_` in search input are not escaped, CORS is hardcoded to localhost, and I added no tests. I kept the diff small on purpose.

## Biggest remaining risk
In-memory pagination: every request loads all matching rows, so it will degrade as the table grows. There are also no tests, so a regression in the search query would go unnoticed. The Oracle changes are untested because I have no Oracle environment.

## Tools / AI used
I used Claude to help review the code and draft the patch, and asked it to explain each bug so I could write my handwritten notes myself. I ran the backend and frontend locally to test the changes.
