# Best-Practice: Repo-Cleanup-Konvention fuer AI-Agent-Projekte

**Erstellt:** 2026-05-13 (Tag 17, F4.1 Vorzieh-Option)
**Schicht:** 1 (Anthropic-Kontext) + 2 (Industry-Sources) + 3 (Gegenstimmen)
**Coverage:** operativ-detailliert
**F-Schritt-Relevanz:** F4.1 (Cleanup-Konvention + Root-Hygiene + Backup-Routine)

---

## 1. Root-Level-Konvention (Industry-Standard)

**Konsens aus 5 Quellen (GitHub Community 2025, Medium Code-Factory-Berlin, acompiler 2025, seylox AI-Agent-Meta-Repo 2026, DocTemplate-Archiv-System 2026):**

Root-Verzeichnis enthaelt NUR:
- Framework-Config-Files: .gitignore, README.md, LICENSE
- Authoritative-State-Files: AGENTS.md, CLAUDE.md, COORDINATION.md (aktive Session-Layer)
- Append-only-Logs: STOLPERSTEINE.md, session_log.md (Projekt-Authority)
- Aktive HANDOVER-Files: letzte 2-3 (Session-Anker)
- Vision-File: UNIVERSAL_NORDSTERN.md oder equivalent

Alles andere gehoert in Unterverzeichnisse. Root als "Landing-Zone fuer AI-Agents" - nur was jede neue Session sofort braucht.

## 2. Archiv-Mechanik (DocTemplate-Archiv-System + seylox Meta-Repo 2026)

**Archiv-Trigger (aus DocTemplate-System):**
- Superseded Content: neue Doku ersetzt alte (→ archive/)
- Completed Phase: phasen-spezifische Docs nach Phase-Abschluss (→ archive/)
- Project Pivot: fundamentaler Richtungswechsel macht Docs obsolet (→ archive/)
- Pre-Format-Files: altes HANDOVER-Format, alte SESSION_START-Files (→ archive/)

**Archiv-Sentinel-Pattern (DocTemplate + AI-Agent-Best-Practice):**
Datei archive/README.md mit explizitem AI-Ignore-Directive:
"AI agents should ignore this directory and not use its contents unless explicitly instructed."

**Goldene Regel:** NIEMALS loeschen - nur archivieren. Git-History ist einziger Loeschweg.

## 3. HANDOVER-Retention-Konvention

**Empfehlung (seylox AI-Agent-Meta-Repo 2026 + eigene Empirie):**
- Aktiv im Root: letzte 2-3 HANDOVER-Files (aktueller Session-Anker)
- Aelter als 14 Tage: → archive/handovers/
- Sonderregel: HANDOVER-Files mit anderer Namenskonvention (pre-Standard) sofort archivieren

CoveLab-spezifisch: HANDOVER_YYYYMMDDTHHMMSSZ_CTO.md ist Standard-Format seit Tag 7. Aeltere Formate (HANDOVER_20260420_CTO, SESSION_HANDOVER_*, DAILY_HANDOVER_*, REENTRY_*) alle archivieren.

## 4. Backup-Bereinigungs-Routine

**Best-Practice (DoHost Agentic Cleanup 2026 + Dina Berry GitHub Cleanup 2025):**
- Retention-Fenster: 10 neueste Backups behalten
- Aeltere: loeschen (Backups sind lokal-reproduzierbar, kein Informationsverlust)
- Skript: tools/maintenance/cleanup_backups.sh (Threshold-basiert)
- Frequenz: manuell vor Phase-Wechseln (nicht automatisch - CEO-Kontrolle behalten)

## 5. .gitignore-Pflege-Konvention

**Standard (git best-practices 2025, 5 Quellen konsistent):**
- Blanket-Exclusions fuer Binary/Large/Secret-Files
- Exceptions via !-Pattern fuer spezifische tracked Files
- Niemals aktive Artefakt-Verzeichnisse blanket excluden wenn sie tracked werden sollen
- CC-Stolperstein-Klasse: reports/-Blanket-Exclusion war bekanntes Anti-Pattern → gefixt 2026-05-13

## 6. Gegenstimmen / Caveats

**"Start Small" (seylox 2026):** Erste Iteration hatte zu viel Dokumentation - falscher Anreiz zu cleanen bevor genug aktive Files unterscheidbar. CoveLab-Gegenargument: Pre-Pivot-Files sind klar identifizierbar (Datum vor 2026-05-10, ADR-0011-Pivot). Cleanup hier ist Post-Pivot, nicht Pre-Start.

**"Documentation drift is real" (seylox 2026):** Archivierte Files koennen via Git-History zurueckgeholt werden - kein Informationsverlust-Risiko. R7-Prio-1 (kein Informationsverlust) via Git-History-Garantie erfuellt.

**Archive ist "underused but worth keeping" (seylox 2026):** Empfehlung archive/ klein halten, nicht als Dumping-Grounds verwenden. CoveLab: archive/ existiert bereits (in .gitignore), Sentinel-README fehlt noch.

## 7. CoveLab-Root-Allowlist (post-F4.1)

Root-Files die bleiben:
- UNIVERSAL_NORDSTERN.md (Vision + Lock)
- AGENTS.md (Universal-Framework)
- CLAUDE.md (Projekt-Layer)
- COORDINATION.md (Live-State)
- STOLPERSTEINE.md (Lessons)
- session_log.md (Tagebuch)
- HANDOVER_*.md (letzte 3 aktive)
- NORDSTERN.md (Pointer-Stub, 322B, behalten als Fallback)
- .gitignore
- README.md (falls vorhanden - CoveLab: fehlt noch, F5.1-Target)

Root-Files die zu archive/ wandern (alle anderen):
- Pre-Pivot-HANDOVERs (vor 2026-05-08)
- SESSION_HANDOVER_*.md, DAILY_HANDOVER_*.md, REENTRY_*.txt
- STACK_AUDIT_*.md, F6_INSPEKTION_*.md, SYSTEM_CORE.md, F23_INSPEKTION_*.md
- CLAUDE.md.bak*, NORDSTERN.md.bak*, HANDOVER_2026_04_14.md
- PROJECT_STATUS.md, SYSTEM_B_STATUS.md, CTO_STATE.md
- DECISIONS.md, RULES.md, AWIN_PARTNER_STATUS.md, CHANGELOG.md
- f40_*.json, f40_*.txt, f40_*.log, f40_*.md (Pre-Pivot-Repair-Artefakte Root-Level)
- deploy_phase1b.sh, crontab_backup_*.txt, clickup_import_backlog_*.csv
- PLAN_*.md, BESCHLUSS_*.md, TAGESENDE_*.md, SESSION_START_*.md
- ROADMAP.json (SUPERSEDED durch FAHRPLAN)
- requirements*.txt, requirements*.lock (Pre-Pivot Python-Deps)
- FUNDAMENT_PLAN.md (falls noch vorhanden, SUPERSEDED)
- F36_ANALYSE_*.md, F37_HEADER_BLOCKS_*.md, F39_IMAGE_AUDIT_*, F40_CONTENT_ANALYSIS_*, F40_IMAGE_DOWNLOAD_*.log

## References

- GitHub Community Discussion #173482 (2025) - Root-Hygiene
- Medium Code-Factory-Berlin (2021) - Folder-Konvention
- seylox.github.io 2026-03-05 - AI-Agent-Meta-Repo-Pattern
- deepwiki DocTemplate 7.4 (2026) - Archiv-Mechanik + Sentinel
- DoHost Agentic Cleanup 2026 - Backup-Routine
- Dina Berry GitHub Cleanup 2025 - dry-run + apply Pattern
