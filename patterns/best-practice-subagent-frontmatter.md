# Best-Practice-Snapshot: Subagent-Frontmatter in Claude Code 2026

**Recherchiert:** 2026-05-12 (Tag 16 Vormittag, F1.5-Vor-Arbeit)
**Anlass:** F1.5 Subagent-Definitionen (4 Markdown-Files in /opt/covelab/.claude/agents/) braucht operativ-detaillierte Frontmatter-Syntax plus skills-Preload-Feld-Format. BR-Coverage-Pflicht (ADR 0010 Schicht 1.5).
**Quellen:** code.claude.com/docs/en/sub-agents (Anthropic offiziell, vollstaendig gefetched), Medium Sathish Raju Complete Guide April 2026, Pubnub Blog Best Practices, Agensi SKILL.md-Reference, Github Issue 8501 color-Feld, Anthropic Skills-Engineering Blog, patterns/best-practice-skill-activation.md (Tag 15 Schicht 3).

## Subagent-Konzept

Subagent ist isolierte AI-Assistant-Instanz mit eigenem Context-Window, eigenem System-Prompt, restringiertem Tool-Set, eigenem Model. Wird vom Main-Conversation via Agent-Tool delegiert (auto via description-Match oder explizit via /agent-Befehl oder @-mention).

Vorteile (Anthropic offiziell):
- Preserve Context: Exploration und verbose-Output isoliert
- Enforce Constraints: Tool-Restriction pro Subagent
- Reuse Configurations: User-Level cross-project, Project-Level team-shared
- Specialize Behavior: Focused System-Prompt pro Domain
- Control Costs: Tasks an Haiku/Sonnet routen statt Opus

## File-Format

YAML-Frontmatter plus Markdown-System-Prompt:

```yaml
---
name: subagent-name
description: Trigger-phrases plus use-proactively-Hinweise
tools: Read, Grep, Glob, Bash
model: inherit
skills:
  - skill-name-1
  - skill-name-2
---

System-Prompt im Markdown-Body. Wird beim Subagent-Spawn als System-Prompt geladen.
```

## Frontmatter-Felder komplett (Anthropic Reference)

| Feld | Required | Beschreibung |
|------|----------|--------------|
| name | yes | Unique lowercase identifier mit hyphens. Filename muss NICHT matchen. Hooks bekommen value als agent_type. |
| description | yes | Wann Claude delegieren soll. Trigger-Phrasen. use-proactively fuer Auto-Routing. |
| tools | no | Allowlist. Inherit all wenn omitted. Skill-Tool fuer Skill-Invocation, Agent-Tool fuer Sub-Spawning. |
| disallowedTools | no | Denylist. Disallow wins gegen tools-Allowlist. |
| model | no | sonnet/opus/haiku/inherit oder full ID (claude-opus-4-7). Default inherit. |
| permissionMode | no | default/acceptEdits/auto/dontAsk/bypassPermissions/plan. Parent-Mode kann override blocken. |
| maxTurns | no | Hard-Stop nach N agentic Turns. |
| skills | no | PRELOAD-Feld - full skill content im Context bei Subagent-Start. NICHT nur description. |
| mcpServers | no | Inline-Definition oder String-Ref. Inline = Subagent-only, String = shared mit Parent. |
| hooks | no | Lifecycle-Hooks (PreToolUse/PostToolUse/Stop -> SubagentStop). |
| memory | no | user/project/local fuer persistent cross-session Knowledge-Base. |
| background | no | true/false fuer concurrent vs blocking. |
| effort | no | low/medium/high/xhigh/max. Override Session-Effort. |
| isolation | no | worktree fuer isolated git-worktree-Copy. |
| color | no | red/blue/green/yellow/purple/orange/pink/cyan fuer UI-Display. |
| initialPrompt | no | Auto-submit als erster User-Turn bei main-agent-Modus (--agent). |

## skills-Preload-Feld (3-Schichten-Activation Schicht 3)

