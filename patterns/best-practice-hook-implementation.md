# Best-Practice-Snapshot: Hook-Implementation in Claude Code 2026

**Recherchiert:** 2026-05-12 (Tag 16 Vormittag, F1.6-Vor-Arbeit)
**Anlass:** F1.6 Hook-System (4 Hook-Scripts in /opt/covelab/.claude/hooks/) braucht operativ-detaillierte Event-Lifecycle plus Input/Output-Mechanik plus Registrierungs-Pattern. ADR 0010 Schicht 1.5 BR-Coverage-Pflicht (RESEARCH_INDEX Gap-Eintrag bis Tag-15-Spaet-Abend, jetzt geschlossen).
**Quellen (8):**
- code.claude.com/docs/en/hooks-guide (Anthropic offiziell - Konfig-Layer plus Event-Beispiele)
- code.claude.com/docs/en/hooks (Anthropic offiziell - Reference Event-Matrix plus Schemas)
- claudefa.st/blog/tools/hooks/hooks-guide (Community-Tutorial - Lifecycle-Praxis)
- smartscope.blog/en/generative-ai/claude/claude-code-hooks-guide (Industry-Walkthrough)
- github.com/disler/claude-code-hooks-mastery (OSS-Pattern-Sammlung mit echten Scripts)
- codesignal.com Implementing-PostToolUse-Hooks (PostToolUse Tutorial mit jq-Beispiel)
- blakecrosley.com Claude-Code-Hooks-Tutorial (Komplette Hook-Einrichtung)
- claudelog.com/mechanics/hooks (Mechanik-Dokumentation plus Exit-Code-Semantik)

## A) Hook-Konzept

Hook ist deterministisches Shell-Command oder MCP-Tool-Call das bei einem Lifecycle-Event ausgeloest wird. Anthropic-offiziell-Zitat:

> Hooks let you run code at specific points in Claude Code's lifecycle to control or extend its behavior. They turn LLM-driven judgments into deterministic, code-enforced policy.

Industry-Standard 2026: Hooks ersetzen LLM-Judgment (probabilistisch) durch Code-Logik (deterministisch) fuer Sicherheits-, Compliance- und Workflow-Checkpoints. Beispiele: Verbot-Pfad-Blocker, Audit-Logging, Auto-Format, Backup-Trigger, Drift-Detection.

CoveLab-Vision-Bezug (UNIVERSAL_NORDSTERN): Hooks sind das technische Rueckgrat fuer R-Regel-Erzwingung im VPS-Setup (z.B. Verbot von .claude/settings*.json-Schreiben durch Code-Subagent via PreToolUse-Hook, R-Lehre plus CA-Lehre).

## B) Event-Lifecycle-Tabelle

Stand Anthropic-Doku 2026-05: 9 offiziell-dokumentierte Lifecycle-Events. Industry-Erweiterungen (Custom-Hooks via Wrapper) bringen weitere Hookpunkte. Truth-First: die Zahl der Events ist nicht stabil zwischen Releases; pruefe code.claude.com/docs/en/hooks vor jedem F-Schritt.

| Event | Trigger | Matcher | Block-Faehigkeit |
|-------|---------|---------|-------------------|
| SessionStart | Beim CLI-Start oder /resume oder /clear oder /compact | startup, resume, clear, compact | nein (nur stdout-Injection) |
| SessionEnd | Beim CLI-Exit | exit, logout, clear, prompt_input_submit | nein (Cleanup-Hook) |
| UserPromptSubmit | Nach User-Prompt vor Claude-Verarbeitung | keine Matcher noetig | ja (Exit 2 = Prompt-Reject) |
| PreToolUse | Vor Tool-Call (Bash, Edit, Write, Read, MCP, ...) | tool name pattern (Bash, Edit, mcp__server__tool) | ja (Exit 2 = Tool-Reject) |
| PostToolUse | Nach Tool-Call vor Output-Verarbeitung | tool name pattern | nein (Modifikation via JSON-Output) |
| PreCompact | Vor Context-Compaction | manual, auto | nein |
| Stop | Wenn Claude Main-Agent-Turn beendet | keine Matcher | ja (Exit 2 = Continue-Request) |
| SubagentStop | Wenn Subagent-Turn beendet | subagent name pattern | ja (analog Stop) |
| Notification | Bei System-Notification (User-Wait, Tool-Approval) | keine Matcher | nein |

