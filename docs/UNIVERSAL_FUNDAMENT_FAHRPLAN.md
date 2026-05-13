# UNIVERSAL_FUNDAMENT_FAHRPLAN

**Erstellt:** 2026-05-10 (Tag 13 Spaetnachmittag)
**Authority:** ADR 0011 (Strategie-Pivot, Commit 0a35d66) + ADR 0007 (Fundament-Lock-Mechanik)
**Lock-Status:** ACTIVE — keine Verkaufs-Verpackung-Arbeit vor Phase 1-4 alle pass; keine Cashflow-Arbeit bis Phase 5 pass

## Vision
Universal-Fundament fuer KI-gestuetzte Projektarbeit als wiederverwendbares Repo-Template. Verkauf als Asset (Upwork, SkillsMP, Agensi, Lizenz) ist die neue Cashflow-Strategie post-Phase-5. CoveLab dient als erstes Anwender-Projekt und Demo-Implementierung.

## Beziehung zu FUNDAMENT_PLAN.md (alt)
FUNDAMENT_PLAN.md (F1-F10) bleibt operativ als historischer Anker und CoveLab-spezifischer Lock. Dieser Fahrplan ersetzt ihn als primaerer Plan-Anker. F8 / F9 / F10 aus FUNDAMENT_PLAN.md werden in Phase 2 verlagert (mit neuer Nummerierung F2.1-F2.3). F7 entfaellt (war n8n-MCP-Setup, projekt-spezifisch, gehoert in Projekt-Layer post-Phase-3).

## Pflicht-Regel
Jede CTO-Session beginnt mit Pruefung dieser Datei. Phasen-Gate: keine Phase-N+1-Arbeit bis Phase-N alle F-Schritte pass. CTO-Self-Check-Punkt-2 (F-Schritt vs M-Schritt) wird erweitert um Phase-Pruefung.

## Update-Protokoll
Code-Chat updated F-Schritt-Status nur mit evidence_required-Belegung. CTO verifiziert via evidence (MCP-Read). Kein self-marked pass.

---

## PHASE 0: Strategie-Pivot dokumentieren (1.5-2.5h, Tag 13 EOD + Tag 14 Vormittag)

### F0.1: ADR 0011 Strategie-Pivot
- **Status:** pass
- **Completed:** 2026-05-10
- **Evidence:** Commit 0a35d66 decisions/0011-strategie-pivot-universal-fundament.md

### F0.2: NORDSTERN.md zu UNIVERSAL_NORDSTERN.md umbenennen + Vision aktualisieren
- **Status:** pass
- **Completed:** 2026-05-11
- **Scope:** Datei umbenennen, alten Cashflow-Inhalt durch Universal-Fundament-Vision ersetzen, Phasen P0-P5 + qualitative Pass-Bedingungen statt fixer Termin
- **Evidence Required:** UNIVERSAL_NORDSTERN.md committed mit neuer Vision, alter NORDSTERN.md geloescht oder weitergeleitet
- **Effort:** 1h
- **Depends:** F0.1

### F0.3: ADRs 0001-0003 supersede-Pruefung committen
- **Status:** pass
- **Completed:** 2026-05-11
- **Scope:** Status-Update in ADRs 0001-0003 mit "supersede"-Markierung + Verweis auf ADR 0011. Kein Inhalt-Refactor, nur Status-Header.
- **Evidence Required:** 3 ADRs mit aktualisiertem Status-Header committed
- **Effort:** 30min
- **Depends:** F0.1

---

## PHASE 1: Industry-Standard-Konvergenz (8.5-12h, Tag 14-16)

### F1.1: AGENTS.md erstellen (unter 200 Zeilen)
- **Status:** pass
- **Completed:** 2026-05-11
- **Scope:** Universal-Layer AGENTS.md mit WHAT / HOW / WHO / WHY-Struktur. Inhalt aus STARTPROMPT_CTO.md + userPreferences extrahiert, generalisiert auf Universal-Layer.
- **Evidence Required:** AGENTS.md committed unter /opt/covelab/AGENTS.md, unter 200 Zeilen, unter 2000 Tokens
- **Effort:** 2-3h
- **Depends:** F0.2

### F1.2: CLAUDE.md (duenne Variante) mit AGENTS.md-Import
- **Status:** pass
- **Completed:** 2026-05-11
- **Scope:** Kurze CLAUDE.md die AGENTS.md importiert plus CoveLab-spezifische Ergaenzungen
- **Evidence Required:** CLAUDE.md committed, importiert AGENTS.md korrekt
- **Effort:** 30min
- **Depends:** F1.1

### F1.3: Plugin-Marketplace-Setup (Anthropic Official + alirezarezvani)
- **Status:** pass
- **Completed:** 2026-05-11
- **Scope:** Anthropic Official Marketplace aktivieren (Auto-Update), alirezarezvani/claude-skills marketplace adden, pm-skills + engineering-skills installieren
- **Evidence Required:** /plugin list zeigt installierte Pakete, patterns/plugin-evaluation-anthropic-official.md + plugin-evaluation-alirezarezvani.md committed
- **Effort:** 1-2h
- **Depends:** F1.2

### F1.3a: Skill-Aktivierungs-Baseline-Test (NEU Tag 15 Spaetnachmittag)
- **Status:** pass
- **Completed:** 2026-05-11
- **Scope:** Empirische Baseline-Messung der Plugin-Skill-Activation-Rate post-F1.3-Install. Code-Chat probiert (a) Slash-Aufruf /pm-skills:roadmap (manuell, sollte 100% klappen), (b) Auto-Activation via natuerlicher Anfrage die pm-skills triggern sollte ohne expliziten Verweis, (c) Inspect ~/.claude/plugins/ Filesystem-Struktur, (d) Capture aller Skill-Namen plus Descriptions als Input fuer F1.3b SKILL_INDEX.
- **Evidence Required:** Test-Run-Output in patterns/skill-activation-baseline.md committed: Slash-Aufruf-Resultat, Auto-Activation-Erfolg-oder-Fehler, Skill-Names-Liste mit Descriptions, Filesystem-Pfade.
- **Effort:** 30 min
- **Depends:** F1.3
- **Anlass:** CEO-eingeforderte Skill-Adoption-Sicherstellung (Tag 15 Spaetnachmittag-Dialog). Recherche zeigt 50-77% Auto-Activation in Praxis (siehe patterns/best-practice-skill-activation.md), Test misst CoveLab-Baseline vor Workflow-Hardening.

