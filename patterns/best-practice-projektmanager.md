# Best-Practice-Snapshot: Projektmanager-Konzept fuer KI-Multi-Agent-Setup

**Recherchiert:** 2026-05-10 (Tag 13 Spaetnachmittag, CTO-Chat)
**Anlass:** Anti-Drift-Mechanismus, post-F10-Vorbereitung wenn CEO nicht mehr Mittler ist
**Quellen:** alirezarezvani/claude-skills (PM + C-Level), wshobson/agents Multi-Agent-Coordinator, mindstudio.ai parallel-agents, addyosmani.com Code Agent Orchestra

## Problem
Aktuell ist CEO der taktische Projektmanager — Drift-Wurzel der ersten 8 Wochen (Prioritaeten-Wechsel, Spontan-Entscheidungen, mitten-im-Bauen-weglaufen). Nach F10 (Direct-Communication CTO-Code) ohne CEO-Bridge braucht es strukturellen Drift-Schutz.

## Drei Varianten

### Variante A — Plugin-Marketplace PM-Skills (sofort, Plug-and-Play)
- Quelle: alirezarezvani/claude-skills, /plugin install pm-skills@claude-code-skills
- 6 vordefinierte PM-Skills (Roadmap, Sprint, Backlog, Status-Reports, Stakeholder-Comm, Risk-Management)
- Vorteil: sofort einsatzbereit, keine Custom-Build-Aufwand
- Nachteil: nicht CoveLab-spezifisch, kennt unsere Lock-Mechanik nicht

### Variante B — Custom Subagent in .claude/agents/projektmanager.md
- Definition mit YAML-Frontmatter + Markdown-Body
- Custom Drift-Detection-Logic (FUNDAMENT_PLAN-Status pruefen, Phase-Gates, Lock-Compliance)
- Auto-Trigger via SessionStart-Hook (Pflicht-Pruefung Plan vs Stand)
- Kommuniziert mit CTO und Code-Chat via Shared-Task-List (COORDINATION.md)
- Vorteil: CoveLab-spezifisch, kennt unsere ADRs / Stolpersteine / Lock
- Nachteil: Custom-Build (4-6h Effort)

### Variante C — wshobson Multi-Agent-Coordinator (Vollausbau)
- Quelle: wshobson/agents Multi-Agent-Coordinator-Plugin
- 4 spezialisierte Coordinator-Agents + 7 Commands + 6 Skills
- Coordinates 7+ specialized agents (backend / database / frontend / test / security / deployment / observability)
- Backbone fuer skalierende Multi-Agent-Architektur
- Vorteil: industriell, getestet, vollstaendig
- Nachteil: Overkill fuer 2-Agent-Setup (CTO + Code), Komplexitaets-Drift

## CoveLab-Empfehlung Reihenfolge
1. **Phase 1 F12 (Tag 14):** Variante A installieren als Test-Run und Lerngrundlage
2. **Phase 1 F13 (Tag 14-15):** Variante B als CoveLab-spezifischer Custom-Subagent (kennt unseren Lock)
3. **Post-Lock-Release / Phase 5+:** Variante C als Backbone-Pruefung wenn Skalierung noetig (mehrere parallele Projekte)

## Bessere Optionen aktuell nicht verwendet (dokumentiert)
- **Remy** (mindstudio.ai): hosted Product-Manager-Agent. Vendor-Abhaengigkeit, Backlog
- **Compound Eng / Conductor** (Spec-driven Multi-Agent IDE): visueller Manager. Komplexitaet, Backlog
- **Intent (Augment Code) BYOA-Modell**: Multi-Agent-Coordination-Layer mit Living-Spec-Pattern. Vendor-Lock, Backlog
- **Claude Squad / Antigravity**: Multi-Agent-Local-Tools mit Dashboard-UI. Backlog Phase 5+

## Auto-Trigger fuer Variante-B-Subagent
- SessionStart-Hook: Plan-vs-Stand-Pruefung
- Pre-Phase-Gate: Evidence-Pruefung vor Pass-Markierung
- Drift-Indikator: 3+ Tasks ohne Plan-Reference, Subagent warnt CTO
- CEO-Status-Report: nur auf Anfrage, sonst stille Drift-Detection im Hintergrund

## Folge-Aktionen
- Phase 1 F12: Variante A installieren
- Phase 1 F13: Variante B Custom-Subagent definieren
- Phase 2 F16: COORDINATION.md als Shared-Task-List etablieren
- Post-Lock: Variante C evaluieren bei Skalierung
