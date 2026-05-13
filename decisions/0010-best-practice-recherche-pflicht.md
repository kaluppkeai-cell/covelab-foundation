# 0010: Best-Practice-Recherche-Pflicht vor Architektur-Entscheidungen

## Status
Proposed | Date: 2026-05-10 | extends FUNDAMENT_PLAN process layer

## Context
Tag 13 Nachmittag: CTO-Chat plante F9-Skizze und Code-Prompts ohne vorherige Best-Practice-Recherche. CEO musste ausdruecklich nach systematischer Quellen-Pruefung fragen. Anschliessende Recherche zu ADR-Format und Direct-Agent-Communication lieferte:
- Bestaetigung dass unser ADR-Format mainstream-konform ist (kein Refactor noetig)
- Mehrere konkrete Architektur-Optionen fuer Direct-Agent-Communication (4 Patterns) die CTO-Plan ohne Recherche unsichtbar geblieben waeren
- Technische Spezifikationen (Claude Agent SDK, Subagents, Headless-Mode) die fuer F9-Teil-3-Scope und F10-Aufnahme entscheidend sind

Die Recherche-Luecke ist strukturell, nicht inzidentell:
- Memory und userPreferences sind statisch (Trainings-Cutoff Jan 2026 fuer Standard-Wissen, kein Update fuer Anthropic-Produkt-Aktualitaet)
- Self-Check (R36) prueft Format und Ton, nicht systematische Sach-Pruefung von Inhalt-Behauptungen (Stolperstein AM)
- Anthropic-Produkt-Wissen (Claude Code, MCP, SDK) entwickelt sich monatlich, Memory-Stand veraltet schnell

Konsequenz ohne Pflicht: Architektur-Entscheidungen basieren auf Memory-Wissen das veraltet sein kann. Korrektur post-Commit ist deutlich teurer als 15-30 min Recherche vor Spec-Entwurf.

## Decision

Best-Practice-Recherche-Pflicht vor jeder folgenden Aktion:
- jeder neuen oder revidierten ADR
- jedem F-Schritt-Spec-Entwurf (FUNDAMENT_PLAN.md)
- jeder Architektur-Entscheidung mit mindestens mittlerem Effort (groesser 2h)

Drei Pflicht-Quellen-Schichten in dieser Reihenfolge:

1. Anthropic-Produkt-Wissen-Skill lesen wenn Claude / MCP / Claude-Code / SDK relevant sind (Skill-Pfad /mnt/skills/public/product-self-knowledge/SKILL.md)
2. Offizielle Anthropic-Docs fetchen wenn Skill auf Spezifikum verweist (docs.claude.com fuer API, code.claude.com fuer Claude Code)
3. web_search fuer Industry-Best-Practice (Format-Standards, Workflow-Patterns, Tooling-Landschaft)

Output-Pflicht: Recherche-Snapshot als File in patterns/best-practice-TOPIC.md persistieren mit:
- Recherche-Datum, Anlass, Quellen-Liste
- Pflicht-Elemente (was alle Quellen einig sind)
- Konkrete CoveLab-Bewertung (was uebernehmen, was nicht, warum)
- Folge-Aktionen

Erste Snapshots Tag 13 als Referenz: patterns/best-practice-adr.md, patterns/best-practice-direct-communication.md (Commit eee13a7).

## Alternatives Considered

A) Keine Pflicht, Recherche nur bei akuter Wissens-Luecke (Status Quo bis Tag 13). Verworfen: Tag-13-Lehre zeigt dass CTO Wissens-Luecke nicht selbst erkennt, CEO muss explizit fragen — das skaliert nicht und macht CEO zum Bottleneck (gleiche Anti-Pattern die F9 aufloesen soll).

B) Recherche nur auf CEO-Anfrage. Verworfen: CEO kann nicht jede Architektur-Entscheidung mit-recherchieren. R-Spirit ist CTO-Lead-Rolle, CTO muss eigenstaendig Best-Practice einholen.

C) Automatische Recherche durch Subagent (post-F10). Verworfen als sofortige Loesung: braucht Direct-Agent-Communication-Architektur (F10) als Voraussetzung. Process-ADR jetzt etabliert die Disziplin manuell, Subagent kann sie spaeter automatisieren.

## Consequences