KEY-FEATURE fuer Skill-Activation-Sicherstellung. Anthropic offiziell-Zitat:

> The full content of each listed skill is injected into the subagent context at startup. ... Subagents can still invoke unlisted project, user, and plugin skills through the Skill tool.

Pattern:

```yaml
skills:
  - senior-pm
  - senior-architect
  - adversarial-reviewer
```

100% Skill-Activation innerhalb Subagent-Session (patterns/best-practice-skill-activation.md Schicht 3). Industry-Standard ueber 95% mit Hook (Schicht 2), 100% mit Subagent-Preload (Schicht 3).

CoveLab-Konsequenz: jeder F1.5-Subagent (cto, code, projektmanager, recherche) bekommt skills-Feld mit relevanten Plugin-Skills aus docs/SKILL_INDEX.md.

## Location und Prioritaet

| Pfad | Scope | Prioritaet | Versionierung |
|------|-------|------------|---------------|
| managed-settings | Org | 1 (hoechste) | Admin-deployed |
| --agents CLI flag | Session | 2 | Inline JSON, nicht persistent |
| .claude/agents/ | Project | 3 | Git-versioniert (RECOMMENDED) |
| ~/.claude/agents/ | User | 4 | User-Profile |
| plugin agents/ | Plugin | 5 (niedrigste) | Read-only |

Project-Subagents (.claude/agents/) sind RECOMMENDED fuer Team-Sharing. CoveLab-Vision (UNIVERSAL_NORDSTERN VPS-Single-Source): /opt/covelab/.claude/agents/ ist authoritative Quelle, via Git versioniert (BY/BZ-Lehre).

## Description-Engineering (Activation-Pattern)

Aus Schicht 1 patterns/best-practice-skill-activation.md plus Anthropic offiziell:

- Specific Trigger-Phrases, nicht vage Rolle
- use proactively fuer Auto-Routing
- Imperative Sprache (ALWAYS, MUST, NEVER)
- Negative Constraints (Do NOT use for X)

Anthropic-offizielles Beispiel:
description: Expert code review specialist. Proactively reviews code for quality, security, and maintainability. Use immediately after writing or modifying code.

## Tools-Restriction (Sicherheits-Layer)

CoveLab-Permission-Architektur (ADR 0005 plus CA-Lehre): pro Subagent restriktiv. Pattern:

```yaml
tools: Read, Grep, Glob
disallowedTools: Write, Edit, Bash
```

Anthropic-Reference: Wenn tools omitted = inherit all (inkl MCP). Allowlist plus Denylist kombinierbar (Disallow wins).

## Hook-Pattern (F1.6-Vorbereitung)

Subagent-Frontmatter-Hooks ODER Settings-Project-Hooks:

```yaml
hooks:
  PreToolUse:
    - matcher: Bash
      hooks:
        - type: command
          command: ./scripts/validate-bash.sh
```

Frontmatter-Stop wird automatisch zu SubagentStop konvertiert. Hooks-Field-Limitation: Plugin-Subagents unterstuetzen KEINE Hooks/mcpServers/permissionMode.

## CoveLab-Anwendung F1.5

Vier Subagent-Files in /opt/covelab/.claude/agents/:

| Subagent | Role | tools | model | skills (Preload-Vorschlag) |
|----------|------|-------|-------|----------------------------|
| cto | Lead Architect (Plan+Recherche) | Read, Grep, Glob, WebSearch, WebFetch | inherit | senior-architect, senior-prompt-engineer, adversarial-reviewer |
| code | Executor (Build+Commit) | Read, Write, Edit, Bash, Grep, Glob, Skill | inherit | code-reviewer, tdd-guide, senior-devops |
| projektmanager | Drift-Detector + Lock-Compliance | Read, Grep, Glob | haiku | senior-pm, adversarial-reviewer |
| recherche | Best-Practice-Researcher | Read, WebSearch, WebFetch, Grep | inherit | senior-prompt-engineer, tech-stack-evaluator |

