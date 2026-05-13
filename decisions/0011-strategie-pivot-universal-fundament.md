# 0011: Strategie-Pivot - Universal-Fundament als primaeres Ziel

## Status
Accepted | Date: 2026-05-10 | partial supersede ADR 0001, supersede ADR 0002 + ADR 0003, supersedes 26.06.2026 Cashflow-Frist

## Context
Tag 1-13 (45 Tage Projektarbeit) zeigte wiederholte strategische Wechsel und Drift trotz aktivem Anker-System (NORDSTERN / ROADMAP / STOLPERSTEINE).

CEO-Diagnose Tag 13 Spaetnachmittag (CEO-Zitat):
- "Leider musste ich spontan immer wieder die Richtung aendern, weil mir Probleme wie driften, Informationsverlust und so weiter aufgefallen sind. So konnten wir kein Projekt durchziehen."
- "Ich habe mich davon verabschiedet schnell Geld zu verdienen."
- "Hauptaufgabe ist ein solides Fundament um damit unterschiedliche Projekte zu erstellen."

Zwei Erkenntnisse zwingen den Pivot:
1. Cashflow-Druck (NORDSTERN.md 26.06.2026-Frist) hat Drift verstaerkt, nicht reduziert. Drift selbst ist die Wurzel-Problem die jeden Cashflow-Versuch sabotierte.
2. Das Fundament das wir gerade bauen (Lock-Mechanik, Self-Check, Doku-Pipeline, MCP-Setup, Plugin-Integration) ist selbst marktfaehig — andere Solo-Founders, Berater, AI-Builders haben das gleiche Drift-Problem.

Tag-13-Spaetnachmittag-Recherche bestaetigte: 4200+ Skills, 770+ MCP-Server, 2500+ Marketplaces existieren — der Bedarf an Strukturen die Multi-Agent-Drift verhindern ist Industry-weit nicht geloest. CoveLab-Lock-Mechanik plus Self-Check plus Stolperstein-System sind innovativ und nicht im Mainstream.

## Decision

Strategischer Pivot:

1. **Primaeres Ziel:** Universal-Fundament fuer KI-gestuetzte Projektarbeit als wiederverwendbares Repo-Template.

2. **Sekundaeres Ziel (post-Fundament-Release):** Verkauf des Universal-Fundaments als Asset (Upwork-Lieferung an Solo-Founders / Berater, Skill-Listing auf SkillsMP / Agensi, Lizenz-Modell). Dies ist die neue Cashflow-Strategie.

3. **CoveLab als erstes Anwender-Projekt:** das Universal-Fundament wird mit CoveLab-Projekt-Layer befuellt als Demo-Implementierung. Andere Anwender klonen das Universal-Fundament und befuellen mit ihrem Projekt-Layer.

4. **NORDSTERN.md 26.06.2026-Cashflow-Frist:** entfaellt. Wird ersetzt durch UNIVERSAL_NORDSTERN.md mit Vision (Universal-Fundament als Produkt) plus Phasen (P0-P5) plus qualitative Pass-Bedingungen statt fixer Termin.

5. **Fundament-Plan-Erweiterung:** F1-F10 wird erweitert zu F1-F28 (5 Phasen plus Phase 0, dokumentiert in docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md). Lock-Release-Bedingung wird F1-F28 alle pass.

6. **ADR-Pruefung erforderlich:**
   - ADR 0001 (Faceless Hybrid Pivot): Cashflow-strategisch, partial supersede — Verkauf des Fundaments ersetzt Faceless-Hybrid-Asset-Strategie. Faceless-YouTube-Constraint kann fuer Fundament-Demo-Walkthrough beibehalten werden (Phase 5 F27).
   - ADR 0002 (No-direct-customer-contact): Cashflow-strategisch, supersede — Universal-Fundament-Verkauf via Upwork erfordert direkten Kundenkontakt. Constraint entfaellt.
   - ADR 0003 (3-Asset-Model mining-driven): Cashflow-strategisch, supersede — Mining ist nicht relevant fuer Fundament-Verkauf. Single-Asset-Model (das Fundament selbst).

## Alternatives Considered