### F1.3b: SKILL_INDEX.md plus RESEARCH_INDEX.md (NEU Tag 15 Spaetnachmittag)
- **Status:** pass
- **Completed:** 2026-05-11
- **Evidence:** Commit 748b567 - docs/SKILL_INDEX.md (141 Zeilen, 54 surfaced Skills plus 5 Bundle-Roots, CoveLab-Use-Case pro Skill) und docs/RESEARCH_INDEX.md (69 Zeilen, 14 Snapshots plus 5 Coverage-Gaps). Skill-Auswahl-Workflow in beiden Index-Files Zweck-Sektion plus session_log Sub-1b.3-Eintrag plus session_log Workflow-Anker.
- **Scope:** Zwei zentrale DB-Files in docs/. SKILL_INDEX.md: kurated Liste aller installierten Skills mit Name, Marketplace, Trigger-Keywords, CoveLab-Use-Case, Activation-Type (autonomous/slash/hook). Quelle der Wahrheit fuer Skill-Lookup vor jedem Code-Prompt. RESEARCH_INDEX.md: Mapping Topic zu patterns/best-practice-*.md mit Coverage-Markierung (konzeptionell vs operativ-detailliert per BR-Lehre).
- **Evidence Required:** docs/SKILL_INDEX.md plus docs/RESEARCH_INDEX.md committed. Plus: Skill-Auswahl-Workflow im session_log dokumentiert.
- **Effort:** 1-1.5h
- **Depends:** F1.3a (Baseline-Daten als Input)
- **Anlass:** Skill-Adoption-Hard-Verriegelung Schicht 1 (Doku-Layer). Skill-Wahl wird explizit dokumentiert vor Code-Prompt, Drift-Quelle entfernt. F1.5 plus F1.6-Spec-Konkretisierung (Subagent-skills-Preload-Feld plus UserPromptSubmit-Hook) folgen mit Bezug auf F1.3b-Output.

### F1.4: wshobson agent-orchestration-Plugin installieren (scope-updated)
- **Status:** pass
- **Completed:** 2026-05-12
- **Scope:** Original-Scope multi-agent-coordinator-Plugin plus 4 Agents plus 7 Commands plus 6 Skills obsolet durch wshobson-Refactor zu 82 granularen Plugins (Sub-1-Recon reports/wshobson_marketplace_recon_f14_sub1.md, Marketplace-Version 1.6.0). CEO-Entscheidung Sub-3: agent-orchestration alleine als F1.4-Realisierung. Begruendung: F1.4-Multi-Agent-Intent direkt getroffen, F1.5-Subagent-Lerngrundlage gegeben (context-manager-Agent), Token-arm.
- **Evidence:** Commit 001d8ba (Sub-5 patterns/plugin-evaluation-wshobson.md). Plugin agent-orchestration@claude-code-workflows v1.2.1 installiert (Sub-4, Capture reports/f14_sub4_install_20260512T065059Z.md), Real-Inhalt 1 Agent context-manager.md plus 2 Commands improve-agent.md plus multi-agent-optimize.md plus 0 Skills. claude plugin list zeigt 4 enabled.
- **Effort actual:** 3h (Sub-1 Recon plus Sub-2 CTO-Analyse plus Sub-3 CEO-Entscheidung plus Sub-4 Install plus Sub-5 plus Sub-6 Doku-Commits)
- **Depends:** F1.3
- **Lehren:** BQ-Bestaetigung (Marketplace-Manifest-Key-Authority), CB neu (Marketplace-Add OWNER/REPO statt Manifest-Key, BQ-Verstaerkung), BT-Reminder (Surfacing-Test post-Restart pending in neuer Code-Chat-Session)
- **Phase-2-Backlog:** agent-teams plus context-management plus block-no-verify (F1.6-Lerngrundlage) plus conductor plus plugin-eval plus review-agent-governance evaluiert in patterns/plugin-evaluation-wshobson.md.

### F1.5: Subagent-Definitionen .claude/agents/
- **Status:** pass
- **Completed:** 2026-05-12
- **Scope:** 4 Subagent-Markdown-Files in /opt/covelab/.claude/agents/: cto.md (Lead Architect Read+WebSearch+WebFetch plus skills senior-architect/senior-prompt-engineer/adversarial-reviewer), code.md (Executor Read/Write/Edit/Bash plus skills code-reviewer/tdd-guide/senior-devops), projektmanager.md (Drift-Detector Read-only plus haiku-model plus skills senior-pm/adversarial-reviewer), recherche.md (Best-Practice-Researcher Read+WebSearch+WebFetch plus skills senior-prompt-engineer/tech-stack-evaluator)
- **Evidence:** Commit 127497f (patterns/best-practice-subagent-frontmatter.md Snapshot operativ-detailliert, 183 Zeilen, 17 Frontmatter-Felder dokumentiert) plus Commit 6919767 (4 Subagent-Files Atomic-Commit, 222 insertions). Files architecture-korrekt mit valide YAML-Frontmatter und skills-Preload-Feld pro Subagent fuer 3-Schichten-Activation Schicht 3.
- **Caveat:** Empirische Surfacing-Verifikation (/agents-Befehl listet 4 plus Auto-Routing-Test) erfordert claude-CLI mit working-dir-walk-Zugriff auf /opt/covelab/. Aktuell laeuft claude-CLI lokal Windows ohne Repo-Klon (Vision UNIVERSAL_NORDSTERN VPS-Single-Source). Strukturelle Aufloesung via F2.4 (claude-CLI auf VPS). Files sind persistiert, Skill-Preload-Schicht-3 nicht-empirisch-validiert bis F2.4 pass.
- **Effort actual:** 2.5h (Recherche-Aufpolierung Schicht-1+2 web_search+web_fetch plus Snapshot-Commit plus Atomic-Build-Commit plus Status-Mini-Commit)
- **Depends:** F1.4
- **Lehren:** BR-Coverage erfuellt durch patterns/best-practice-subagent-frontmatter.md (operativ-detailliert). BT-Lehre praezisiert in Tag-16-Vormittag: galt fuer Plugin-Install (client-side ~/.claude/plugins/), Subagents-VPS-versioniert sind anderer Use-Case.