Industry-Erweiterungen (Custom-Wrapper, NICHT offiziell): PreRead/PostRead via Bash-Wrapper, ToolError via Exit-Code-Catch, AgentSwitch via SubagentStop+SessionStart-Chain. Diese gehoeren NICHT in F1.6-Scope.

## C) Konfigurations-Layer-Tabelle (6 Locations)

| Location | Scope | Pfad | Versionierung |
|----------|-------|------|---------------|
| Enterprise managed-settings | Org | /etc/claude-code/managed-settings.json (Linux) | Admin-deployed, nicht-user-bearbeitbar |
| User-Settings | User | ~/.claude/settings.json | User-Profil, cross-project |
| Project-Settings | Project | .claude/settings.json | REPO-versioniert (RECOMMENDED fuer Team-Sharing) |
| Project-Local-Settings | Project | .claude/settings.local.json | gitignored, Maschinen-spezifisch |
| Plugin-Hooks | Plugin | plugin/hooks/hooks.json | Plugin-paketiert, read-only |
| Frontmatter-Hooks | Skill/Agent | YAML-frontmatter hooks-Field | innerhalb Skill/Agent-File |

Praezedenz: managed-settings ueberschreibt User-Settings ueberschreibt Project-Settings ueberschreibt Project-Local-Settings ueberschreibt Plugin-Hooks. Frontmatter-Hooks gelten nur im Skill/Subagent-Kontext.

CoveLab-Anwendung F1.6: Project-Settings .claude/settings.json fuer Repo-versionierte Hook-Registrierung (BY/BZ-Lehre VPS-Single-Source). Hook-Scripts liegen in .claude/hooks/ und werden in settings.json-Hooks-Block referenziert.

## D) Input/Output-Mechanik

Hook-Input ueber stdin als JSON:

```json
{
  "session_id": "abc-123",
  "transcript_path": "/path/to/transcript.json",
  "cwd": "/opt/covelab",
  "hook_event_name": "PreToolUse",
  "tool_name": "Bash",
  "tool_input": { "command": "rm -rf /" }
}
```

Event-spezifische zusaetzliche Felder:
- PostToolUse: plus tool_response
- UserPromptSubmit: plus prompt
- SessionStart: plus source (startup/resume/clear/compact)
- SessionEnd: plus reason
- Stop / SubagentStop: plus stop_hook_active flag

Hook-Output ueber stdout (Auto-Mode) oder JSON-Schema (Advanced-Mode):

Auto-Mode: einfacher Text auf stdout wird je nach Event injiziert (SessionStart -> Context-Pre-Pend; UserPromptSubmit -> Prompt-Annotation; PostToolUse -> Audit-Echo).

JSON-Mode: strukturierte Steuerung via hookSpecificOutput-Pattern:

```json
{
  "continue": true,
  "stopReason": null,
  "suppressOutput": false,
  "decision": "approve" | "block" | null,
  "reason": "string fuer Claude-Visibility",
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow" | "deny" | "ask",
    "permissionDecisionReason": "string"
  }
}
```

Exit-Code-Semantik:
- 0 = success, weiterer Verlauf normal
- 2 = block, stderr-Inhalt wird an Claude geliefert als Feedback
- andere = non-blocking error, stderr wird user-side gezeigt

## E) Matcher-Patterns

PreToolUse/PostToolUse: matcher matcht gegen tool_name als Regex oder Pipe-getrennte Liste. Beispiele:
- matcher: Bash -> nur Bash-Tool
- matcher: Edit|Write|MultiEdit -> alle Schreib-Tools
- matcher: mcp__.* -> alle MCP-Tools
- matcher: * oder weglassen -> alle Tools

