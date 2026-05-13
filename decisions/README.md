# Architecture Decision Records (ADR)

## Zweck
Append-only Log strategischer + technischer Entscheidungen. Verhindert Wiederhol-Diskussionen und Strategie-Drift.

## Format
- 1 Datei pro Entscheidung
- Filename: `NNNN-kurzer-name.md` (4-stellige Sequenznummer)
- NIEMALS editieren nach Status "Accepted" - neue ADR erstellen die alte superseded

## Status-Werte
- `Proposed` - in Diskussion
- `Accepted` - aktiv
- `Deprecated by NNNN` - durch andere ADR ersetzt
- `Superseded by NNNN` - explizit aufgehoben

## Template
NNNN: Titel der Entscheidung
Status
Accepted | Date: YYYY-MM-DD
Context
Welches Problem stand zur Debatte?
Decision
Was wurde entschieden?
Alternatives Considered
Was wurde verworfen, warum?
Consequences
Positive + negative Folgen, inkl. Trade-offs.
References
Links zu Quellen, Memory-Eintraegen, Chats.
