# Macro — TODO

> **Legend** — priority `P0` critical · `P1` high · `P2` normal · `P3` low
> categories `security` `bug` `feature` `performance` `design` `docs` `testing` `infra` `research`
> owner `@me` (needs you — accounts, keys, money, judgement) · `@ai` (Claude can do this)

---

## v1 — current

- [ ] `P2` `testing` `@ai` Pin the regime score against known historical conditions. The weights are published and the components named, which makes the heuristic auditable — but nothing asserts that a 2008 or 2020 input produces the regime a reader would expect, so a weight change can drift the output with no test objecting.
- [ ] `P2` `bug` `@ai` Say when the cache is serving stale values. The six-hour TTL keeps the last good numbers on disk when FRED is unreachable, which is the right call — but a regime built from three-day-old data currently looks identical to a fresh one.
- [ ] `P2` `bug` `@ai` The `stale` flag means only "all six series empty" (`__init__.py:163`), no fetch time is stored, `series()` serves any-age cache on any error (`:102-105`), a missing series silently reweights the regime (`:199`), and a failed snapshot is held six hours with no retry (`:229-234`). Store `fetched_at` per series, set `stale` when any series is missing or past the TTL, retry a failed snapshot sooner, and pass `as_of`/`stale` to SONAR's UI and LLM read (`core.py:1391-1403`, `ui/app.py:4950-4978`). Source: SONAR `docs/audit/code-readiness-2026-11-18.md` M5 (extends the item above).
- [ ] `P3` `bug` `@ai` Small truths: `as_of` omits CPIAUCSL (`__init__.py:161`); the README calls `MacroCache` a disk cache (it is in-process; the disk cache is in `series()`); SONAR's `/api/macro` runs `get()` on a request thread and can block up to 6 x 25 s. Source: SONAR `docs/audit/code-readiness-2026-11-18.md` L6.
- [ ] `P3` `design` `@ai` Split fetching, caching and scoring out of the single `__init__.py`. Fine at its current size; worth doing before a seventh series or a second source arrives.