### F1.6: Hook-System (SessionStart + PreToolUse + PostToolUse + SessionEnd)
- **Status:** pass (mit Caveat empirische Aktivierung, analog F1.5)
- **Completed:** 2026-05-12
- **Scope:** 4 Hook-Scripts Python3 in /opt/covelab/.claude/hooks/ (Portabilitaet Windows-aktuell plus VPS-Phase-2). session_start_covelab.py (Lock-Stand plus R22-Reminder via stdout zu Claude-Context), pre_tool_use_covelab.py (Verbots-Pfad-Check via stdin-JSON plus exit 2), post_tool_use_covelab.py (Drift-Pattern-Auto-Detect plus decision approve mit Hinweis), session_end_covelab.py (Backup-Reminder zu stderr, kein Auto-SSH credential-fragil).
- **Evidence:** Commit 4ba97e8 (Sub-2 patterns/best-practice-hook-implementation.md operativ-detailliert 303 Zeilen) plus Commit 63ffc52 (Sub-3 4 Hook-Scripts Atomic-Build, 4 files / 102 insertions / mode 100755 chmod-plus-x verifiziert).
- **Caveat:** Empirische Aktivierung (slash-hooks-Browser plus Trigger-Tests) erfordert claude-CLI mit working-dir-Zugriff auf /opt/covelab/. Aktuell laeuft claude-CLI lokal Windows in C\Users\ronal\covelab-cockpit\ (Repo nicht lokal geklont, UNIVERSAL_NORDSTERN VPS-Single-Source). Strukturelle Aufloesung via F2.4 (claude-CLI auf VPS) plus F2.5 (Local-Cockpit-Eliminierung) - Hooks finden sich dann automatisch als working-dir-Files. KEIN settings.json-Lokal-Patch jetzt (CEO-Klarstellung "keine Provisorien").
- **Effort actual:** 4h (Recherche-Aufpolierung Schicht-2+3 web_search+web_fetch plus Sub-2 Snapshot-Commit plus Sub-3 Build-Atomic-Commit plus Sub-4 Status-Mini-Commit)
- **Depends:** F1.5
- **Lehren:** BR-Coverage Hook-Implementation Detail von GAP zu operativ-detailliert geschlossen (RESEARCH_INDEX Sub-2-Update). Truth-First-Praezisierung Sub-3: Code-Chat hat decision approve fuer PostToolUse korrekt gewaehlt (PostToolUse hat keine Block-Faehigkeit laut Anthropic-Doku) statt Task-Brief-Wort block.

---

## PHASE 2: Schreib-Faehigkeit + Live-State + Mobile-Bedienung (14-22h, Tag 17-22) — Tag 14 erweitert um F2.4 / F2.5

### F2.1: CTO-Schreibrechte via MCP-rw-Mount (war F9 alt)
- **Status:** pass
- **Completed:** 2026-05-12
- **Scope:** mcp-fs Container-Konfig auf rw umstellen fuer Allowlist-Pfade (docs/, patterns/, decisions/, STOLPERSTEINE.md, session_log.md, HANDOVER, COORDINATION.md). Aufloesung Stolperstein BJ. Implementiert als Variante A Single-Mount-Flip (Empfehlung aus patterns/best-practice-mcp-rw-mount.md Commit 5674e46): /opt/covelab zu /mnt/repo:rw via docker run -v ohne :ro Suffix. Application-Layer-Allowlist /mnt/repo (einziger Positional-Arg an server-filesystem) bleibt unveraendert.
- **Evidence:** docs/F21_MCP_RW_TEST.md (CTO-MCP write_file Test, 1849 Bytes, Roundtrip-Read OK via MCP read_text_file head=5, Metadata via MCP get_file_info bestaetigt mode 644 created 12:02:51Z). Sub-3 docker inspect RW=true plus HostConfig.Binds Liste mit /opt/covelab:/mnt/repo ohne :ro Suffix, neuer Container-ID 30b22569ec67. Stolpersteine BJ + BN RESOLVED in diesem Commit (siehe STOLPERSTEINE.md).
- **Effort actual:** ca 1h total (Sub-1 Recherche 30min + Sub-2 Recon 10min + Sub-2-Closure inkl Stolperstein CC 10min + Sub-3 CEO-PowerShell 5min + Sub-4 MCP-Test plus Closure 10min). BN-Schaetzung 1-2h korrekt bestaetigt.
- **Depends:** F1.6
- **Lehren:** Stolperstein CC neu (reports/-Verzeichnis vs .gitignore-Drift, CA-Klasse, F4.1-Cleanup-Backlog). Konfig-Quelle-Typ D bestaetigt (manueller docker run, keine compose/systemd/Skript-Quelle) - F4.1-Backlog: docker run als /opt/covelab/scripts/mcp_fs_start.sh ODER docker-compose.yml fuer Reproduzierbarkeits-Konsistenz (UNIVERSAL_NORDSTERN-VPS-Single-Source-Vision).

### F2.2: COORDINATION.md fuer Live-State (war F8 alt)
- **Status:** pass
- **Completed:** 2026-05-12
- **Scope:** COORDINATION.md als Top-Level Root-File mit Live-State (Current State plus Recent Activity plus Open Loose Ends plus Update-Konvention plus References - 5-Sektionen-Pattern). Industry-Standard-Name statt STATE.json. Hook-Auto-Update-Mechanik via 3 F1.6-Lifecycle-Events spec-definiert (SessionStart-Read als Live-State-Context, PostToolUse-git-commit-Reminder, SessionEnd-Backup-plus-Update-Reminder). Reminder-Pattern als Sub-3-Realisierung, Auto-Schreibe-Mechanik kommt F4.3.
- **Evidence:** Commit 7ce2a38 (Sub-1 patterns/best-practice-coordination-md.md operativ-detailliert 10798 B) plus Commit 132a70c (Sub-2 COORDINATION.md initial 7517 B Top-Level Root-File via CTO-MCP write_file) plus Commit 2835bc5 (Sub-3 3 F1.6-Hooks erweitert plus Stolperstein CD persistiert) plus diesen Commit (Sub-4 FAHRPLAN-Status-pass plus COORDINATION-Rekursiv-Update-Test).
- **Effort actual:** ca 2.5h total (Sub-1 Recherche 30min + Sub-2 COORDINATION-Build 25min + Sub-3 Hook-Erweiterung 45min + Sub-4 Closure 30min). Schaetzung 2-3h getroffen.
- **Depends:** F2.1
- **Caveat:** Auto-Schreibe-Mechanik fuer COORDINATION ist Reminder-only via Hooks (PostToolUse git-commit-Detection plus SessionEnd-Backup-Reminder plus SessionStart-Live-State-Read). Voll-automatische Schreibe-Logik kommt F4.3. Empirische Hook-Aktivierung erst post-F2.4 (analog F1.5 plus F1.6-Caveat).
- **Lehren:** Stolperstein CD neu (CTO-MCP write_file resetet File-Permissions auf 644 - Klasse-Lerner fuer Executable-File-Updates via Code-Chat-Build-Pattern statt MCP-direct). F2.2 demonstriert CTO-MCP-direct-write-Pattern fuer 2 NEUE Files (patterns/best-practice-coordination-md.md plus COORDINATION.md initial) plus Code-Chat-Build-Pattern fuer Executable-Overwrites (3 Hook-Files mit chmod-Pre-Stage plus py_compile-Check).

