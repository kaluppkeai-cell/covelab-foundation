# CLAUDE.md — Your Project Layer (Template)

> **This is the empty project-layer template.** It shows how you'd describe *your* project to your AI assistant.
> The Universal-Framework it plugs into (the rulebook, the running components, the full method guide) ships with the paid CoveLab Foundation product. This template is free so you can see the structure.
> **Usage:** copy this file to your repo-root as `CLAUDE.md` and fill in the placeholders.

---

## What goes here

This file holds **your** project-specific details — infrastructure, accounts, project-only constraints. The general framework rules live in the package's framework file and apply automatically; you do not repeat them here.

Your strategic anchor (vision + the locked objective) lives in your `PROJECT_NORDSTERN.md`; your phase plan lives in your `FAHRPLAN.md`.

---

## Project Identity

**Project name:** _<your project name>_
**Domain / problem:** _<one sentence — what does your project do?>_
**Founder / CEO:** _<your name, location, primary language>_
**Stage:** _<early / MVP / live / scaling>_

---

## Tech Environment

Replace the placeholders with your real values.

- **VPS / Cloud:** _<provider, host alias, region>_
- **Database:** _<container name, db name, user>_
- **Reverse proxy / TLS:** _<Caddy / nginx / managed>_
- **Code-Repo:** _<github.com/your-org/your-project>_
- **Account split (optional):** _<billing account / tech account>_

---

## Standard Commands

Adapt to your host alias and paths.

```bash
# Standard remote command
ssh <your-host-alias> "cd /opt/your-project && <command>"

# Database backup (good habit before ending a work session)
ssh <your-host-alias> "docker exec <db-container> pg_dump -U <db-user> <db-name> > /opt/your-project/backups/db_$(date +%Y%m%d_%H%M%S).sql"
```

---

## Project-Specific Rules (your absolute rules)

Non-negotiable rules unique to your project. These apply only here, on top of the framework.

### ABSOLUTE-RULE-XXX: _<short name>_
**Set by:** _<who, when>_
**Rule:** _<exact wording>_
**Effect:** _<what the system enforces>_

---

## Project-Specific Notes

- **Patterns:** document project-only patterns as you discover them (problem, context, solution).
- **Stumbling blocks:** keep your own `STOLPERSTEINE.md` (see the template) — it compounds over time.

---

*This file is yours. Update it freely as your project evolves.*
