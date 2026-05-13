# best-practice-coordination-md

**Erstellt:** 2026-05-12 (Tag 16 Nachmittag, F2.2 Sub-1)
**Anlass:** F2.2 COORDINATION.md fuer Live-State erfordert Recherche-Pflicht (ADR 0010 Schicht 1.5). Im RESEARCH_INDEX kein bestehender Snapshot zu Live-State-File-Pattern.
**Quellen-Schichten:** Schicht 1 (Anthropic Produkt-Wissen-Skill - Routing zu Schicht 2/3) plus Schicht 2 (offizielle AGENTS.md-Spec, Linux Foundation AAIF Stand 2026) plus Schicht 3 (web_search Industry-Pattern-Stand fuer Live-State-Coordination-Markdown).

## Quellen-Liste

1. agents.md / github.com/agentsmd/agents.md (offizielle AGENTS.md-Spec, Linux Foundation AAIF Stand 2026, donated post-OpenAI-Codex-Pioneering)
2. docs.factory.ai/cli/configuration/agents-md (Factory-Doku AGENTS.md-Struktur und Precedence)
3. purplehorizons.io/blog/tick-md-multi-agent-coordination-markdown (tick-md Task-Coordination-Pattern, MCP-Server-faehig)
4. augmentcode.com/guides/how-to-build-agents-md (Living-Specs Pattern, Intent-System, ETH-Studie zu Context-File-Performance)
5. infoq.com/news/2025/08/agents-md (AGENTS.md Industry-Adoption-Stand, 20k+ Repos GitHub)
6. glebbahmutov.com/blog/status-dashboard-from-markdown (Status-Dashboard mit Markdown plus Badges - Build-Status-Pattern)
7. github.com/martinpeck/oss-project-status (OSS_STATUS.md Health-Badge-Pattern)
8. developers.openai.com/codex/guides/agents-md (Codex AGENTS.md Precedence-Discovery)
9. patterns/best-practice-hook-implementation.md (CoveLab-eigener Snapshot F1.6 fuer Hook-Integration)
10. patterns/best-practice-claude-md.md (CoveLab-eigener Snapshot F1.1/F1.2)

## Pflicht-Elemente (Quellen-Konsens und Gap-Befunde)

### 1. KEIN harter Industry-Standard fuer Live-State-Coordination-Markdown

Recherche-Stand 2026-05-12: Es existiert KEINE etablierte Konvention "COORDINATION.md" oder Equivalent fuer Live-State-Management in AI-Agent-Projekten. Cousin-Patterns die existieren:

- **AGENTS.md**: persistent project context (Build-Befehle, Conventions, Test-Workflows). Statisch, kein Live-State. Linux Foundation AAIF donated Dezember 2025, 20k+ Repos.
- **TICK.md (tick-md)**: Multi-Agent Task-Tracking mit Claim/Lock/Dependency. Task-Management, NICHT State-Snapshot. MCP-Server-faehig.
- **OSS_STATUS.md**: Project-Health-Status-Badge (Active/Inactive/Abandoned). Statische Pflege.
- **README.md mit Badges**: Build-Status-Coverage via externe Service-Badges. Live aber begrenzt.
- **Living Specs (Augment Intent)**: spec-Updates durch Agent bei Task-Completion. Proprietary, MCP-orientiert.

Konsequenz: CoveLab-COORDINATION.md ist Design-Entscheidung ohne harten Industry-Anker. FAHRPLAN-Notiz "Industry-Standard-Name statt STATE.json" praezisiert: Naming-Wahl (UPPERCASE-Markdown-Root-File-Konvention konsistent mit AGENTS.md/CLAUDE.md/STOLPERSTEINE.md), keine Konventions-Ankerung. ETH-Studie zu Context-Files (Augment-Quelle): LLM-generierte Context-Files verschlechterten Task-Success-Rate um 3 Prozent. Manuell kuratierte Context-Files leicht positiv. Live-State-Files brauchen menschliche Kuration plus maschinelle Auto-Updates - Hybrid-Ansatz.

### 2. Live-State-File Design-Prinzipien (aus AGENTS.md-Konsens abgeleitet)

- **Plain Markdown** (nicht JSON/YAML) fuer LLM-Native-Lesbarkeit, Git-Diff-Lesbarkeit, menschliche Direkt-Lese-Faehigkeit
- **Top-Snapshot Sektion** ueberschreibbar (Current-State: Phase, F-Step, Container, Backup, HEAD, Lock-Status)
- **Recent-Activity Tail** append-haeufig (letzte N Events, strukturierter als session_log das ist Free-Form)
- **Next-Step Sektion** ueberschreibbar (was kommt als Naechstes nach aktuellem F-Step)
- **Maschinenparsbare Struktur** ueber Markdown-Listen mit konsistenten Schluesseln (KEIN YAML-Frontmatter da Markdown-Render dann YAML versteckt)
- **Auto-Update-Mechanik** via Hooks (PostToolUse fuer Commit-Events, SessionStart fuer Read-into-Context, SessionEnd fuer Backup-Stand)
- **Konsistent mit AGENTS.md-Konvention**: Plain Markdown, semantic Headings, kompakt (Augment-Stand: 150 Zeilen empfohlen, ETH-Befund Brevity ueber Encyclopedia)

