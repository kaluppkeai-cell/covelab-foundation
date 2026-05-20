# STOLPERSTEINE.md — Stumbling-Block Log (Template)

> **This is an empty template.** It shows the *format* of the stumbling-block log.
> The real database — 70+ documented pitfalls with their fixes, accumulated over 45 days of production use — ships with the paid CoveLab Foundation product (Core / Pro).

---

## What this is

An append-only log of every pitfall you hit, so you (and your AI assistant) never lose a day to the same mistake twice. The point is that it **compounds**: the more you log, the less error-prone your project becomes over time.

Each entry gets a short code (A, B, C, … AA, AB, …) and three things:

- **Cause** — what actually went wrong
- **Fix-recipe** — the concrete rule that prevents it next time
- **(optional) Class** — group similar pitfalls so patterns emerge

---

## Format

```
### A — <one-line summary>
**Cause:** <what went wrong, concretely>
**Fix:** <the rule / habit that prevents it next time>
**Class:** <optional grouping tag>
```

---

## Example entry (illustrative only)

### A — Server file-time is not the real date
**Cause:** Relying on a file's modified-time as if it were the real creation date; the two diverged and a timestamped filename was wrong.
**Fix:** Always derive timestamps from a real wall-clock source, never from file metadata.
**Class:** time/dates

---

*Your entries start below. Add a new block each time you learn something the hard way.*
