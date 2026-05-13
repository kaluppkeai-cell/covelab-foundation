# Best-Practice-Snapshot: CLAUDE.md / AGENTS.md / Anti-Drift

**Recherchiert:** 2026-05-10 (Tag 13 Spaetnachmittag, CTO-Chat)
**Anlass:** Phase 1 F11 (CLAUDE.md / AGENTS.md erstellen)
**Quellen:** code.claude.com/docs/best-practices, augmentcode.com how-to-build-agents-md (Maerz 2026), claudearchitect.com claude-context-drift, generative.inc Complete Claude Code Guide 2026, the-ai-corner.com Claude Best Practices 2026, marmelab.com Claude Code Tips, medium.com 50 Best Practices, tw93.fun You-Dont-Know-Claude-Code (Maerz 2026)

## Industry-Konsens
- CLAUDE.md unter 200 Zeilen / 2000 Tokens — laenger fuehrt zum Ignorieren
- AGENTS.md ist emerging Industry-Standard fuer Tool-Portabilitaet (Claude / Copilot / Codex / Cursor / 8 weitere)
- Empfehlung: Inhalt in AGENTS.md, kurze CLAUDE.md die @AGENTS.md importiert
- Auto-generated Files (von LLM erzeugte CLAUDE.md) reduzieren Erfolg um 0.5-2 Prozent und erhoehen Kosten um 20 Prozent
- Human-curated Files erhoehen Erfolg um 4 Prozentpunkte
- Faustregel: Rules sollten OBSERVED FAILURE adressieren, nicht spekulativ generiert werden

## Anti-Drift Best Practices 2026
- Context-Rot ab ~300-400k Token auf 1M Context Modellen
- "Dumb Zone" ab ~40 Prozent Context (Newcomer cap)
- Erfahrene halten Context unter 30 Prozent, push 60 Prozent nur fuer Simple-Tasks
- /clear oder /compact aktiv zwischen unrelated Tasks
- Phase-based Decomposition: grosse Tasks in Sessionable-Phasen
- Spec-First-Workflow (Anthropic-offiziell): minimal prompt + AskUserQuestion-Tool fuer Interview, schreibt SPEC.md, fresh Session executes Spec
- Triggered Re-reads vor Architektur-Entscheidungen + Forced Acknowledgment ("summarize the constraints" statt "did you read it")
- COORDINATION.md fuer State-Persistenz across Sessions (Industry-Standard-Name fuer unsere Live-State-File-Idee)

## CLAUDE.md Inhalt-Empfehlung
- WHAT: Tech-Stack, Project-Structure, Key-Abstractions
- HOW: Commands, Tests, Build, Deploy
- WHO: Identity-Block, Voice-Profile, Anti-AI-Writing-Liste (in 2026)
- WHY: Project-Constraints (non-negotiable), Architecture-Decisions
- NICHT: Spekulative Rules, redundante Code-Konventionen, ueberlange ToC

## CoveLab-Bewertung
- STARTPROMPT_CTO.md ist unsere Variante, aber NICHT in CLAUDE.md / AGENTS.md-Standard
- Phase 1 F11: Convert STARTPROMPT_CTO.md zu AGENTS.md (universal-portabel) plus duenne CLAUDE.md die importiert
- Anti-Drift-Disziplin in 6-Punkt-Self-Check ist gut, koennte erweitert werden um Forced-Acknowledgment-Pattern (CTO muss bei Plan-Edit explizit Constraints zitieren)

## Bessere Optionen aktuell nicht verwendet (dokumentiert)
- **Cowork-Style Identity + Voice + Anti-AI-Writing-Trio** (the-ai-corner.com): drei separate Files Auto-loaded via Global Instructions. Mehr Granularitaet, mehr Wartung. Backlog Phase 4
- **PLAN.md mit GitHub-Issues als Plan-Store**: Phase-Spec persistiert via GitHub-Issues fuer noch staerkere Persistenz. Backlog post-Lock
- **Notion-Database fuer beste Prompts** (sabita2025): externe Knowledge-Base. Vendor-Abhaengigkeit, Backlog
- **/health-Command und /simplify und /review** (shanraisshan): Auto-Audit-Commands. Phase 4 F22 als Status-on-Demand-Komponente integrieren

## Folge-Aktionen
- Phase 1 F11: AGENTS.md (unter 200 Zeilen) plus duenne CLAUDE.md
- Phase 4 F23: Auto-Compact-Hook zur Vermeidung von Context-Rot
