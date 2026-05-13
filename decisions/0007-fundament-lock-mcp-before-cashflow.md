# 0007: Fundament-Lock - MCP vor Cashflow (supersedes ADR 0006 partial)

## Status
Accepted | Date: 2026-04-30 | partial supersede ADR 0006

## Context
Tag 1-8 zeigte wiederholtes Abdriften vom HANDOVER-Plan trotz aktiv gepflegtem Triple-Anker (NORDSTERN/ROADMAP/STOLPERSTEINE). Tag 8 EOD: ADR 0006 entschied "MCP nach Nordstern" mit Begruendung Cashflow-Druck (57 Tage). Tag 9 morgens: CTO drifted vom HANDOVER T9-S1 auf eigene "2 Tage Konsolidierung dann M1"-Empfehlung. CEO-Diagnose: ohne MCP rate ich weiter, fordere Voll-Bloecke an, weiche vom Plan ab. 8 Wochen Drift-Kosten zaehlen mehr als 5-7 Tage Fundament-Kosten.

## Decision
Fundament-Lock aktiv: alle 8 Schritte F1-F8 in docs/FUNDAMENT_PLAN.md MUESSEN pass sein bevor M-Arbeit (Cashflow) startet.

ADR 0006 wird partial superseded: Sequenz-Aussage "MCP nach Nordstern" wird ersetzt durch "MCP vor Cashflow als Teil von Fundament-Lock". ADR 0006 Kern-Bewertung (Cashflow-Druck ist real) bleibt gueltig — sie wird neu gewichtet gegen Drift-Kosten.

ADR 0004 (MCP loest Drift-Wurzel) wird damit zur Tag-9-Realitaet: empirisch belegt durch Drift trotz Triple-Anker.

## Alternatives Considered
- A) ADR 0006 belassen, M1 starten: verworfen, CEO-Veto. 8 Wochen Drift-Beleg ist authoritative.
- B) Nur F1-F3 (Hook+Untracked+LITE) ohne MCP: verworfen, lindert Symptom nicht Ursache. CTO-Drift kommt aus Telephone-Game (ADR 0004 Kern-Diagnose), nicht nur aus Block-Groesse.
- C) Fundament parallel zu M1: verworfen, R7-Prio-Reihenfolge erlaubt das nicht ("Cashflow zuerst" gilt nur wenn Datensicherheit+Informationserhalt+Effizienz solide sind, was sie nicht sind).

## Consequences
+ Drift-Wurzel wird strukturell behoben statt durch Disziplin-Versprechen.
+ Lock-Mechanik (FUNDAMENT_PLAN status pruefen) macht Drift technisch sichtbar, nicht nur menschlich.
+ Nach F8 startet M1 mit MCP-Zugriff, sauberer Permission-Realitaet, LITE-Standard, STATE.json.
- Cashflow-Arbeit pausiert 5-7 Tage. Bei F4-Explosion (Stolperstein S UWP/MSIX) bis 10 Tage.
- Nordstern-Frist 26.06.2026 wird enger. Akzeptiert.

## References
- ADR 0004 (MCP loest Drift-Wurzel) — re-aktiviert
- ADR 0006 (MCP nach Nordstern) — partial superseded
- docs/FUNDAMENT_PLAN.md — F1-F8 Definition
- NORDSTERN.md FUNDAMENT-LOCK Block
- STOLPERSTEINE.md AD (Drift-Pattern)
- Tag-9-CEO-Diagnose vom 2026-04-30