SessionStart: matcher matcht gegen source-Feld (startup|resume|clear|compact).
SessionEnd: matcher matcht gegen reason-Feld (exit|logout|clear|prompt_input_submit).
SubagentStop: matcher matcht gegen subagent_type.

Mehrere Matcher pro Event moeglich (Array von Objects). Wildcard * matcht alles.

## F) Hook-Types (5)

| Type | Verwendung | Format |
|------|-----------|--------|
| command | Shell-Command (Default) | type: command, command: ./scripts/hook.sh |
| http | HTTP-Webhook-POST | type: http, url: https://endpoint, headers: {} |
| mcp_tool | MCP-Tool-Aufruf | type: mcp_tool, server: name, tool: tool_name |
| prompt | LLM-Single-Turn-Judgment | type: prompt, prompt: 'Pruefe X. Antworte yes oder no.' (Anti-Pattern fuer Sicherheit, weil LLM probabilistisch) |
| agent | LLM-Multi-Turn (experimental) | type: agent, agent: subagent-name |

CoveLab-Anwendung F1.6: ausschliesslich type=command fuer alle 4 Hooks. Begruendung: deterministisch, schnell, kein API-Cost.

## G) CoveLab-Anwendung F1.6 - 4 Hook-Specs

### Hook-1: session_start_covelab.sh (Event SessionStart, alle Matcher)

**Aufgabe:** Pflicht-Lesung-Reminder per stdout-Injection in Context. Liefert R22-File-Liste plus aktuellen Lock-Stand aus FAHRPLAN als Header-Block.

**Implementation:**
```bash
#!/bin/bash
set -euo pipefail
cat <<TXT
PFLICHT-LESUNG (R22):
- UNIVERSAL_NORDSTERN.md (Vision plus Lock)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md (F-Schritt-Status)
- STOLPERSTEINE.md tail-100
- session_log.md tail-60
- neueste HANDOVER_*.md
- AGENTS.md plus CLAUDE.md
LOCK-STAND (aktuell aus FAHRPLAN):
$(grep -E '^### F[0-9]' /opt/covelab/docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md 2>/dev/null | head -10)
TXT
exit 0
```

**Matcher:** keine (alle SessionStart-Quellen).
**Performance:** unter 50ms.

### Hook-2: pre_tool_use_covelab.sh (Event PreToolUse, Matcher Bash|Write|Edit|MultiEdit)

**Aufgabe:** Verbots-Pfad-Check. Block auf:
- .claude/settings*.json (R-Lehre Code-Chat-Verbot)
- NORDSTERN.md (ADR 0011 SUPERSEDED-Stub, nicht-veraender)
- Top-Level-Files ausserhalb ADR-0005-Allowlist

**Implementation-Skeleton:**
```bash
#!/bin/bash
set -euo pipefail
INPUT=$(cat)
TOOL=$(echo "$INPUT" | python3 -c 'import json,sys; print(json.load(sys.stdin).get("tool_name",""))')
TOOL_INPUT=$(echo "$INPUT" | python3 -c 'import json,sys; print(json.dumps(json.load(sys.stdin).get("tool_input",{})))')

block() {
  echo "$1" >&2
  exit 2
}

if [[ "$TOOL" =~ ^(Write|Edit|MultiEdit)$ ]]; then
  FILE=$(echo "$TOOL_INPUT" | python3 -c 'import json,sys; d=json.load(sys.stdin); print(d.get("file_path") or d.get("filepath",""))')
  case "$FILE" in
    */.claude/settings*.json) block "R-Lehre: .claude/settings.json nur CEO-side manuell." ;;
    */NORDSTERN.md) block "ADR 0011: NORDSTERN.md ist SUPERSEDED-Stub - bearbeite UNIVERSAL_NORDSTERN.md." ;;
  esac
fi
exit 0
```

**Matcher:** Bash|Write|Edit|MultiEdit.
**Performance:** unter 100ms.

### Hook-3: post_tool_use_covelab.sh (Event PostToolUse, Matcher Bash|Write|Edit)

