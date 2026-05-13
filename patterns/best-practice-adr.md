# Best-Practice-Snapshot: Architecture Decision Records (ADR)

**Recherchiert:** 2026-05-10 (Tag 13 Nachmittag, CTO-Chat)
**Anlass:** ADR 0009 Skizze - Format-Konsistenz-Check vor Verankerung
**Quellen:** adr.github.io/madr, github.com/joelparkerhenderson/architecture-decision-record, ozimmer.ch MADR Primer (Nov 2022), startupbricks.in Startup-ADR-Guide (2026-01)

## Status der ADR-Landschaft 2025-2026

- MADR (Markdown Any Decision Records) v3.0.0 ist aktueller Standard, ~2100 GitHub-Stars. Wichtigste Aenderung in v3.0.0: Positive und Negative Consequences merged in einheitliche Consequences-Sektion (analog zu Pros-and-Cons-of-the-Options-Grammatik).
- Nygard-Klassiker (Status / Context / Decision / Consequences) bleibt weiterhin valide und weit verbreitet.
- Y-Statements (single-sentence-Format): "In the context of X, facing Y, we decided for Z to achieve Q, accepting downside R" - fuer Mini-Decisions.
- Auswahl-Faustregel (Startupbricks): MADR fuer significant decisions, Y-statements fuer minor.

## Pflicht-Elemente (alle Quellen einig)

1. Title (eindeutig, sprechend, mit Nummer)
2. Status (proposed / accepted / superseded / deprecated, mit Datum)
3. Context (Hintergrund, Problem, treibende Faktoren)
4. Decision (was entschieden wurde, klar formuliert)
5. Alternatives Considered (was geprueft wurde + warum verworfen) - shows you did your homework
6. Consequences (positiv UND negativ, hindsight-bias-Risiko bei einseitiger Doku)
7. References (verwandte ADRs, Quellen, Stolpersteine)

## Praxis-Empfehlungen 2025-2026

- Each ADR is about ONE decision (kein Multi-Decision-Bundle)
- Pull-Request-Review fuer ADRs (bei Solo: CEO-Review als Aequivalent)
- ADR-Review-Cycle every 6-12 months: Status pruefen ob noch valid
- ADR-Linking aus README + Onboarding-Doku
- Effort-Einsparung 5-10h pro Monat durch eliminierte recurring debates (Startup-Daten Startupbricks)
- 2025-Faktor: AI-Coding-Assistants nutzen ADRs als Context (Memory-Inhalt fuer LLM)

## CoveLab-Bewertung

Unser Format (ADR 0007 / 0008 / 0009) ist Nygard-Hybrid mit MADR-Element Alternatives-Considered - mainstream-konform. Kein Format-Refactor noetig.

Adoptier-Empfehlungen:
- ADR-Review-Cycle nach Lock-Release als Backlog-Item (alle 6 Monate ADR-Status-Audit)
- Y-Statement-Format fuer Mini-Decisions (kleine Process-Anpassungen, statt jede Kleinigkeit als full-ADR)
- Alternatives-Considered und Negative-Consequences sind Pflicht (machen wir bereits)

## Folge-Aktionen

- Keine sofortige Aktion auf bestehenden ADRs noetig
- Beim naechsten ADR (0010 Best-Practice-Recherche-Pflicht): dieses Snapshot als Quelle referenzieren
- Tag-30+: ADR-Status-Audit-Routine etablieren (Backlog)