A) Cashflow-Frist 26.06.2026 beibehalten und parallel Fundament weiterbauen: verworfen, fuer Cashflow-Push ohne Fundament fehlt die Stabilitaets-Grundlage. Cashflow-Druck wuerde die gleiche Drift produzieren die in Tag 1-12 sichtbar war.

B) Fundament beenden und dann Cashflow ohne strategischen Pivot: verworfen als gleiches Konzept aber ohne Verpackungs-Idee — Cashflow-Druck wuerde nach Lock-Release sofort wieder Drift erzeugen, weil das Fundament nicht zum Asset wurde sondern nur zum internen Werkzeug. Universal-Verpackung ist die strukturelle Loesung.

C) CoveLab-Strategie unveraendert lassen, Fundament als internes Werkzeug nutzen: verworfen, verschwendet das Differenzierungs-Potenzial. Das was wir bauen ist Markt-relevant.

## Consequences

Positiv:
- Drift-Wurzel strukturell behoben: kein Cashflow-Druck mehr, der spontane Richtungs-Wechsel produziert
- Marktfaehiges Asset entsteht statt internes Werkzeug
- 5-Phasen-Plan (UNIVERSAL_FUNDAMENT_FAHRPLAN.md) gibt klare Sequenz ohne Termin-Pressure
- Universal-Layer-Trennung (Phase 3) erlaubt sauberen Wiedereinsatz fuer beliebig viele zukuenftige Projekte
- CEO-Bridge-Aufwand sinkt nach F10 drastisch (Direct-Communication CTO-Code)
- ADR 0010 Best-Practice-Recherche-Pflicht passt nahtlos zu Verkaufs-Strategie (Quellen-Anker als Verkaufsargument)

Negativ:
- Cashflow-Verzoegerung: 4-8 Wochen Foundation-Investition bevor erstes Verkaufsangebot moeglich. CEO-Runway muss diese Phase tragen.
- Komplexitaets-Erhoehung: 28 F-Schritte statt 10, Phase-Gates statt binaerer Lock
- Markt-Risiko: Annahme dass Universal-Fundament marktfaehig ist nicht validiert. Erste Validierung erst Phase 5 (Verkaufs-Verpackung).
- ADRs 0001-0003 supersede-Aufwand: Strategie-Doku durchschauen und revidieren
- Mitigation Markt-Risiko: Phase 5 enthaelt Demo-Walkthrough und Landing-Page als Markt-Test bevor grosser Marketing-Push

## Implementation

Sofort (Tag 13 EOD):
- Diese ADR (0011) committen — DONE mit diesem Commit
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md schreiben (Phase 0-5, F1-F28) — Folge-Step J
- FUNDAMENT_PLAN.md aktualisieren mit Cross-Reference plus F7 streichen plus F8-F10 in Phase-2-Verlagerung markieren — Folge-Step K

Tag 14 morgens:
- Phase 0 abschliessen: NORDSTERN.md zu UNIVERSAL_NORDSTERN.md umbenennen, ADRs 0001-0003 supersede-Pruefung committen
- Phase 1 starten: F11 AGENTS.md / CLAUDE.md, F12 Plugin-Marketplace-Setup

Mid-Term (Phase 5):
- Universal-Fundament als Verkaufs-Produkt verpacken
- Upwork-Profil aktualisieren mit "AI Project Foundation"-Lieferung-Angebot

## References

- patterns/best-practice-multi-agent.md (Commit ba39a2b) — Tag-13-Spaetnachmittag-Recherche-Quelle
- patterns/best-practice-claude-md.md (Commit ba39a2b) — dito
- patterns/best-practice-skills-plugins.md (Commit ba39a2b) — dito
- patterns/best-practice-projektmanager.md (Commit ba39a2b) — dito
- ADR 0007 (Fundament-Lock) — Lock-Mechanik wird beibehalten und erweitert
- ADR 0010 (Best-Practice-Recherche-Pflicht) — Tag-13-Spaetnachmittag-Recherche war erste Vollanwendung
- ADR 0001 (Faceless Hybrid) — partial supersede
- ADR 0002 (No-direct-customer-contact) — supersede
- ADR 0003 (3-Asset-Model mining-driven) — supersede
- HANDOVER_20260510T154259Z_TAG13_NACHMITTAG_CTO.md — Kontext-Anker
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md (Folge-Step J)
