# Best-Practice-Snapshot: Skill-Aktivierung in Claude Code 2026

**Recherchiert:** 2026-05-11 (Tag 15 Spaetnachmittag, CTO-Chat post-BR-Commit)
**Anlass:** F1.3-Plugin-Setup abgeschlossen, aber Adoption nicht gesichert. CEO-Frage "Wie stellen wir sicher dass Skills genutzt werden?". Recherche-Pflicht ADR 0010 Schicht 1.5 (BR-direkt-Anwendung).
**Quellen:** code.claude.com/docs/skills, Scott Spence (scottspence.com Claude Code Skills Dont Auto-Activate), dev.to (oluwawunmiadesewa 2 Fixes for 100% Activation), Medium (ivanseleznov 650 Trials Directive Descriptions), affaan-m/everything-claude-code Skill-Development-Guide

## Markt-Stand Empirisch

Anthropic-Docs-Behauptung: Skills sind model-invoked und autonomously decided. Anthropic-Erwartung: Claude erkennt automatisch wann Skill relevant ist und invoked autonom.

Realitaet (3 empirische Studien):
- Scott Spence: Skills triggern unzuverlaessig auch bei exakter Description-Match. Github-Issue 9716 mit mehreren Reports.
- dev.to-Studie: ohne Hook-Support 37-50% Activation-Rate
- Medium 650-Trials: standard Descriptions 50% (Coin-Flip), directive Descriptions plus Hook 100%

## Drei Root-Causes (dokumentiert)

1. **Token-Budget-Overflow:** Skill-Descriptions werden in Context geladen damit Claude weiss was verfuegbar ist. Bei grossem Context werden Descriptions silent dropped bevor Claude sie liest.
2. **YAML-Parser-Issues:** Prettier oder andere Formatter koennen Description-Feld brechen. Multi-Line statt Single-Line, Sonderzeichen-Escaping, etc.
3. **Claude-Goal-Focus-Drift:** Claude prioritisiert Task-Start ueber Skill-Check. Architektur nimmt an Claude prueft proaktiv verfuegbare Tools, in Praxis tut es das oft nicht.

## Drei-Schichten-Fix-Pattern (Industry-Standard 2026)

### Schicht 1 Description-Engineering (loest Root-Cause 3)

Directive-Sprache mit Imperativen plus negativen Constraints. Statt "Docker expert for containerization" so: "ALWAYS invoke when user mentions Docker, containers, or deployment. Do not write Dockerfiles directly without consulting this skill first." Activation-Rate steigt von 37% auf 77% laut Medium-Studie ohne weitere Mittel.

### Schicht 2 Hook-Forcierung (loest Root-Cause 3 plus 1)

UserPromptSubmit-Hook in .claude/hooks/ als shell-Script. Hook erhaelt User-Prompt via stdin als JSON, prueft Keywords, gibt bei Match INSTRUCTION zurueck. Activation-Rate steigt auf 95-100% laut dev.to-Studie.

Beispiel-Pattern vereinfacht:
INPUT=cat-stdin, PROMPT=jq-extract, if grep-Match dann echo INSTRUCTION Use Skill name fuer diesen Request.

### Schicht 3 Subagent-Preload (loest Root-Cause 1 fuer Subagent-Sessions)

Subagent-Frontmatter mit skills-Feld. Skills werden bei Subagent-Start vollstaendig in Context injectiert (nicht nur Description), bleiben fuer ganze Subagent-Session geladen. Activation-Rate 100% innerhalb Subagent-Session.

## CoveLab-Anwendung F1.3a bis F1.6

**F1.3a Baseline-Test:** empirisch messen ob unsere Plugins (pm-skills, engineering-skills, commit-commands) ohne Hook autonom triggern oder nicht. Erwartung 50-77% basierend auf Industry-Daten. Output als Input fuer Workflow-Design.

