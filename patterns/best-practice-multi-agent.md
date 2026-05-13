# Best-Practice-Snapshot: Multi-Agent-Patterns in Claude Code 2026

**Recherchiert:** 2026-05-10 (Tag 13 Spaetnachmittag, CTO-Chat)
**Anlass:** F10 Direct-Communication-Architektur + zukuenftiger Projektmanager-Subagent
**Quellen:** code.claude.com/docs/sub-agents, mindstudio.ai workflow-patterns + agent-teams-vs-sub-agents + parallel-agents, shipyard.build/blog/claude-code-multi-agent (Maerz 2026), addyosmani.com/blog/code-agent-orchestra, github.com/wshobson/agents, github.com/VILA-Lab/Dive-into-Claude-Code

## Anthropic-Offiziell: 5 Composable Workflow-Patterns
1. Prompt Chaining (sequenziell)
2. Routing (Task an passenden Worker)
3. Parallelization (split-and-merge)
4. Orchestrator-Workers (Manager + Subagents) — Claude-Code-Default
5. Evaluator-Optimizer (Output + Judge-Loop)

## Sub-Agent-Pattern (klassisch hierarchisch)
- 1 Orchestrator + N Worker-Subagents
- Subagents kommunizieren NUR durch Orchestrator
- Subagent fresh Context, returns final message zum Parent
- Max 10 parallel Subagents (split-and-merge)
- Max 5 nesting depth (default 1, recommended 2)
- Built-in Subagents: Explore (read-only), Plan (research), General-purpose (multi-step)
- Custom-Subagents in .claude/agents/NAME.md (YAML-Frontmatter + Markdown-Body)

## Agent Teams (NEU, experimental, Claude Code v2.1.32+)
- 1 Team-Lead + N Teammates
- Teammates kommunizieren DIREKT untereinander (nicht nur durch Lead)
- Shared task list als Coordination-Primitive
- Built-in seit Q1 2026

## Drei Tiers Multi-Agent-Tooling 2026
- Tier 1: Built-in Subagents + Agent Teams (Single Terminal, Claude Code intern)
- Tier 2: Local Multi-Agent Orchestration (Conductor, Vibe Kanban, Gastown, Claude Squad, OpenClaw + Antfarm)
- Tier 3: Cloud Background Agents (Claude Code Web, GitHub Copilot Coding Agent, Jules by Google, Codex Web)

## Subagent-Definition-Felder (vollstaendig 2026)
- skills (preloaded)
- mcpServers (server names oder inline configs)
- hooks (lifecycle: PreToolUse, PostToolUse, Stop)
- memory (user / project / local scope)
- background (true / false)
- effort (low / medium / high / max)
- isolation (worktree fuer git-getrennten Workspace)

## CoveLab-Empfehlung
- Sofort: Subagent-Definitionen in .claude/agents/ (cto.md / code.md / projektmanager.md / recherche.md)
- Nutzen: Tier-1-Built-in-Subagents als Foundation
- Verwerfen aktuell: Tier 2 (Komplexitaets-Drift) und Tier 3 (zu fruehe Cloud-Abhaengigkeit), als Backlog dokumentiert
- Coordination via Shared-Task-List in COORDINATION.md (Phase 2 F16)

## Bessere Optionen aktuell nicht verwendet (dokumentiert fuer spaeter)
- **Agent Teams** als Hauptarchitektur statt Subagents: experimental, noch nicht stabil. Backlog Phase 5+
- **Conductor / Vibe Kanban** (Tier 2 visueller Manager): Komplexitaet, Vendor-Layer
- **OpenClaw + Antfarm** (Tier 2 Multi-Agent-Layer): erweitert Claude Code, Komplexitaets-Drift
- **Cloud Background Agents** (Tier 3): zu fruehe Cloud-Abhaengigkeit, kein lokaler Backup-Pfad
- **Meta-Agents** (Sub-Orchestrator von Sub-Orchestratoren): "turtles all the way down" — fuer 50+ parallele Agents, Overkill fuer 2-3-Agent-Setup

## Folge-Aktionen
- Phase 1 F13: Subagent-Definitionen erstellen
- Phase 2 F17: Direct-Communication via Headless claude -p (MVP) plus Subagent-Layer
- Phase 3 (post-Lock): ggf. Tier 2 Tools fuer Skalierung evaluieren
