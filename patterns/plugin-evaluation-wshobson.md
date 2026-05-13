# Plugin-Eval wshobson/claude-code-workflows

**Recherchiert:** 2026-05-12 (Tag 16 Morgen, post-F1.4-Sub-4-Install)
**Anlass:** F1.4 wshobson Multi-Agent-Coordinator-Plugin Abschluss-Bewertung. CEO-Entscheidung Sub-3: agent-orchestration alleine.
**Quellen:** reports/wshobson_marketplace_recon_f14_sub1.md (Sub-1 Marketplace-Recon, 82 Plugins komplett gelistet), reports/f14_sub4_install_20260512T065059Z.md (Sub-4 Install-Capture), patterns/best-practice-multi-agent.md (Tag-13-Anthropic-Multi-Agent-Patterns, konzeptionell), patterns/best-practice-skill-activation.md (3-Schichten-Activation).

## Marketplace-Profil

| Attribut | Wert |
|---------|------|
| Marketplace-Manifest-Name | claude-code-workflows |
| Repo-Pfad (Github) | wshobson/agents |
| Marketplace-Version | 1.6.0 |
| Owner | Seth Hobson |
| Total-Plugins | 82 |
| Total-Agents | 185 specialized |
| Total-Skills | 153 |
| Architektur-Prinzip | Granular (focused plugins, minimal token usage) |

## FAHRPLAN-Scope-Drift (wichtige Bemerkung)

Original-FAHRPLAN-Scope F1.4: "multi-agent-coordinator-Plugin installieren plus 4 Agents plus 7 Commands plus 6 Skills". Diese Erwartung basiert auf alter wshobson-Architektur (Single-Plugin-Pattern). Tag-15-Spaet-Abend-Sub-1-Recon zeigte: wshobson hat seit dem Refactor 82 granulare Plugins (jeweils fokussiert, Token-arm). Single-Plugin-Erwartung obsolet.

CEO-Entscheidung Sub-3 (Tag 16 Morgen): agent-orchestration alleine als F1.4-Realisierung. Begruendung: F1.4-Multi-Agent-Intent direkt getroffen, F1.5-Subagent-Lerngrundlage gegeben (context-manager-Agent), Token-arm (nur 1 Agent + 2 Commands).

FAHRPLAN F1.4-Block-Patch (Sub-6) ersetzt Original-Scope durch Real-Auswahl-Begruendung.

## agent-orchestration Plugin-Inhalt (Real-Zahlen, BW+BX Cache-Top-Level)

| Attribut | Wert |
|---------|------|
| Plugin-Name | agent-orchestration |
| Marketplace | claude-code-workflows |
| Version installiert | 1.2.1 |
| Category | ai-ml |
| Beschreibung | Multi-agent system optimization, agent improvement workflows, context management |
| Agents | 1 (context-manager.md) |
| Commands | 2 (improve-agent.md, multi-agent-optimize.md) |
| Skills | 0 (kein skills/-Subverzeichnis) |
| Sub-Verzeichnis-Pattern | versioniert 1.2.1/ (analog pm-skills / engineering-skills) |

## Begruendung Auswahl (Sub-2 CTO-Empfehlung an CEO)

Aus 82 Plugins zwei Top-Kandidaten gefiltert gegen F1.4-Multi-Agent-Intent plus F1.5-Subagent-Lerngrundlage:
- **agent-orchestration** (ai-ml) - Single-Agent-Pattern plus Context-Management plus Agent-Improvement-Workflows
- **agent-teams** (workflows) - Multi-Agent-Team-Orchestration plus Code-Review-Fokus

CEO-Entscheidung Sub-3: agent-orchestration alleine. Begruendung-Voll:
1. F1.4-Multi-Agent-Intent direkt getroffen (agent-orchestration ist der Coordinator-Equivalent im Refactor-Modell).
2. F1.5-Subagent-Lerngrundlage durch context-manager-Agent (Pattern-Template fuer cto.md / code.md / projektmanager.md / recherche.md).
3. Token-arm: 1 Agent + 2 Commands + 0 Skills viel kleiner als agent-teams (mehrere Agents + Code-Review-Stack).
4. agent-teams ist Code-Review-spezifisch, gehoert nicht in F1.4-Phase-1-Scope. Phase-2-Backlog.

## BQ-Bestaetigung (Marketplace-Manifest-Key-Authority)

Sub-1-Recon hatte Manifest-Key bereits dokumentiert (claude-code-workflows ist NICHT Repo-Pfad sondern Manifest-Name aus marketplace.json im Repo wshobson/agents). Plugin-Install-Syntax: agent-orchestration@claude-code-workflows. Bestaetigt via plugin install Success in Sub-4. BQ-Lehre erneut empirisch validiert.

