# RESEARCH_INDEX.md

**Stand:** 2026-05-11 (Tag 15 Spaet-Abend, F1.3b Sub-1b.2)
**Authority:** patterns/best-practice-*.md + patterns/skill-activation-baseline.md + patterns/sme-multi-agent-architecture.md + patterns/sonic_weave_extraction.md + patterns/context-need-log.md
**Coverage-Klassifikation (BR-Lehre):** konzeptionell / operativ-detailliert / empirisch / pre-pivot-archivarisch / laufendes-logging

## Zweck

Lookup-DB fuer Recherche-Snapshot-Auswahl vor jeder ADR / jedem F-Schritt mit Effort ueber 2h (ADR 0010 Schicht 1.5 Coverage-Check).

Pflicht-Workflow:
1. CTO sieht Aufgabe mit Effort ueber 2h
2. CTO scannt diesen Index nach Topic-Match
3. Snapshot vorhanden plus operativ-detailliert - direkt verwendbar
4. Snapshot vorhanden plus konzeptionell - web_search-Aufpolierung Pflicht VOR Code-Prompt-Generierung
5. Kein Snapshot - neue Recherche-Pflicht

## Topics

| Topic | File | Coverage | Letztes Update | F-Schritt-Relevanz |
|-------|------|----------|----------------|--------------------|
| Architecture Decision Records | best-practice-adr.md | konzeptionell plus praxisreif | 2026-05-10 | ADR-Bau allgemein, Format-Konsistenz |
| CLAUDE.md / AGENTS.md Best-Practices | best-practice-claude-md.md | konzeptionell | 2026-05-10 | F1.1+F1.2 (gebraucht) |
| Multi-Agent Patterns (Anthropic) | best-practice-multi-agent.md | konzeptionell | 2026-05-10 | F1.5 Subagent-Definitionen |
| Direct Agent-to-Agent Communication | best-practice-direct-communication.md | operativ-detailliert | 2026-05-10 | F2.3 Headless claude -p |
| Projektmanager-Subagent | best-practice-projektmanager.md | konzeptionell | 2026-05-10 | F1.5 PM-Subagent |
| Skill-Aktivierung 3-Schichten | best-practice-skill-activation.md | operativ-detailliert | 2026-05-11 | F1.5 plus F1.6 Skill-Forcierung |
| Skills + Plugins (Marketplace-Mechanik) | best-practice-skills-plugins.md | konzeptionell (Drift BR/BU - Skill-Zahlen veraltet) | 2026-05-10 | F1.3 historisch, Refresh fuer F1.4 noetig |
| VPS-only + Mobile-Bedienung | best-practice-vps-only-mobile-management.md | operativ-detailliert | 2026-05-11 | F2.4+F2.5+F4.4+F4.5 |
| Plugin-Eval Anthropic-Official | plugin-evaluation-anthropic-official.md | F1.3-Bewertung | 2026-05-11 | F1.3 |
| Plugin-Eval alirezarezvani | plugin-evaluation-alirezarezvani.md | F1.3-Bewertung | 2026-05-11 | F1.3, Phase-2 Re-Eval engineering-advanced |
| Plugin-Eval wshobson | plugin-evaluation-wshobson.md | F1.4-Bewertung operativ-detailliert | 2026-05-12 | F1.4 plus Phase-2 Re-Eval agent-teams + context-management + block-no-verify (F1.6) + conductor + plugin-eval + review-agent-governance |
| Subagent-Frontmatter Claude Code 2026 | best-practice-subagent-frontmatter.md | operativ-detailliert | 2026-05-12 | F1.5 (Subagent-Definitionen) plus F1.6 Hook-Integration |
| Hook-Implementation Claude Code 2026 | best-practice-hook-implementation.md | operativ-detailliert | 2026-05-12 | F1.6 (4 Hook-Scripts SessionStart+PreToolUse+PostToolUse+SessionEnd) |
| Skill-Aktivierungs-Baseline (empirisch) | skill-activation-baseline.md | empirisch plus Post-Restart-Ergaenzung | 2026-05-11 | F1.3a + Input fuer F1.5+F1.6 |
| Context-Need-Log | context-need-log.md | laufendes-logging | aktiv | Pre-EFF1-Cut-Datenquelle |
| Multi-Agent SME-Architektur | sme-multi-agent-architecture.md | pre-pivot-archivarisch | 2026-04-27 | ARCHIVARISCH (Faceless-Hybrid M1-M3) - durch ADR 0011 sunsetted |
| Sonic-Weave-Extraction | sonic_weave_extraction.md | pre-pivot-archivarisch | 2026-04-29 | ARCHIVARISCH - durch ADR 0011 sunsetted |