### F2.3: Direct-Communication via Headless claude -p (war F10 alt, MVP)
- **Status:** pass
- **Completed:** 2026-05-13
- **Sub-1 Status:** pass (bin/headless-wrapper.sh mode 755, synchroner claude -p Wrapper, JSON-Output, stdin /dev/null per CG-Lehre)
- **Sub-2 Status:** pass (tasks/in+out+processed File-Pattern, $1=task.json-Pfad, Result nach tasks/out/result_TIMESTAMP.json, ADR-0005 Allowlist um bin/+tasks/ erweitert)
- **Sub-3 Status:** pass (Cache-Reuse-Recon - Anti-Befund: --no-session-persistence zerstoert Cache-Key-Reuse, 9235 cache_creation Tokens je Call neu)
- **Sub-4 Status:** pass (Session-Persistence via --session-id Erst-Call plus --resume Folge-Calls, Timeout-Cap 60s - Re-Mess zeigte cache_creation 9235 auf 14 -99.85%, cost USD 0.0386 auf USD 0.0068 -82.4%)
- **Sub-5 Status:** pass (dieser Commit - FAHRPLAN-pass + STOLPERSTEINE CI+CJ+CK + session_log + HANDOVERs + Report + Atomic-Commit)
- **Scope:** /opt/covelab/tasks/-Verzeichnis (in / out / processed / results), claude -p Headless-Wrapper-Script, Trigger via cron oder inotify, allowed_tools-Restriction nach ADR-0005-Allowlist
- **Evidence Required:** 3 Tasks erfolgreich via B-Pipeline ohne CEO-Bridge ausgefuehrt
- **Evidence:** 3+3 Smoke-Tests (Sub-3 no-persist und Sub-4 persist) via /opt/covelab/bin/headless-wrapper.sh erfolgreich, Result-JSONs in tasks/out/result_*.json, Empirie-Report reports/f23_sub3_cache_reuse.md (7 Sektionen, Sub-3 plus Sub-4 Daten)
- **Effort:** 3-4h
- **Effort actual:** ca 2.5h (Sub-1 30min + Sub-2 30min + Sub-3 30min + Sub-4 45min + Sub-5 30min)
- **Depends:** F2.2

### F2.4: Claude Code Headless auf VPS + Push-Automation + API-Key-Provisioning (NEU Tag 14)
- **Status:** pass
- **Completed:** 2026-05-12
- **Sub-1 Status:** pass (CEO-PowerShell-Install claude-CLI 2.1.139 auf /usr/bin/claude; Stolperstein CE Code-Chat-Auto-Mode-Block korrekt ausgeloest - F1.6-Hook empirisch validated)
- **Sub-2 Status:** pass (OAuth-Token persistiert /etc/claude-code/env chmod 600 plus ~/.claude.json Onboarding-Bypass; Smoke-Test JSON-Roundtrip via ssh + source + claude -p + </dev/null erfolgreich, Sonnet-4-6, 4.1s Latenz, 0.04 USD kalkulatorisch Max-Plan-Quota; Stolpersteine CF Clipboard-CRLF plus CG stdin-Wait plus CH Kaltstart-Cost dokumentiert)
- **Sub-3 Status:** pass (dieser Commit - FAHRPLAN + STOLPERSTEINE CE+CF+CG+CH + session_log + Atomic)
- **Reorder-Notiz:** F2.4 wurde Tag 16 Vormittag VOR F2.3 vorgezogen (CEO-Entscheidung Option-b) wegen Test-Dependency - claude-CLI auf VPS noetig fuer F2.3-Headless-Wrapper-Bau plus -Test. GitHub Actions Push-Approval-Workflow aus Original-Scope herausgenommen plus auf neuen F2.6 verlagert (Scope-Reduktion)
- **Scope:** claude-CLI auf VPS installieren (Anthropic-API-Key als env var, npm-Version pinnen, zB @anthropic-ai/[email protected]). API-Key-Secret-Management via env oder Secret-Manager. GitHub Actions Workflow fuer Push origin main mit Approval-Webhook, Required-Reviewer = CEO (Approval via GitHub Mobile App Push-Notification). Sicherheits-Pattern: timeout 30m, --dangerously-skip-permissions nur sandboxed Container, Exit-Code-Pruefung, Concurrency-Cap, Cost-Limit.
- **Evidence Required:** claude --version auf VPS funktioniert; Test claude -p Run erfolgreich mit Output; .github/workflows/push-approval.yml committed; Approval-Test mit Mobile-Notification empfangen
- **Effort:** 3-5h
- **Depends:** F2.3
- **Referenz:** patterns/best-practice-vps-only-mobile-management.md (Commit 195b47a)

### F2.5: Local-Cockpit-Eliminierung (NEU Tag 14)
- **Status:** pass
- **Completed:** 2026-05-13
- **Scope:** Alle Build-Scripts in C:\Users\ronal\covelab-cockpit\ auf VPS migriert nach /opt/covelab/tools/. settings.local.json auf VPS. 3-Tage-Test auf 1-Tag-Test verkuerzt per CEO-Entscheidung (alle 4 Kriterien Tag 1 erfuellt).
- **Evidence:** Commit ccd591a (tools/ Migration Sub-2a) + ea07708 (settings.local.json Sub-2b) + fd11cbc (Migration-Stand) + e01673a (Test-Protokoll-Start). CEO-Entscheidung 2026-05-13: vorzeitiger Pass nach Tag 1. patterns/test-mobile-only.md PASS + freiwilliges Weiter-Log aktiv.
- **Effort actual:** ca 4h (Migration + Settings + Test)
- **Depends:** F2.4