## CB-Hinweis (NEU, siehe STOLPERSTEINE.md CB - Volltext in Sub-6)

Sub-4-Erst-Versuch zeigte: claude plugin marketplace add interpretiert Argument als Github-Repo-Pfad und faellt auf SSH-Clone zurueck. Argument MUSS OWNER/REPO sein (wshobson/agents), NICHT Manifest-Key (claude-code-workflows). Da Marketplace claude-code-workflows aus Sub-1 schon registriert war (via wshobson/agents), war Sub-4-Install dennoch erfolgreich. CB-Lehre persistiert in Sub-6.

## CoveLab-Anwendung post-F1.4

### F1.5 Subagent-Definitionen
agent-orchestration liefert context-manager-Agent als Lerngrundlage fuer CoveLab cto.md / code.md / projektmanager.md / recherche.md. Tag-16-Surfacing-Test post-Restart pending (BT-Lehre). Pattern-Template-Extraktion in F1.5 selbst.

### F1.6 Hook-System
agent-orchestration enthaelt keine Hooks direkt, aber multi-agent-optimize.md (Slash-Command) ist Lerngrundlage fuer Multi-Agent-Workflow-Triggering. Phase-2-Backlog evaluiert block-no-verify (wshobson-security-Plugin) als konkretes PreToolUse-Hook-Beispiel.

### F2.4 claude-CLI auf VPS
Plugin-Install aktuell client-side (Windows, BY). Nach F2.4 ist VPS-side-Plugin-Install moeglich. agent-orchestration-Migration nach VPS-side dann via plugin install Wiederholung im VPS-context.

## Phase-2-Backlog (weitere wshobson-Plugins evaluiert, nicht installiert)

- **agent-teams** (workflows) - Multi-Agent-Team-Orchestration. Hohe Phase-2-Relevanz fuer parallel CoveLab-Subagent-Operations.
- **context-management** (ai-ml) - Context persistence, restoration, long-running conversation management. Komplementaer zu agent-orchestration context-manager-Agent.
- **block-no-verify** (security) - PreToolUse-Hook-Pattern. Direkte F1.6-Lerngrundlage als konkretes Hook-Beispiel.
- **conductor** (workflows) - Context-Driven-Development-Workflow Context-Spec-Implement. Vorsicht: potenzieller Konflikt mit FAHRPLAN-Authority, pruefen bevor adopt.
- **plugin-eval** (quality) - Three-layer quality evaluation framework mit Elo-Ranking. Meta-relevant fuer eigene plugin-Evaluation-Snapshots.
- **review-agent-governance** (governance) - Human-approval-signal fuer AI-Agent-PR-Reviews. F2.4-Push-Approval-Lerngrundlage.

## Universal-Fundament-Verkaufs-Wert

Multi-Agent-Orchestration ist Industry-weit wachsendes Feld. wshobson-Marketplace mit 82 Plugins / 185 Agents / 153 Skills bietet breite Auswahl. Universal-Fundament-Anwender koennen via SKILL_INDEX-Lookup-Workflow (F1.3b) gezielt wshobson-Plugins nach Use-Case auswaehlen, statt monolithisches Multi-Agent-System nutzen zu muessen. CoveLab demonstriert die Auswahl-Disziplin: 82 Plugins verfuegbar, 1 ausgewaehlt nach klaren Kriterien (Token-arm + Phase-Scope-passend + Lern-Grundlage-Eignung).

## References

- reports/wshobson_marketplace_recon_f14_sub1.md (Sub-1 Marketplace-Recon)
- reports/f14_sub4_install_20260512T065059Z.md (Sub-4 Install-Capture)
- patterns/best-practice-multi-agent.md (Tag-13 Anthropic-Multi-Agent, konzeptionell)
- patterns/best-practice-skills-plugins.md (Marketplace-Manifest-Authority-Mechanik)
- patterns/best-practice-skill-activation.md (3-Schichten Activation, F1.5+F1.6-Lerngrundlage)
- patterns/plugin-evaluation-alirezarezvani.md (paralleler Plugin-Eval, Strukturanalog)
- patterns/plugin-evaluation-anthropic-official.md
- STOLPERSTEINE.md BQ (Marketplace-Manifest-Key), CB (NEU, SSH-Clone-Fallback bei falschem add-Argument - Volltext in Sub-6)
- decisions/0010-best-practice-recherche-pflicht.md
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F1.4-Block (post-Sub-6-Update)
- docs/SKILL_INDEX.md (Sub-Skill-Hierarchie-Pattern fuer agent-orchestration nach Surfacing-Test)