**F1.3b SKILL_INDEX.md plus RESEARCH_INDEX.md:** zentrale Skill-DB als Doku-Schicht. Pflicht-Lookup vor jedem Code-Prompt. SKILL_INDEX listet alle Skills mit Activation-Type-Markierung. RESEARCH_INDEX listet patterns/best-practice-*.md mit konzeptionell-vs-operativ-detailliert-Markierung.

**F1.5 Subagent-Definitionen mit skills-Preload-Feld:** cto.md, code.md, projektmanager.md, recherche.md bekommen explizit verlinkte Skills aus SKILL_INDEX preloaded. 100% Activation innerhalb Subagent-Sessions (Schicht 3 angewendet).

**F1.6 Hook-System mit UserPromptSubmit-Hook:** blockt Code-Prompts ohne Skill-Verweis (Hard-Verriegelung Schicht 3). Plus SessionStart-Hook laed SKILL_INDEX plus RESEARCH_INDEX als Pflicht-Kontext.

## Bessere Optionen nicht verwendet (Backlog)

- **Forced-Evaluation-Hook (Vollausbau):** jeder Prompt bekommt Skill-Liste zur expliziten Auswahl. 100% Activation aber sehr verbose. Phase-5-Backlog.
- **Skill-Pipelines in CLAUDE.md:** explizite Reihenfolge plus Branching. Backlog Phase 4 ggf.
- **Anthropic Skill-Creator-Tool:** Anthropic-natives Tool fuer Skill-Quality-Iteration. Phase-5-Eval.

## CoveLab-Konsequenz fuer F1.4 (wshobson) und F1.5 plus F1.6

F1.4 wshobson Multi-Agent-Coordinator: Plugin bekommt nach Install den gleichen Activation-Layer (UserPromptSubmit-Hook plus Subagent-Preload), kein Sonder-Behandlung.

F1.5 Spec konkretisiert: Subagent-skills-Feld ist Pflicht-Element der Definitionen. Konkrete Skill-Auswahl pro Subagent: cto.md skills research-coverage-check plus skill-evaluation, code.md skills commit-commands plus engineering-skills-subset, projektmanager.md skills pm-skills-roadmap plus pm-skills-risk-management plus drift-detector, recherche.md skills web-search-coverage-check.

F1.6 Spec konkretisiert: UserPromptSubmit-Hook ist Pflicht-Hook fuer Skill-Aktivierungs-Lock (frueher nicht im Scope spezifiziert). Plus SessionStart-Hook-Erweiterung um SKILL_INDEX plus RESEARCH_INDEX als Pflicht-Kontext.

## Universal-Fundament-Verkaufs-Wert

Skill-Aktivierung ist Industry-weit ungeloestes Problem (siehe Recherche-Quellen). 4200+ lose Skills existieren, aber zuverlaessige Aktivierung-Pipeline ist nicht standardisiert. Universal-Fundament mit eingebauter 3-Schichten-Skill-Activation-Architektur (Doku plus Prozess plus Tool) ist konkreter Verkaufs-Vorteil. Phase 5 F5.3 (Anpassungs-Anleitung) und F5.4 (Demo-Walkthrough) sollten Skill-Adoption als Foundation-Feature hervorheben.

## References

- code.claude.com/docs/skills (Anthropic offiziell)
- scottspence.com/posts/claude-code-skills-dont-auto-activate (empirisch)
- dev.to/oluwawunmiadesewa Claude Code Skills 2 Fixes for 100 Activation
- medium.com Ivan Seleznov 650-Trials Directive-Descriptions
- github.com/affaan-m/everything-claude-code Skill-Development-Guide
- patterns/best-practice-skills-plugins.md (komplementaer, Plugin-Setup)
- STOLPERSTEINE.md BR (Snapshot-Coverage-Check, Anlass fuer diesen Snapshot)
- decisions/0010-best-practice-recherche-pflicht.md Schicht 1.5
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F1.3a / F1.3b / F1.5 / F1.6