### F2.6: GitHub Actions Push-Approval-Workflow (NEU Tag 16 Abend, verlagert aus F2.4 Sub-3)
- **Status:** pass
- **Completed:** 2026-05-13
- **Scope:** VPS-seitiges Push-Approval-Dashboard statt GitHub Actions (CEO-Entscheidung: alles auf VPS). Flask-App push_dashboard.py auf Port 172.18.0.1:8765, systemd-Service covelab-push-dashboard, Caddy reverse_proxy push.covelab.tech. Flask-eigene Basic-Auth (Credentials /etc/covelab-push/credentials chmod 600). DNS A-Record push.covelab.tech → 187.124.164.242 gesetzt.
- **Evidence:** push.covelab.tech erreichbar, Login funktioniert, Push-Button pushed erfolgreich (77da1ba..1fddc71 main -> main, Screenshot bestätigt). Stolpersteine CM (Caddy crash-loop Hard-Restart) + PEP668 venv-Fix dokumentiert. Kein PowerShell-Push mehr nötig.
- **Effort actual:** ca 3h (Flask + venv + systemd + Caddy + DNS + Auth-Fixes)
- **Depends:** F2.5

---

## PHASE 3: Universalitaet + Layer-Trennung (10-15h, Tag 20-23)

### F3.1: Repo-Verzeichnis-Refactor /framework + /project
- **Status:** fail
- **Scope:** Verzeichnis-Trennung im aktuellen Repo: /framework/* fuer Universal-Layer (CLAUDE.md, AGENTS.md, .claude/, docs/UNIVERSAL_*, decisions/0007 + 0010 + 0011 als Universal-Process-ADRs, patterns/best-practice-*, STOLPERSTEINE.md, session_log.md, COORDINATION.md, HANDOVER-Konvention) und /project/* fuer CoveLab-spezifisch (PROJECT_NORDSTERN.md statt UNIVERSAL_NORDSTERN, Mining-Patterns, projekt-spezifische ADRs)
- **Evidence Required:** Repo-Listing zeigt klare Trennung, Cross-References korrekt aktualisiert
- **Effort:** 4-6h
- **Depends:** F2.3

### F3.2: Generic Template-Repo "covelab-foundation" als separater Repo
- **Status:** fail
- **Scope:** Neuer Repo github.com/kaluppkeai-cell/covelab-foundation. /framework/* aus aktuellem Repo extrahieren, projekt-agnostisch generalisieren
- **Evidence Required:** Repo existiert, README.md vorhanden, Klon plus Setup-Skript dokumentiert
- **Effort:** 4-6h
- **Depends:** F3.1

### F3.3: Beispiel-Projekt-Layer "saas-launch-template" oder Equivalent
- **Status:** fail
- **Scope:** Generic Beispiel-Projekt-Layer-Repo das auf covelab-foundation aufbaut. Demo-Use-Case fuer Fremd-Anwender (zB SaaS-Launch oder Consulting-Pipeline). Klare Trennung Template vs CoveLab
- **Evidence Required:** Beispiel-Repo existiert, README erklaert Anwender-Workflow
- **Effort:** 2-3h
- **Depends:** F3.2

---

## PHASE 4: Datenreinigung + Auto-Routinen + Mobile-Trigger (8-11h, Tag 24-27) — Tag 14 erweitert um F4.4 / F4.5

### F4.0: Token-Effizienz-Audit plus Prompt-Optimierung (NEU Tag 16 Nachmittag, CEO-Anforderung)
- **Status:** pass (2026-05-13, Sub-1+2+3+4+5+6 alle pass)
- **Scope:** Senkung der Token-pro-Session ohne Qualitaetsverlust (CEO-Constraint explizit Tag 16 Nachmittag - kein Quality-Loss). 6 Sub-Schritte:
  - Sub-1: Schicht-2-plus-Schicht-3-Recherche plus Snapshot patterns/best-practice-token-efficiency.md (Schicht-1-Vorlauf Tag 16 Nachmittag mit 8 Anthropic-plus-Industry-Quellen schon erfolgt - Kernbefunde Prompt-Caching 90% Cost-Reduction plus Subagent-separate-Context-Windows plus Concise-Output-Skill 65% Output-Reduction plus Cache-Killer Whitespace-Drift).
  - Sub-2: Mess-Baseline reports/f40_baseline.md (aktuelle Session-Start-Tokens, Code-Prompt-Average, Cache-Hit-Rate via /cost oder Token-Checkup-Tool).
  - Sub-3: STARTPROMPT-Diaet (laut Memory generieren STARTPROMPT_CTO+CODE einen 50KB-plus Session-Start-Block - Ziel unter 5KB via Modular-References-at-docs-Slash statt Volltext-Inline).
  - Sub-4: Code-Prompt-Schema-Komprimierung (Build-Script-Inhalt als File-Reference-Pattern statt Inline-Doppelt-Uebertragung CTO-zu-Code-Chat).
- **Sub-4 Status:** pass (2026-05-13)
- **Sub-4 Evidence:** patterns/code-prompt-schema.md via CTO-MCP write_file (File-Reference-Pattern + Prompt-Template R13-konform + Script-Ablage-Konvention + Token-Einsparung-Empirie 760 Token/Sub-Schritt). AGENTS.md Code-Prompt-Hygiene-Sektion um Verweis ergaenzt. RESEARCH_INDEX.md Zeile addiert.
- **Sub-4 Effort actual:** ca 1h (CTO-MCP write_file x2 + Code-Chat-Commit).
  - Sub-5: Concise-Output Skill in .claude/skills/concise-output/SKILL.md fuer Code-Chat-PFLICHT-RUECKMELDUNG-Disziplin plus optional CTO-Antwort-Disziplin.
- **Sub-5 Status:** pass (2026-05-13)
- **Sub-5 Evidence:** .claude/skills/concise-output/SKILL.md committed (mode 100644, 7 Regeln + Beispiele + Token-Kontext). ADR-0005 um .claude/ non-settings Allowlist-Abschnitt erweitert.
- **Sub-5 Effort actual:** ca 30min (CTO-MCP Build-Script + Code-Chat-Commit).
  - Sub-6: Closure-Messung reports/f40_after.md plus FAHRPLAN F4.0 status-Update plus Stolperstein-falls-Aufgekommen.
- **Sub-6 Status:** pass (2026-05-13)
- **Sub-6 Evidence:** reports/f40_after.md via CTO-MCP write_file (statische Groessen-Messung + Token-Einsparung-Rechnung: -59% Session-Start-Load, -760 Token/Sub-Schritt File-Ref, -350 Token/Sub-Schritt Concise-Output, Quality-Check GRUEN). ccusage-7-Tage-Follow-up deferred Phase-2-Closure.
- **Sub-6 Effort actual:** ca 30min (CTO-MCP Mess + Report + Code-Chat-Commit).
- **Evidence Required:** patterns/best-practice-token-efficiency.md committed (Schicht-2-plus-3-vollstaendig, operativ-detailliert). reports/f40_baseline.md committed (Vorher-Werte mit Datum-Wallclock). STARTPROMPT_CTO.md plus STARTPROMPT_CODE.md neu unter 5KB committed. Code-Prompt-Schema-Update in AGENTS.md ODER neuem patterns/file committed. .claude/skills/concise-output/SKILL.md committed (chmod 644 fuer .md ist OK). reports/f40_after.md committed (Nachher-Messung plus Token-Reduction-Prozent dokumentiert mit Vorher-Werten als Baseline).
- **Sub-1 Status:** pass (2026-05-12)
- **Sub-1 Evidence:** patterns/best-practice-token-efficiency.md (18238 B, 8 Sektionen, via CTO-MCP write_file). Schicht-2 7 Quellen primaer Anthropic-Docs prompt-caching plus subagents plus 5 sekundaere Tiefen-Quellen. Schicht-3 4 Gegenstimmen-Quellen inkl arXiv-2604.00025 Maerz 2026 (Brevity-Constraints verbessern Large-Model-Accuracy 26 Prozentpunkte) plus Anthropic-Postmortem 23. April 2026 (aggressive Verbositaets-Cuts in System-Prompt verursachten Quality-Drop in Claude Code). Schicht-1.5 Coverage-Check ohne Konflikte BR/BT/BW/BY/BZ.
- **Sub-1 Effort actual:** ca 1.5h (Recherche 1h plus Snapshot-Schreiben 30min). Schaetzung Sub-1 war 1-2h getroffen.
- **Sub-1 Kern-Befunde:** (a) 3 aktive Cache-Killer in CoveLab identifiziert (STARTPROMPT-Timestamp im Cache-Prefix, append-Files-Tail-Wachstum, embedded Build-Scripts in Code-Prompts). (b) Subagent-Token-Multiplier ca 7x aber 90 Prozent davon Cache-Reads zu 10-Prozent-Preis - effektiv 1.5-2x Real-Kosten bei Heavy-Subagent-Sessions. (c) Caveman-Style aggressive Cuts risiko-positiv, Mid-Compression-Lite-Style empfohlen. (d) Mess-Tool ccusage (npm ryoppippi) als Baseline fuer Sub-2-Run. (e) Quality-Validation per Sub-6 zwingend, Stop-Loss bei jeglichem messbaren Quality-Drop.
- **Sub-3 Status:** pass (2026-05-12)
- **Sub-3 Evidence:** Token-Disziplin-Konvention etabliert in 4 Files: AGENTS.md (NEUE Sektion "## Token-Disziplin" Universal-Layer), docs/STARTPROMPT_CTO.md (R22-Tail-Limits-Praezisierung), patterns/best-practice-token-efficiency.md (Sektion 7 Sub-3-Block-Replace REVIDIERT post-Sub-2-Empirie), reports/f40_sub3_konvention.md (NEU 6 Sektionen Audit-Recap plus Re-Scope-Begruendung). Vorgaenger-Sub-2-Commit-SHA e6475e9 (Baseline-Mess-Run mit ccusage 7-Tage-Aggregat). Re-Scope-Begruendung kurz: Sub-2-Empirie revidierte Original-Direktive STARTPROMPT-Diaet (Files klein 4.7 plus 10 KB, NICHT Hauptdiaet), Re-Scope auf Token-Disziplin-Konvention (Tail-Limits plus Heavy-Session-Disziplin plus Cache-Hit-Disziplin).
- **Sub-3 Effort actual:** ca 1.5h (Konvention plus 3 MCP-Writes plus 1 Code-Chat-Edit-Run plus Atomic-Commit). Schaetzung Sub-3 war 1-2h aus FAHRPLAN, getroffen.
- **Effort:** 3-5h
- **Depends:** F2.2 done (formell Phase-4-Position). VORZIEH-OPTION: bei akutem Token-Druck kann F4.0 zwischen F2.2 und F2.3 ODER zwischen F2.5 und F3.1 gezogen werden (CEO-Entscheidung, Phase-Gate-Exception). Default-Reihenfolge bleibt F2.3-zu-F2.4-zu-F2.5-zu-F3.1-plus-zu-F4.0.
- **Anlass:** Tag 16 Nachmittag CEO-Anfrage Token-Effizienz fuer autonome-Arbeit-Phase post-F2.4. Schicht-1-Recherche-Vorlauf zeigt 90% Cost-Reduction-Potential via Industry-Best-Practices (Prompt-Caching automatisch bei claude.ai-Browser, Subagent-Context-Isolation, Concise-Output-Skill). Quality-first-Constraint vom CEO explizit zitiert (die qualitaet in keiner weise leiden).
- **Caveat:** Quality-first-Constraint - kein Caveman-Style fuer CTO-zu-CEO-Antworten (R11 max 5 Zeilen plain Deutsch bleibt). Concise-Output Skill nur fuer Code-Chat-PFLICHT-RUECKMELDUNG plus optional CTO-Antwort-Disziplin. Mess-Baseline zwingend vor-und-nach jeder Diaet-Aktion zur Qualitaets-Pruefung (Gegenstimme Token-Economics-2026 dokumentiert: compress prompts and you will confuse the model).

### F4.1: Cleanup-Konvention plus Notepad-Files-Cleanup
- **Status:** pass (2026-05-13, Vorzieh-Option CEO-Freigabe Tag 17)
- **Scope:** Konvention fuer Top-Level-Hygiene (was darf in /opt/covelab Root, was muss in Subverzeichnisse). Notepad-Files-Sammlung vom Cockpit-Lokal aufraeumen (alle .py Build-Scripts und .md Drafts archivieren oder loeschen). Backup-Bereinigungs-Routine (alte Backups nach N Tagen archivieren oder loeschen)
- **Evidence Required:** Konvention dokumentiert in patterns/cleanup-konvention.md, Cockpit aufgeraeumt, Backup-Bereinigungs-Script committed
- **Evidence:** patterns/best-practice-repo-cleanup.md (Research) + patterns/cleanup-konvention.md (Konvention) + archive/README.md (AI-Sentinel) committed 89c8af5. 118 Root-Files via git mv nach archive/handovers/ + archive/pre_pivot/ + archive/bak/ bewegt. 7 untracked WARN-Files via plain mv nachgezogen. tools/maintenance/cleanup_backups.sh (Retention 10 Backups, mode 755) committed. patterns/test-mobile-only.md auf 3 Tage aktualisiert.
- **Effort actual:** ca 3h (Research + Konvention + Root-Cleanup + Backup-Script)
- **Vorzieh-Option:** CEO-Freigabe Tag 17 (formal Depends F3.3, vorgezogen weil Root-Zustand Phase-3-Refactor vorbereitet)
- **Effort:** 2-3h
- **Depends:** F3.3

### F4.2: Status-on-Demand-Dashboard
- **Status:** pass
- **Completed:** 2026-05-13
- **Scope:** Slash-Command /status der aktuellen Stand zeigt (aktuelle Phase, aktiver F-Schritt, Working-Tree, HEAD-Sync, Container, Backup, Disk) ohne Code-Chat-Recon-Round-Trip
- **Evidence:** Commit 9cdfa35 — .claude/skills/status/SKILL.md (831B, mode 644). /status-Befehl zeigt HEAD, Working-Tree, pass/fail-Counts, Container, letztes Backup, Disk.
- **Effort:** 2-3h
- **Depends:** F4.1


### F4.2b: r22-reader + recon-runner Subagents (Session-Start-Token-Reduktion)
- **Status:** pass
- **Completed:** 2026-05-13
- **Scope:** Zwei neue Subagent-Defines in /opt/covelab/.claude/agents/: r22-reader.md (liest 9 R22-Pflicht-Files, gibt 500-1000-Token-Summary zurueck statt ~78K-Token-Full-Inject) und recon-runner.md (R28-Silent-Execution-Recon als isolierter Context). STARTPROMPT_CODE.md aktualisieren: statt vollem File-Dump r22-reader-Subagent aufrufen. Ziel: Session-Start-Block von ~78K auf ~5-10K Token reduzieren.
- **Evidence:** Commit 6cc5956 — .claude/agents/r22-reader.md (2.46KB) + recon-runner.md (2.82KB) neu. docs/STARTPROMPT_CODE.md 10KB -> 2.65KB (-73%). Session-Start-Block Output ~78K Token -> ~2-3K Token (S1-S3 Bash + r22-reader 500-1000 Token).
- **Effort actual:** ca 1.5h (CTO-MCP 3 File-Writes + Code-Chat-Commit + FAHRPLAN-Closure)
- **Depends:** F4.2 (Voraussetzung F2.3 + F2.4 bereits pass)
- **Anlass:** CEO-Direktive Tag 17 (Session-Start-Prompt verbrennt zu viele Token). Geplant seit AGENTS.md Token-Disziplin-Sektion F1.5-Roadmap-Erweiterung.

### F4.3: Auto-Routinen via Hooks (Backup, Push-Reminder, Cleanup-Trigger)
- **Status:** pass
- **Completed:** 2026-05-13
- **Scope:** SessionEnd-Hook triggert Postgres-Backup automatisch. Push-Reminder via Notification-Hook bei ungepushten Commits. Cleanup-Trigger via Schedule oder Threshold
- **Evidence:** Commit 225b556 — session_end_covelab.py (2725B, 0o755): Auto-Backup via docker exec + Python open(wb) BK-konform + Cleanup-Trigger cleanup_backups.sh + Push-Reminder mit Commit-Anzahl. post_tool_use_covelab.py (2405B, 0o755): Push-Reminder mit ungepushter-Commit-Zaehlung nach git commit. tools/build_f43_hooks.py persistiert (CD-Lehre: chmod via Build-Script).
- **Effort actual:** ca 1h (CTO-MCP Build-Script + Code-Chat-Commit)
- **Depends:** F4.2

### F4.4: MCP-Write-Loop-Trigger fuer Mobile-Bedienung (NEU Tag 14, ersetzt fruehere Telegram-Bot-Idee)
- **Status:** pass
- **Completed:** 2026-05-13
- **Scope:** /opt/covelab/tools/task_watcher.sh (inotifywait close_write tasks/in/), headless-wrapper.sh als Trigger-Target, systemd-Service covelab-task-watcher (enabled + active). Error-Handling nach tasks/errors/.
- **Evidence:** Commit c38abf1 (task_watcher.sh + systemd-Service + patterns/best-practice-inotify-systemd.md) + Commit d5764e3 (Stale-Session-Fix + Re-Smoke-Test pass). 3/3 Re-Smoke-Test Tasks erfolgreich: tasks/in/ -> inotify -> headless-wrapper.sh -> tasks/out/result_*.json + tasks/processed/. Stolperstein CL neu (stale session_id, Fix: retry-once nach stderr-Detection). Watcher-Service via systemctl is-active: active.
- **Effort actual:** ca 2h (Build-Script + inotify-tools + systemd + Smoke-Test + CL-Fix)
- **Depends:** F4.3 + F2.1

### F4.5: COORDINATION.md-Status-Loop fuer Async-Notifikation (NEU Tag 14)
- **Status:** pass
- **Completed:** 2026-05-13
- **Scope:** Mechanismus: CTO prueft bei jedem CEO-Turn die COORDINATION.md auf neue Status-Eintraege (Task-Results, Container-Status, Background-Activity). Auto-Update von COORDINATION.md via Hook (F1.6) oder via task_watcher.sh (F4.4). CTO meldet aktiv ohne CEO-Aufforderung.
- **Evidence:** Commit 2699bac — tools/log_task_result.py (results_log.jsonl append + COORDINATION.md Recent-Activity Update nach OK/ERROR), tools/task_watcher.sh (log_task_result.py-Aufruf eingebaut), .claude/hooks/session_start_covelab.py (results_log.jsonl tail-3 in Session-Start-Output), AGENTS.md (Status-Loop-Verhaltensregel). systemd-Watcher mit neuem task_watcher.sh aktiv.
- **Effort actual:** ca 1h (CTO-MCP Build-Script + Code-Chat-Commit)
- **Depends:** F4.4 + F2.2

---

## PHASE 5: Dokumentation + Verkaufs-Verpackung (8-13h, Tag 27-30)

### F5.0: Komponenten-Recherche plus Verkaufs-Strategie (NEU Tag 17, CEO-Direktive)
- **Status:** fail
- **Scope:** Recherche welche Fundament-Komponenten einzeln verkaeuflich sind (.claude/agents-Subset, Hooks, ADR-System, COORDINATION-Pattern etc.) versus Gesamt-Paket. Ziel: mehrere Produkte gleichzeitig im Markt platzierbar. Output: patterns/sales-strategy-components.md mit Komponenten-Liste plus Markt-Hypothesen plus Bundle-vs-Unbundle-Analyse.
- **Evidence Required:** patterns/sales-strategy-components.md committed; CEO bestaetigt strategische Ausrichtung vor F5.1-Quickstart-Doku-Start.
- **Effort:** 2-3h
- **Depends:** F4.5

### F5.1: Quickstart-Doku fuer Fremd-Anwender
- **Status:** fail
- **Scope:** README.md im covelab-foundation-Repo mit 5-Minuten-Quickstart (Klon, Konfig, erste Session)
- **Evidence Required:** README.md committed, Test-Lesung durch CEO als Fremd-Anwender-Sicht
- **Effort:** 2-3h
- **Depends:** F4.3

### F5.2: Konzepte-Doku (6-Schichten, Lock-Mechanik, Anti-Drift)
- **Status:** fail
- **Scope:** docs/CONCEPTS.md im covelab-foundation-Repo. Erklaert 6-Schichten-Modell, Lock-Mechanik, Anti-Drift-Disziplin, Best-Practice-Recherche-Pflicht, Stolperstein-System
- **Evidence Required:** CONCEPTS.md committed
- **Effort:** 2-3h
- **Depends:** F5.1

### F5.3: Anpassungs-Anleitung (Project-Layer-Customization)
- **Status:** fail
- **Scope:** docs/CUSTOMIZATION.md im covelab-foundation-Repo. Erklaert wie Anwender ihren eigenen Projekt-Layer befuellen (PROJECT_NORDSTERN.md, projekt-spezifische ADRs, Tools)
- **Evidence Required:** CUSTOMIZATION.md committed
- **Effort:** 2h
- **Depends:** F5.2

### F5.4: Demo-Walkthrough (Screencast oder Video)
- **Status:** fail
- **Scope:** 5-10 Minuten Demo-Walkthrough zeigt Klon-Setup-Erste-Session-Workflow. Faceless-Style (kein Gesicht, ElevenLabs-Voice nach ADR 0001 partial-supersede)
- **Evidence Required:** Video-File oder YouTube-Link in covelab-foundation-Repo
- **Effort:** 1-3h
- **Depends:** F5.3

### F5.5: Verkaufs-Material (Landing-Page, Upwork-Profil-Update)
- **Status:** fail
- **Scope:** Landing-Page covelab.tech-Subdomain oder neuer Hosting-Slot fuer Foundation-Verkauf. Upwork-Profil-Update mit "AI Project Foundation"-Lieferung-Angebot
- **Evidence Required:** Landing-Page live, Upwork-Profil aktualisiert, Markt-Test-Daten in patterns/launch-data.md nach 7 Tagen
- **Effort:** 1-2h
- **Depends:** F5.4

---

## Lock-Release
Sobald F0.1 bis F5.5 alle pass:
1. CEO bestaetigt im Chat: "Lock release Universal-Fundament"
2. UNIVERSAL_NORDSTERN.md neuer Eintrag "FUNDAMENT-LOCK released YYYY-MM-DD"
3. ADR 0012 mit Lessons aus Phase-0-bis-5
4. Cashflow-Phase startet (Verkaufs-Aktivitaet, Marketing, Produkt-Iteration)

## Effort-Zusammenfassung
- Phase 0: 1.5-2.5h
- Phase 1: 11-15h (Tag 15 Spaetnachmittag erweitert um F1.3a + F1.3b fuer Skill-Adoption-Workflow)
- Phase 2: 14-22h (Tag 14 erweitert um F2.4 + F2.5 fuer Mobile-Bedienung)
- Phase 3: 10-15h
- Phase 4: 8-11h (Tag 14 erweitert um F4.4 + F4.5 fuer MCP-Write-Loop + Status-Loop)
- Phase 5: 8-13h
- **Gesamt: 53-78.5h** (12-19 Werktage bei 4h/Tag, 25-38 Werktage bei 2h/Tag)
- Realistisch: 5-10 Wochen
- Anmerkung Tag 15 Spaetnachmittag: 2 zusaetzliche F-Schritte (F1.3a + F1.3b) eingefuegt fuer Skill-Adoption-Sicherstellung (Workflow-Lock vor Chat-Komprimierung). Plan ist jetzt 29 F-Schritte (war 27). Recherche-Quelle patterns/best-practice-skill-activation.md.
Anmerkung Tag 14: 4 zusaetzliche F-Schritte (F2.4 / F2.5 / F4.4 / F4.5) eingefuegt fuer CEO-Vision "alles auf Hostinger + Mobile-Bedienung". Plan ist jetzt 27 F-Schritte (war 23).
Anmerkung Tag 16 Nachmittag: 1 zusaetzlicher F-Schritt (F4.0 Token-Effizienz-Audit plus Prompt-Optimierung) eingefuegt am Anfang Phase 4 fuer CEO-Anforderung Token-Effizienz post-F2.2-RESOLVED. Plan ist jetzt 30 F-Schritte (war 29). Phase 4 Effort: 11-16h (war 8-11h). Gesamt: 56-83.5h (war 53-78.5h). Schicht-1-Recherche-Vorlauf erfolgt Tag 16 Nachmittag mit 8 Anthropic-plus-Industry-Quellen. Vorzieh-Option dokumentiert in F4.0-Depends-Sektion (kann bei akutem Token-Druck zwischen F-Schritten Phase 2-3 gezogen werden, CEO-Entscheidung).
Anmerkung Tag 17 Abend: 1 zusaetzlicher F-Schritt (F4.2b r22-reader + recon-runner Subagents) eingefuegt nach F4.2 fuer CEO-Direktive Session-Start-Token-Reduktion (~78K auf ~5-10K Token). Plan ist jetzt 31 F-Schritte. Effort-Schätzung +1-2h.

## References
- ADR 0007 (Fundament-Lock) — Lock-Mechanik
- ADR 0010 (Best-Practice-Recherche-Pflicht)
- ADR 0011 (Strategie-Pivot Universal-Fundament) — Authority-Quelle
- patterns/best-practice-multi-agent.md
- patterns/best-practice-claude-md.md
- patterns/best-practice-skills-plugins.md
- patterns/best-practice-projektmanager.md
- FUNDAMENT_PLAN.md — historischer CoveLab-spezifischer Lock-Plan, wird durch diesen Fahrplan ersetzt
