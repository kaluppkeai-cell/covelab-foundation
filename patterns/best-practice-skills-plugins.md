# Best-Practice-Snapshot: Claude Code Plugin-Marketplace + Skills + MCP-Server

**Recherchiert:** 2026-05-10 (Tag 13 Spaetnachmittag, CTO-Chat)
**Anlass:** Phase 1 F12 (Plugin-Marketplace-Setup, "Professionelle Helfer")
**Quellen:** code.claude.com/docs/discover-plugins, github.com/anthropics/claude-plugins-official, claudemarketplaces.com (4200+ Skills, 770+ MCP-Server, 2500+ Marketplaces), skillsmp.com (1.2M+ Skills aggregiert), agensi.io/learn

## Markt-Stand Mai 2026
- 4200+ Skills, 770+ MCP-Server, 2500+ Marketplaces (claudemarketplaces.com)
- Plugin-System productive seit Q1 2026
- Anthropic Official Marketplace mit Auto-Update Default
- Community-Marketplaces: jeremylongshore (425 plugins, 2810 skills, 200 agents), wshobson (80 plugins, 153 skills), alirezarezvani (232+ skills inkl. C-Level + PM)

## Plugin-Struktur (vollstaendig 2026)
- .claude-plugin/plugin.json (Manifest)
- .mcp.json (MCP server config)
- commands/ (slash commands)
- agents/ (Subagent-Definitionen)
- skills/ (SKILL.md files)
- hooks/ (Lifecycle-Event-Scripts)

## Konkrete Empfehlungen fuer CoveLab Phase 1 F12
1. **Anthropic Official Marketplace** (claude-plugins-official, Auto-Update default): commit-commands, basics
2. **alirezarezvani/claude-skills**: pm-skills (6) + c-level-skills (28) + engineering-skills (49) + engineering-advanced (25 POWERFUL) + ra-qm-skills (12 regulatory)
3. **wshobson/agents**: Multi-Agent-Coordinator-Plugin (4 Agents + 7 Commands + 6 Skills) plus 16 Workflow-Orchestrators
4. **shanraisshan/claude-code-best-practice**: Hooks-Templates (PreToolUse, PostToolUse, SessionStart-Sounds), /health-Command fuer Project-Hygiene-Audit

## Bessere Optionen aktuell nicht verwendet (dokumentiert fuer spaeter)
- **Agensi Pro** (MCP-Connection statt Manual-Install): Live-Skill-Access, Security-gescannt, Agent waehlt Skills on-demand. Vorteil kein Manual-Management. Nachteil Vendor-Abhaengigkeit + Preis. Backlog Phase 5+
- **LiteLLM Plugin Marketplace** (Self-hosted Marketplace): fuer Enterprise-Deployment einer eigenen kuratierten Marketplace. Backlog post-Lock-Release wenn CoveLab-Foundation als Produkt verkauft wird
- **OpenClaw + Antfarm** (Tier 2 Multi-Agent): erweitert Claude Code mit Multi-Agent-Layer. Komplexitaets-Drift, Backlog
- **Conductor / Vibe Kanban** (Tier 2 Multi-Agent UI): visueller Multi-Agent-Workflow-Manager. Backlog
- **ccrecall + mcp-sqlite-tools + mcp-omnisearch** (Scott-Spence-Setup): SQLite-basierte Conversation-History plus Live-Search. Interessant fuer Stolperstein-Datenbank, Backlog Phase 4
- **jeremylongshore/claude-code-plugins-plus-skills**: 425 Plugins / 2810 Skills / 200 Agents. Sehr breit aber Quality varies. Selektive Picks moeglich, Backlog Phase 4

## Skills vs Plugins vs MCP (3 Install-Methoden)
- Manual: SKILL.md in ~/.claude/skills/ (custom)
- Plugin: /plugin install NAME@MARKETPLACE (community + marketplace)
- MCP-Connection: Agent zieht on-demand (Agensi Pro)
- Alle drei koexistieren ohne Konflikt

## Folge-Aktionen
- Phase 1 F12 als erster Bau-Schritt Tag 14 morgens
- Reihenfolge Installation: Anthropic Official → alirezarezvani PM-Skills → wshobson Multi-Agent-Coordinator → shanraisshan Hooks-Templates
- Per-Plugin Test-Run + Snapshot in patterns/plugin-evaluation-NAME.md (ADR-0010-Pflicht)


## Update 2026-05-11 (Tag 15 Nachmittag, post-F1.3-Run)

Konkretisierung und Korrekturen nach F1.3-Voll-Bau-Recherche (web_search docs.claude.com plus alirezarezvani README plus marketplace.json):

**Marketplace-Manifest-Key-Authority (vorher nicht abgedeckt):** Jeder Marketplace hat .claude-plugin/marketplace.json mit name-Feld - dieser Name ist authoritative Install-Key fuer NAME at KEY-Syntax. Repo-Owner-Wahl, NICHT zwingend identisch mit Github-Repo-Pfad. Beispiel: alirezarezvani/claude-skills (Repo) zu claude-code-skills (Manifest-Key). Pflicht-Verifikation via claude plugin marketplace list nach Add. Stolperstein-Referenz BQ.

**Skill-Zahlen-Aktualisierung alirezarezvani:**
- engineering-skills: 24 core (war 49 im aelteren Snapshot) - architecture / design-patterns / refactoring / testing
- engineering-advanced-skills: 25 POWERFUL-tier - agent-designer / agent-workflow-designer / MCP-server-builder / spec-driven-workflow / RAG-architect / secrets-vault-manager / dependency-auditor / llm-cost-optimizer / prompt-governance / observability-designer / performance-profiler (CoveLab-Phase-2-relevant)
- pm-skills: 6 (Match)
- marketing-skills: 43-44, c-level-skills: 28-34, ra-qm-skills: 12-14, product-skills: 12-16, business-growth-skills: 4-5, finance-skills: 2-4 (Skill-Zahlen variieren leicht je nach Quelle, Marketplace im Wachstum)
- Standalone-Plugins (10 Stueck): pw / self-improving-agent / google-workspace-cli / executive-mentor / content-creator / demand-gen / fullstack-engineer / aws-architect / product-manager / skill-security-auditor - Subsets der Bundle-Plugins

**Reserved-Marketplace-Namen Anthropic:** claude-code-marketplace, claude-code-plugins, claude-plugins-official, anthropic-marketplace, anthropic-plugins, agent-skills, knowledge-work-plugins, life-sciences. Namen die official-Anthropic impersonate (zB official-claude-plugins) sind geblockt.

**CoveLab-Konsequenz fuer F1.4 wshobson:** Marketplace-Key nicht agents annehmen. Pattern: claude plugin marketplace add wshobson/agents -> claude plugin marketplace list -> Manifest-Key ablesen -> Plugin-Install mit korrektem Key.

**Phase-2-Backlog erkannt:** engineering-advanced-skills hat hohe CoveLab-Relevanz fuer F1.5 (Subagent-Definitionen) und F2.1 (MCP-rw-Mount). Re-Evaluation an diesen F-Schritten.