**Aufgabe:** Stolperstein-Auto-Detect via Keyword-Scan in tool_response auf typische Drift-Patterns (SSH-Hostkey-Fail, Plugin-Cache-Miss, Permission-Denied auf .claude/agents). Hinweis via JSON-Output decision approve plus reason.

**Implementation-Skeleton:**
```bash
#!/bin/bash
set -euo pipefail
INPUT=$(cat)
RESPONSE=$(echo "$INPUT" | python3 -c 'import json,sys; d=json.load(sys.stdin); print(json.dumps(d.get("tool_response","")))')

if echo "$RESPONSE" | grep -qE "Permission denied.*\.claude/settings"; then
  cat <<JSON
{"decision":"approve","reason":"Drift-Pattern erkannt: settings.json-Schreibversuch. Pruefe Stolperstein R + CA, escaliere an CEO."}
JSON
  exit 0
fi
exit 0
```

**Matcher:** Bash|Write|Edit.
**Performance:** unter 100ms.

### Hook-4: session_end_covelab.sh (Event SessionEnd, Matcher exit)

**Aufgabe:** Postgres-Backup-Trigger via ssh-localhost docker exec pg_dump. Host-Side-Redirect (Stolperstein T-Pattern konform).

**Implementation-Skeleton:**
```bash
#!/bin/bash
set -euo pipefail
BACKUP_DIR=/opt/covelab/backups
mkdir -p "$BACKUP_DIR"
TS=$(date -u +%Y%m%dT%H%M%SZ)
docker exec postgres pg_dump -U covelab covelab_db > "$BACKUP_DIR/covelab_db_$TS.sql"
echo "Postgres-Backup OK: $BACKUP_DIR/covelab_db_$TS.sql" >&2
exit 0
```

**Matcher:** reason=exit (nur regulaerer CLI-Exit, nicht logout/clear).
**Performance:** unter 5s (pg_dump-Laufzeit, akzeptabel fuer SessionEnd).

## H) Registrierungs-Mechanik plus Stolperstein-R/CA-Konsequenz

Hooks werden registriert via JSON-Block in .claude/settings.json:

```json
{
  "hooks": {
    "SessionStart": [
      { "hooks": [{ "type": "command", "command": "/opt/covelab/.claude/hooks/session_start_covelab.sh" }] }
    ],
    "PreToolUse": [
      { "matcher": "Bash|Write|Edit|MultiEdit",
        "hooks": [{ "type": "command", "command": "/opt/covelab/.claude/hooks/pre_tool_use_covelab.sh" }] }
    ],
    "PostToolUse": [
      { "matcher": "Bash|Write|Edit",
        "hooks": [{ "type": "command", "command": "/opt/covelab/.claude/hooks/post_tool_use_covelab.sh" }] }
    ],
    "SessionEnd": [
      { "matcher": "exit",
        "hooks": [{ "type": "command", "command": "/opt/covelab/.claude/hooks/session_end_covelab.sh" }] }
    ]
  }
}
```

**Stolperstein-R-Konsequenz:** Code-Chat darf .claude/settings*.json NICHT schreiben (R-Lehre seit Tag 5). Loesung fuer F1.6-Build:
1. Sub-3 (Code-Chat): 4 Hook-Scripts in .claude/hooks/ committen.
2. Zwischenschritt (CEO-Side, manuell): JSON-Block in /opt/covelab/.claude/settings.json einfuegen (Pattern analog CA bei AGENTS.md Tag-15-Spaet-Abend).
3. Sub-4 (Code-Chat): FAHRPLAN F1.6 fail-zu-pass plus session_log-Eintrag.

Alternative-Pattern wenn CEO-side-Eingriff vermieden werden soll: Hook-Registrierung via Frontmatter im Skill/Subagent (z.B. code-Subagent in .claude/agents/code.md). Diese Variante ist nicht-global und gilt nur fuer den jeweiligen Subagent. Trade-off: keine settings-Modifikation noetig, dafuer geringere Coverage.

## I) Anti-Drift-Hinweise