description-Pattern fuer Auto-Routing (CTO-Vorschlag):
- cto: Lead architect for plan-and-recherche tasks. Use proactively for architecture decisions, ADR drafts, F-step specifications, build-prompt preparation. Do NOT use for direct code-writing or commits.
- code: Executor for build-and-commit tasks. Use proactively for SSH-based VPS operations, atomic commits, multi-file patches. Do NOT use for strategic decisions or web-research.
- projektmanager: Drift detector for lock-compliance and phase-gate validation. Use proactively before commits, plan-vs-state verification, F-Schritt-Status-Pruefung.
- recherche: Best-practice researcher with WebSearch and WebFetch. Use proactively for ADR-drafts and F-step-specs requiring industry-snapshot. Schicht 1-3 Recherche pro ADR 0010.

## F1.5-Konstruktions-Reihenfolge

CTO-Empfehlung: 4-File-Atomic-Commit als logische Einheit (alle 4 Subagents Phase-1-Konvergenz). Pattern:
1. cto.md
2. code.md
3. projektmanager.md
4. recherche.md

Alle 4 Files via Python-Build-Script (BS-Default bei mehreren Files).

Post-Commit: Session-Restart Pflicht (BT-Lehre) fuer Surfacing-Test. Code-Chat sieht Subagents im /agents-Befehl plus autonomous-Activation testbar.

## Anti-Drift-Hinweise

- name: lowercase plus hyphens, KEINE Underscores
- description: Trigger-Phrasen UND use-proactively-Hinweis UND negative-Constraints
- Keine Apostrophe im YAML-Inhalt (Q-Lehre bei kuenftigen Patches)
- Umlaute via ae/oe/ue im YAML-Description (Q-Lehre)
- model inherit als Default - explizit haiku nur fuer cost-sensitive Read-Only-Subagents (projektmanager)
- isolation worktree fuer code-Subagent NICHT setzen - wir wollen direkte VPS-Edits, kein git-worktree-Branch

## CoveLab-Universal-Fundament-Verkaufs-Wert

Skill-Aktivierung 3-Schichten (Doku F1.3b SKILL_INDEX plus Hook F1.6 plus Preload F1.5) ist Industry-weit ungeloestes Problem (siehe patterns/best-practice-skill-activation.md). CoveLab-Universal-Fundament mit eingebauter Subagent-Preload-Pipeline ist konkretes Verkaufs-Argument fuer Phase-5-Verpackung. F5.3 Customization-Doku und F5.4 Demo-Walkthrough sollten Subagent-Definitionen als Foundation-Feature hervorheben.

## References

- code.claude.com/docs/en/sub-agents (Anthropic offizielle Authority, vollstaendig CTO-gefetched 2026-05-12)
- medium.com Sathish Raju Claude Code Subagents Complete Guide April 2026
- pubnub.com Best Practices Claude Code Sub-Agents August 2025
- agensi.io SKILL.md Format Reference 2026-05
- github.com/anthropics/claude-code/issues/8501 (color-Feld in /agents-Generator nicht in offizieller Doku)
- anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills (Skills-Engineering)
- patterns/best-practice-skill-activation.md (Schicht 3 Subagent-Preload, Tag 15)
- patterns/best-practice-multi-agent.md (konzeptionell, Tag 13, ergaenzt durch dieses Snapshot)
- patterns/best-practice-projektmanager.md (konzeptionell, Tag 13, ergaenzt durch dieses Snapshot)
- patterns/plugin-evaluation-wshobson.md (context-manager-Lerngrundlage, Tag 16)
- docs/SKILL_INDEX.md (Skill-Auswahl-Quelle fuer skills-Preload-Feld)
- decisions/0010-best-practice-recherche-pflicht.md (Schicht 1.5 Coverage)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F1.5+F1.6
- STOLPERSTEINE.md Q (Apostroph plus Umlaut), BT (Session-Restart), BY/BZ (.claude/agents Repo-Files), CA (settings-Drift)