### 3. Hook-Integration-Optionen (aus patterns/best-practice-hook-implementation.md operativ-detailliert)

Vier Lifecycle-Events fuer COORDINATION.md-Auto-Update (nutzt schon vorhandene F1.6-Hook-Files):

- **SessionStart** (session_start_covelab.py): liest COORDINATION.md und macht Stand fuer CTO-Chat sichtbar via stdout-zu-Claude-Context (R22-Erweiterung) 
- **PostToolUse** (post_tool_use_covelab.py): aktualisiert HEAD-SHA in Top-Snapshot bei git-commit-Operationen. Auf Tool-Pattern Bash mit cmd-Filter git commit -F oder git commit -m
- **SessionEnd** (session_end_covelab.py): schreibt finalen Backup-Stand und Session-End-Tag in Recent-Activity nach Postgres-Backup-Reminder
- **UserPromptSubmit** (optional, nicht F1.6-Standard): zeigt CEO-Anfrage-Empfang als Recent-Activity-Event - eher Schluerf, weil R22 das schon macht

Caveat (F1.5/F1.6-Praezedenz Tag 16 Vormittag): Hook-Aktivierung erfordert claude-CLI mit working-dir-walk-Zugriff auf /opt/covelab/.claude/hooks/. Aktuell laeuft claude-CLI lokal Windows ohne Repo-Klon. Strukturelle Aufloesung via F2.4 (claude-CLI auf VPS). Bis dahin: COORDINATION.md wird via CTO-MCP-direct-write (bei Phase-Wechseln und groesseren Events) und Code-Chat-Python-Build (bei Commit-bundled Updates) manuell aktuell gehalten - analog zu session_log.md-Pflege jetzt.

## Konkrete CoveLab-Design-Entscheidung

### Pfad und Naming

`/opt/covelab/COORDINATION.md` - Top-Level, gleicher Rang wie AGENTS.md, CLAUDE.md, STOLPERSTEINE.md, session_log.md, UNIVERSAL_NORDSTERN.md. Konsistent mit Universal-Layer-Files-Konvention (Phase-3-F3.1 Repo-Refactor wird COORDINATION.md in /framework/ verschieben).

### Struktur (5 Sektionen)

Sektion 1: **Current State** (Top-Snapshot, ueberschreibbar bei jedem Hook-Trigger oder manuellen Update)
- Phase (N of 5)
- Active F-Step (in progress) plus Last Completed F-Step
- Lock-Status (ACTIVE/RELEASED)
- HEAD origin/main SHA
- HEAD-ungepusht-Zahl
- Container-Status (postgres / mcp-fs / n8n / caddy mit Up/Down plus Mount-RW falls relevant)
- Last Backup (Filename plus Bytes)
- Last Activity (ISO-Timestamp plus 1-Zeilen-Beschreibung)
- Next Step (1-2 Zeilen)

Sektion 2: **Recent Activity** (Tail-Append, letzte 20 Events)
- Format: `- YYYY-MM-DDTHH:MM:SSZ <event-type>: <description> (commit-SHA falls relevant)`
- Event-Typen: commit, container-restart, backup, phase-change, f-step-pass, stolperstein-add, ceo-pause, session-end

Sektion 3: **Open Loose Ends** (ueberschreibbar, kurze Liste bewusster offener Punkte aktuell)
- Aus aktuellem Sub-Sequenz-Status plus F4.1-Backlog-Sammlung

Sektion 4: **Update-Konvention** (statisch, ueberschreibbar bei Pattern-Wechseln)
- Welche Trigger updaten welche Sektion
- Hook-Pfade plus Caveat-Status (empirisch erst post-F2.4)
- Manuelle-Update-Pflichten bis Hooks empirisch laufen

Sektion 5: **References** (statisch)
- Authority-Files
- Hook-Files-Pfade
- Verwandte ADRs

### Auto-Update-Strategie (Phase 2 jetzt vs Phase 4 spaeter)