| MCP-rw-Mount (Docker bind-mount Mode plus Application-Layer-Allowlist) | best-practice-mcp-rw-mount.md | operativ-detailliert | 2026-05-12 | F2.1 |

| COORDINATION.md Live-State Pattern (AI-Agent-Project) | best-practice-coordination-md.md | operativ-detailliert | 2026-05-12 | F2.2 |

| Code-Prompt-Schema / File-Reference-Pattern | code-prompt-schema.md | operativ-detailliert | 2026-05-13 | F4.0 Sub-4 plus alle kuenftigen CTO-Code-Chat-Prompts |

| Repo-Cleanup-Konvention fuer AI-Agent-Projekte | best-practice-repo-cleanup.md | operativ-detailliert | 2026-05-13 | F4.1 Root-Hygiene + Archiv-Sentinel + Backup-Routine |
| Cleanup-Konvention CoveLab | cleanup-konvention.md | operativ-detailliert | 2026-05-13 | F4.1 Root-Allowlist + Archiv-Regeln + HANDOVER-Retention |
| Repo-Layer-Separation Framework vs Project | best-practice-repo-layer-separation.md | operativ-detailliert | 2026-05-13 | F3.1 + F3.2 + F3.3 (KRITISCHER BEFUND: CLAUDE.md+.claude/ bleiben an Root, F3.1-Scope-Revision noetig) |

| Token-Efficiency / Prompt-Caching / Subagent-Cost-Benefit | best-practice-token-efficiency.md | operativ-detailliert | 2026-05-12 | F4.0 plus F1.5 (Subagent-Token-Multiplier) |

## Coverage-Gaps (Topics ohne Snapshot)

Bei diesen Topics IST KEIN Snapshot vorhanden - Recherche-Pflicht ab Effort ueber 2h:

| Topic | Trigger-F-Schritt | Recherche-Quelle-Hinweis |
|-------|-------------------|--------------------------|
| Context-Engineering / Pointer-Systeme | F1.6 (PreToolUse-Hook Spec) | code.claude.com/docs hook-system, Industry Context-Engineering Patterns (Token-Optimierung teilweise geschlossen via best-practice-token-efficiency.md 2026-05-12) |
| GitHub Actions Push-Approval-Webhook | F2.4 | docs.github.com actions plus stanislas.blog Netclode-Pattern |
| inotify-Watcher systemd-Integration | F4.4 | inotify man-page plus systemd.service docs |

## Drift-Notizen

- best-practice-skills-plugins.md Skill-Zahlen veraltet (49 fuer engineering-skills - real 32 plus Drift in mehreren Quellen wie BU dokumentiert). Update sinnvoll mit F1.4 wshobson-Recherche.
- Pre-Pivot Mining-Patterns (sme-multi-agent + sonic_weave) sind ARCHIVARISCH durch ADR 0011 - NICHT als Recherche-Authority fuer neue F-Schritte verwenden.

## Update-Konvention

Bei jedem neuen Snapshot (patterns/best-practice-*.md committet):
1. RESEARCH_INDEX.md Tabellen-Zeile addieren
2. Coverage-Klassifikation explizit setzen (konzeptionell / operativ-detailliert / empirisch / archivarisch / laufendes-logging)
3. F-Schritt-Relevanz-Spalte fuellen

Bei Coverage-Wechsel (z.B. Update von konzeptionell zu operativ-detailliert):
1. Coverage-Spalte aktualisieren
2. Letztes-Update-Datum aktualisieren

## References

- decisions/0010-best-practice-recherche-pflicht.md (Recherche-Pflicht Authority + Schicht 1.5)
- STOLPERSTEINE.md BR (Snapshot-Coverage-Check)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F1.3b
