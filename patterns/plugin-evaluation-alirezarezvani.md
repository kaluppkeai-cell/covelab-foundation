# Plugin-Evaluation: alirezarezvani/claude-skills Marketplace

Stand: 2026-05-11 (Tag 15 Nachmittag, F1.3-Bau, post-Recherche)
Marketplace-Source: GitHub alirezarezvani/claude-skills (Repo-Pfad als Add-Argument)
Marketplace-Manifest-Key: claude-code-skills (Manifest-name-Feld, Repo-Owner-Wahl - siehe Stolperstein BQ)
Marketplace-Add-Date: 2026-05-11

## Bewertung

Community-Marketplace mit 232+ Skills aufgeteilt in mehrere Bundle-Plugins und Standalone-Plugins. Owner Alireza Rezvani. Hoher Skill-Output, Quality varies pro Skill. Selektive Installation empfohlen, kein Bulk-Install. Marketplace-Name claude-code-skills ist Repo-Owner-Wahl im marketplace.json-Manifest-name-Feld.

## Installierte Plugins (F1.3-Bau)

- pm-skills@claude-code-skills: 6 vordefinierte PM-Skills (Roadmap, Sprint, Backlog, Status-Reports, Stakeholder-Comm, Risk-Management). Begruendung: PM-Subagent-Lerngrundlage (siehe patterns/best-practice-projektmanager.md Variante A). Erwartete Nutzung: Lerngrundlage fuer F1.5 Custom-PM-Subagent.

- engineering-skills@claude-code-skills: 24 core Engineering-Skills (architecture, design-patterns, refactoring, testing-Grundlagen). Begruendung: breite Anwendung fuer Phase-1-2 F-Schritte (Hook-Bau F1.6, Subagent-Definitionen F1.5). NICHT die advanced-Variante (siehe Nicht-Installiert).

## Nicht-Installiert (begruendet)

- engineering-advanced-skills@claude-code-skills (25 POWERFUL-tier: agent-designer, agent-workflow-designer, MCP-server-builder, spec-driven-workflow, RAG-architect, secrets-vault-manager, dependency-auditor, llm-cost-optimizer, prompt-governance, observability-designer, performance-profiler, demo-video, weitere): Phase-2-Backlog. Hohe CoveLab-Relevanz erkannt - Re-Evaluation an F1.5 (Subagent-Definitionen) plus F2.1 (MCP-rw-Mount). Nicht jetzt installiert wegen FAHRPLAN-Treue.
- c-level-skills (28 oder 34): nicht passend zu Solo-Founder-Setup
- ra-qm-skills (12-14): nicht relevant, kein regulatorisches Umfeld
- marketing-skills (43-44), product-skills (12-16), business-growth-skills (4-5), finance-skills (2-4): Phase-5-Backlog (Verkaufs-Verpackung). Re-Evaluation wenn Foundation-Verkauf vorbereitet.
- Standalone-Plugins (pw, self-improving-agent, google-workspace-cli, executive-mentor, content-creator, demand-gen, fullstack-engineer, aws-architect, product-manager, skill-security-auditor): selektiv im Backlog, sind Subsets der Bundle-Plugins.

## Folge-Aktionen

- Test-Run einzelner PM-Skills (Roadmap-Skill fuer FAHRPLAN-Generierung pruefen)
- Phase 2 (F1.5 oder F2.1): engineering-advanced-skills evaluieren, hohe Relevanz erwartet
- Bei Quality-Issues Plugin-Uninstall-Pattern via Stolperstein dokumentieren
- best-practice-skills-plugins.md Skill-Zahlen-Update (Snapshot ba39a2b hat 49 fuer engineering-skills, korrekt 24)

## References

- patterns/best-practice-skills-plugins.md (Recherche-Quelle Tag 13, ba39a2b - Skill-Zahlen veraltet)
- patterns/best-practice-projektmanager.md (PM-Subagent-Vorbereitung F1.5)
- STOLPERSTEINE.md BQ (Marketplace-Manifest-Key-Authority)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F1.3
- Offizielle Quelle: github.com/alirezarezvani/claude-skills README plus marketplace.json