Positiv:
- Wissen-Stand aktuell trotz Trainings-Cutoff (relevant fuer Anthropic-Produkt-Themen mit hoher Aenderungs-Frequenz)
- Snapshot-Persistenz schuetzt R7-Prio-1 (Informationsverlust): Recherche-Output ueberlebt Chat-Ende
- Anti-Drift: Architektur-Entscheidungen explizit gegen aktuellen Industry-Stand begruendet
- Snapshot-Akkumulation in patterns/ baut langfristig CoveLab-internes Knowledge-Base auf (LLM-Memory-Inhalt fuer zukuenftige Sessions)
- ADR-Quality steigt durch Quellen-Anker in References

Negativ:
- 15-30 min Mehr-Aufwand pro Architektur-Entscheidung (Recherche + Snapshot-Schreiben)
- Risiko-Ueberzug bei trivialen Decisions: nicht jeder Code-Prompt braucht Recherche
- Bei Falsch-Anwendung Workflow-Verlangsamung
- Mitigation: Pflicht greift nur fuer ADR + F-Schritt + Architektur-Entscheidung-mittel-Effort. Trivial-Tasks (kleine Edits, Pflege-Aktionen) bleiben Recherche-frei.

## Implementation

Verankerung in zwei Files:
- docs/STARTPROMPT_CTO.md neuer Self-Check-Punkt-6: "Best-Practice-Recherche fuer aktuelle Aufgabe noetig (ADR / F-Schritt / Architektur-Entscheidung)? Wenn ja: Quellen-Schichten 1-3 abgearbeitet und Snapshot in patterns/ persistiert?"
- patterns/-Verzeichnis als Recherche-Snapshot-Home etabliert (bisher nur Mining-Patterns)

## References

- patterns/best-practice-adr.md (Commit eee13a7) - erste Anwendung dieser ADR
- patterns/best-practice-direct-communication.md (Commit eee13a7) - erste Anwendung dieser ADR
- ADR 0007 (Fundament-Lock) - Process-ADRs erlaubt parallel zu Lock-F-Steps
- ADR 0009 (F9 Schreib-Faehigkeit-Foundation) - F9-Plan war erste Test-Anwendung der neuen Recherche-Pflicht
- HANDOVER_20260510T_TAG13_CTO.md - Tag-13-Verlauf-Dokumentation
- Stolperstein AM (CTO-Annahme-Drift bei indirektem Daten-Zugriff) - Recherche-Pflicht ist strukturelle Antwort
- /mnt/skills/public/product-self-knowledge/SKILL.md - Anthropic-Produkt-Pflicht-Skill


## Update 2026-05-11 (Tag 15 Nachmittag, nach Stolperstein BR)

Process-Pflicht-Erweiterung: Snapshot-Coverage-Check vor F-Schritt-Build-Prompt (Schicht 1.5 zwischen Schicht 1 und 2 der urspruenglichen 3-Schichten-Recherche).

Vor jedem F-Schritt-Build-Prompt PFLICHT pruefen ob existierender Snapshot konzeptionell oder operativ-detailliert ist:
- Konzeptionell: beschreibt Architektur, Markt-Stand, Komponenten-Ueberblick. NICHT genug fuer Build.
- Operativ-detailliert: enthaelt konkrete Syntax, Install-Befehle, Konfig-Parameter, Code-Pfade. Genug fuer Build.

Bei nur-konzeptionellem Snapshot: web_search-Aufpolierung Pflicht VOR Code-Prompt-Generierung. Findings als Snapshot-Update oder neuer Snapshot persistieren. Verlinkung im Build-Prompt CONTEXT.

Beispiel-Drift Tag 15 Nachmittag: patterns/best-practice-skills-plugins.md (ba39a2b) hatte Marketplace-Konzepte plus Plugin-Kategorien, nicht aber konkrete Install-Syntax (NAME at MANIFEST_KEY) und nicht Marketplace-Manifest-Key-Authority. Daher F1.3-Voll-Bau STOPP wegen Key-Mismatch (Stolperstein BQ), Lehre als BR persistiert.

Verankerung: R36 Self-Check Punkt 6 wird sinngemaess erweitert (Snapshot operativ-detailliert oder konzeptionell?). Strukturelle Automatisierung in F1.5 PM-Subagent (Drift-Detector) plus F1.6 SessionStart-Hook plus F4.2 /status-Slash-Command. CTO-Disziplin wird durch technische Gates ersetzt.
