# Best-Practice-Snapshot: Direct Agent-to-Agent Communication

**Recherchiert:** 2026-05-10 (Tag 13 Nachmittag, CTO-Chat)
**Anlass:** F9-Vision (CEO ohne Bridge-Rolle), Vorbereitung F10 Direct-Communication-Architektur
**Quellen:** Anthropic Engineering Blog Building-Agents-with-Claude-Agent-SDK (Jan 2026), code.claude.com/docs Subagents, qubittool.com Claude-Code-Guide, mindstudio.ai Workflow-Patterns, augmentcode.com Claude-Code-vs-SDK, arxiv 2604.14228 Dive-into-Claude-Code

## Anthropic-Offiziell: 5 Composable Workflow-Patterns

1. Prompt Chaining (sequenziell)
2. Routing (Task an passenden Worker)
3. Parallelization (split-and-merge)
4. Orchestrator-Workers (Manager + Subagents) - Claude-Code-Default
5. Evaluator-Optimizer (Output + Judge-Loop)

Claude Code nutzt primaer Orchestrator-Workers (siehe Subagents).

## Architektur-Optionen fuer CTO ↔ Code-Chat ohne CEO-Bridge

### Option A: Claude Agent SDK Orchestrator auf VPS
- claude_agent_sdk Python (oder TypeScript), Headless-Process auf Cockpit oder VPS
- ClaudeSDKClient mit options (system_prompt, allowed_tools, permission_mode, max_turns)
- Subagents in .claude/agents/-Markdown-Files definiert (oder programmatisch)
- Built-in Subagents: Explore (read-only), Plan (research), General-purpose (multi-step)
- Eigene Context-Window pro Subagent, nur final message zurueck zum Parent
- Mixed-Model-Pattern (Anthropic intern): Opus als Orchestrator + Sonnet als Subagents (Kosten-Optimierung)
- Effort: hoch (SDK-Lernkurve, Setup, Test-Loop) - geschaetzt 8-12h
- Skaliert am besten

### Option B: Headless claude -p mit File-Trigger
- claude -p "task" --allowedTools "Bash,Read,Write" --permission-mode acceptEdits --output-format json
- Trigger via cron oder inotify (FS-Event)
- Task-Files unter /opt/covelab/tasks/ (post-F9-Teil-1 rw-Mount)
- Output zurueck nach /opt/covelab/tasks/results/
- Effort: niedrig (1-2h Setup mit bekannten Tools)
- Liefert ~80% des Werts fuer ein-Schritt-Tasks
- Begrenzung: kein Multi-Agent-Orchestration

### Option C: Custom-MCP-Server task_executor
- Neuer MCP-Server exposed Tools wie execute_task, get_status
- CTO ruft Tool auf, MCP-Server triggert Code-Chat-Job intern
- Voll-asynchron, sauber abstrahiert
- Effort: mittel-hoch (neuer Container, Maintenance, weiteren MCP-Server)
- Komplexitaets-Drift-Risiko (R7-2)

### Option D: CLAUDE.md + Hooks
- CLAUDE.md im Repo definiert Workflow-Konventionen (auto-loaded by Claude Code)
- Hooks: event-basierte Code-Ausfuehrung (PostCommit-Backup, PreToolUse-Validation etc)
- Komplementaer zu A / B / C, kein eigenes Direct-Communication-Pattern

## Anthropic-Recommendations fuer headless-Automatisierung

- Granting minimum necessary permissions (allowed_tools-Restriction)
- Preferring reversible actions ueber destructive
- Building human approval checkpoints fuer high-stakes decisions
- Subagent-Boundaries: kein infinite nesting (Subagents koennen keine Subagents spawnen)

## CoveLab-Empfehlung

MVP: Option B (Headless claude -p mit File-Trigger). Begruendung:
- Niedrigste Hurde, nutzt vorhandene Tools
- Liefert ~80% Wert in 1-2h Setup
- Erlaubt Lernen und Iteration ohne SDK-Investment

Ziel-Architektur: Option A (Claude Agent SDK Orchestrator). Begruendung:
- Kanonische Anthropic-Loesung
- Skaliert mit wachsender Task-Komplexitaet
- Mixed-Model-Pattern senkt Kosten (Opus orchestriert, Sonnet workt)

Begleit-Pattern: Option D (CLAUDE.md + Hooks) - sofort einsetzbar, komplementaer.

Verworfen: Option C - Komplexitaets-Drift ohne klaren Mehrwert ueber A / B.

## Pflicht-Anpassungen fuer CoveLab vor Implementation

- F9-Teil-1 (Mount /mnt/repo rw) ist Voraussetzung fuer File-Trigger-Pattern (Option B)
- Anthropic-Approval-Checkpoint-Recommendation passt zu R7-Prio-1 (Informationsverlust): destructive Operationen (rm, DROP, push) bleiben CEO-only auch in Option A / B
- Subagent-Definitionen in .claude/agents/ muessen ADR 0005 Permission-Architektur respektieren

## Folge-Aktionen

- F10 in FUNDAMENT_PLAN.md aufnehmen (Direct-Communication-Architektur)
- ADR 0011 (post-F10): Pattern-Auswahl + Implementation-Plan B → A
- F10 depends F9 (rw-Mount Voraussetzung)