- chmod +x auf Hook-Scripts Pflicht (executable bit muss gesetzt sein, sonst silent-fail).
- shell-Profile-Echo (z.B. .bashrc Banner) NICHT in Hook-Scripts ausloesen - interactive-shell-Check via [[ $- == *i* ]].
- jq oder Python fuer JSON-Parsing (NICHT grep auf JSON-Inhalt, fragile bei Whitespace).
- Jeder Hook unter 200ms Ziel-Performance (Anthropic-Empfehlung), max 5s Hard-Cap fuer SessionEnd-Backup.
- Hook-Scripts ASCII-only Output (OneDrive-Sync plus Windows-Terminal-Kompatibilitaet).
- Hook-Scripts duerfen KEIN nested ssh ausfuehren (BP-Lehre). Postgres-Backup-Hook nutzt docker exec auf localhost.
- Hook-Output-stderr wird an Claude-Visibility geliefert bei Exit 2 - nutze klare deutsche Fehler-Messages mit Lehre-Verweis.
- Stop-Hook fuer Working-Tree-Scan ist verfuehrerisch, aber Bash-Tool-Output-Coverage-Caveat: Stop-Hook sieht nur transcript-File, nicht den State der Tool-Bash-Sessions. Backup-Logik MUSS in SessionEnd, nicht Stop.

## J) F1.6-Konstruktions-Reihenfolge

- Sub-1 (erledigt 2026-05-12): F1.6-Vor-Arbeit Snapshot best-practice-hook-implementation.md (DIESER SNAPSHOT).
- Sub-2 (Code-Chat): 4 Hook-Scripts Atomic-Commit in .claude/hooks/ plus chmod +x in Build-Script.
- Zwischenschritt (CEO manuell): settings.json hooks-Block einfuegen.
- Sub-3 (Code-Chat): empirische-Validierung mit Test-Trigger plus FAHRPLAN F1.6 fail-zu-pass plus session_log-Eintrag plus ggf neue Stolpersteine.

## K) Universal-Fundament-Verkaufs-Wert

Hook-System als 3-Schichten-Anti-Drift-Layer (Doku via AGENTS.md plus Skill-Hooks via F1.5 Subagent-Frontmatter plus deterministische Hooks via F1.6) ist konkret-zeigbare Foundation-Feature fuer Phase 5 Verkaufs-Paket. F5.2 Concepts-Doku und F5.4 Demo-Walkthrough sollten 4-Hook-Set als Out-of-the-Box-Compliance-Mechanik hervorheben. Industry-Verkaufsvorteil: deterministisches Verbots-Pfad-Blocking ist 2026 Industry-weit unter-implementiert (Stand alle Best-Practice-Recherchen Tag 10 bis Tag 16).

## L) References

- code.claude.com/docs/en/hooks-guide (Anthropic offizielle Authority, Konfig plus Beispiele)
- code.claude.com/docs/en/hooks (Anthropic offizielle Reference, Event-Matrix plus Schemas)
- claudefa.st/blog/tools/hooks/hooks-guide
- smartscope.blog/en/generative-ai/claude/claude-code-hooks-guide
- github.com/disler/claude-code-hooks-mastery (OSS-Pattern-Sammlung)
- codesignal.com Implementing-PostToolUse-Hooks-Tutorial
- blakecrosley.com Claude-Code-Hooks-Tutorial
- claudelog.com/mechanics/hooks (Mechanik plus Exit-Code-Semantik)
- patterns/best-practice-skill-activation.md (Schicht 2 Hook-Forcierung, Tag 15)
- patterns/best-practice-subagent-frontmatter.md (Schicht 3 Subagent-Preload, Tag 16)
- decisions/0010-best-practice-recherche-pflicht.md (Schicht 1.5 Coverage-Pflicht)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F1.6
- STOLPERSTEINE.md R (settings.json Verbot), CA (Permission-Layer-Drift), BR (Coverage-Pflicht), T (Postgres-Host-Side-Redirect), BP (nested-ssh-Verbot), BY/BZ (.claude/agents+hooks REPO-versioniert)