**Phase 2 F2.2 (jetzt):**
- COORDINATION.md initial via CTO-MCP write_file mit aktuellem Stand (Sub-2)
- Hook-Script-Definition update_coordination.py in /opt/covelab/.claude/hooks/ als Spec-Datei (Sub-3) - PostToolUse-gefiltert auf git-commit-Events plus SessionStart-Read
- Caveat: empirisch erst nach F2.4 aktiv (F1.5/F1.6-Praezedenz)
- Manueller Update-Pfad: bei Phase-Wechseln (CTO-MCP write_file), bei groesseren Sub-N-Closures (Code-Chat-Python-Build im Closure-Commit), bei Container-Lifecycle (CEO-PowerShell plus CTO-Update danach)

**Phase 4 F4.3 (spaeter):**
- Auto-Routinen via Hooks aktiv (post-F2.4)
- SessionEnd-Hook triggert Postgres-Backup plus COORDINATION.md-Update mit neuem Backup-Pfad
- Push-Reminder bei ungepushten Commits
- F4.2 /status-Slash-Command zeigt COORDINATION.md-Tail direkt im Chat

### Update-Atomicitaet und Concurrency

COORDINATION.md ist append-haeufig (Recent-Activity-Tail) und ueberschreibbar-bei-Phase-Change (Current-State-Top). 

Risiken:
- Concurrent Writes durch mehrere Code-Chats (R30 Parallel-Chats-Regel): wenn mehrere parallel laufen. Mitigation: ONE Dedicated-Chat updated COORDINATION.md (analog session_log-Regel). Andere liefern Update-Vorschlaege via Report-File.
- Versehentliches Overwrite via CTO-MCP write_file: read-modify-write Pattern empfohlen (CTO liest erst, modifiziert dann, schreibt zurueck). Build-Script-Pattern (Code-Chat-BS) fuer komplexere Multi-Sektion-Updates.
- Hook-Race-Conditions bei PostToolUse: Hook-File-Lock-Mechanik nutzen (analog tick-md File-Locking). Erst post-F2.4 empirisch testbar.

### F2.2 Sub-Sequenz-Plan

- **Sub-1 (dieser Snapshot):** Recherche plus best-practice-coordination-md.md committed via CTO-MCP-direct-write plus RESEARCH_INDEX-Update via Code-Chat-Closure-Commit.
- **Sub-2:** COORDINATION.md initial via CTO-MCP write_file (Top-Snapshot mit aktuellem Stand Tag 16 Nachmittag, Recent-Activity-Tail mit letzten 10 Events aus session_log) plus session_log-Eintrag plus Commit via Code-Chat.
- **Sub-3:** Hook-Script-Definition .claude/hooks/update_coordination.py via Code-Chat-Build (analog F1.6 Sub-3). Drei Lifecycle-Events (SessionStart-Read, PostToolUse-git-commit-Update, SessionEnd-Backup-Tag). Caveat empirisch erst nach F2.4.
- **Sub-4:** F2.2-Closure - FAHRPLAN F2.2 fail-zu-pass plus session_log-Update plus Recent-Activity-Eintrag in COORDINATION.md selbst (rekursiv-Update-Test).

## Coverage-Klassifikation

**operativ-detailliert:** enthaelt konkrete Datei-Struktur-Spec (5 Sektionen mit Beispiel-Inhalt), Hook-Integration-Plan auf vorhandene F1.6-Hook-Files, Atomicity-Strategie, F2.2-Sub-Sequenz-Plan mit konkreten Update-Pfaden.

## Stolperstein-Bezuege

- BR (Snapshot-Coverage-Check): Coverage-Gap "Context-Engineering / Pointer-Systeme / Token-Optimierung" aus RESEARCH_INDEX teil-relevant (COORDINATION.md ist Pointer-System fuer Live-State). Snapshot schliesst F2.2-spezifische Coverage-Luecke.
- F1.5/F1.6-Caveat: gilt analog fuer F2.2 Sub-3 Hook-Script (empirisch erst post-F2.4 claude-CLI auf VPS).
- R30 (Parallel-Chats ONE-writes-Regel): gilt fuer COORDINATION.md analog session_log.
- BS-Default (Build-Script Pattern fuer File-Operationen ueber 3 KB): COORDINATION.md initial-write via CTO-MCP direkt (1-2 KB), spaetere Multi-Sektion-Updates via Code-Chat-Python-Build.

## References

- decisions/0009-schreib-faehigkeit-foundation.md (F2-Bereich Anker)
- decisions/0010-best-practice-recherche-pflicht.md (Schicht 1.5 Coverage-Check)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F2.2
- patterns/best-practice-hook-implementation.md (Hook-Integration-Detail F1.6)
- patterns/best-practice-claude-md.md (Markdown-File-Konvention F1.1/F1.2)
- patterns/best-practice-mcp-rw-mount.md (MCP-write-Capability-Anker F2.1)
- STOLPERSTEINE.md BR plus R30
