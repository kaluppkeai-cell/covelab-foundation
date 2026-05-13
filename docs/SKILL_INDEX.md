# SKILL_INDEX.md

**Stand:** 2026-05-11 (Tag 15 Spaet-Abend, F1.3b Sub-1b.2)
**Authority:** Surfaced Skills via Claude Code CLI (BW-Lehre - marketplace.json + Filesystem-Top-Level)
**Quelle:** reports/skills_recon_f13b_sub1b.md (Recon Tag 15 Spaet-Abend)
**Update-Intervall:** bei Plugin-Install / Uninstall / Version-Update

## Zweck

Lookup-DB fuer Skill-Auswahl vor jedem Code-Prompt. Pflicht-Workflow:
1. CTO sieht Aufgabe
2. CTO scannt diesen Index nach Trigger-Keywords
3. Passender Skill identifiziert - Skill-Name in Code-Prompt CONTEXT-Sektion erwaehnen
4. Kein passender Skill - Code-Prompt ohne Skill-Verweis, im session_log als Gap-Signal fuer F4.1 Cleanup notieren

## Top-Level Surfaced (54)

| Bundle | Count | Quelle |
|--------|-------|--------|
| Built-in | 10 | Claude Code Core |
| commit-commands | 3 | claude-plugins-official |
| pm-skills | 9 | claude-code-skills |
| engineering-skills | 32 | claude-code-skills |

Plus 5 Bundle-Roots mit 19 nested Sub-Skills (separate Tabelle unten). 18 ZIP-Bundles im Cache (nicht entpackt, ignorierbar).

## Built-in Skills (10)

| Name | Activation | CoveLab-Use-Case |
|------|------------|------------------|
| update-config | autonomous | Setting-Updates - nicht direkt fuer CoveLab |
| keybindings-help | autonomous | Shortcut-Lookup - nicht relevant |
| simplify | autonomous | Code-Simplifizierung - bei Build-Scripts ggf |
| fewer-permission-prompts | autonomous | Permission-Workflow streamlinen - F1.6-relevant |
| loop | autonomous | Loop-Pattern - nicht direkt |
| schedule | autonomous | Scheduled Tasks - F4.3 Auto-Routinen |
| claude-api | autonomous | API-Call-Pattern - F2.4 claude-CLI |
| init | autonomous | Project-Init - F3.2 foundation-Repo |
| review | autonomous | Code-Review - bei Build-Scripts |
| security-review | autonomous | Security-Audit - vor F2.4 push-approval |

## commit-commands Slash-Commands (3)

| Name | Activation | CoveLab-Use-Case |
|------|------------|------------------|
| /commit-commands:commit | slash | git commit waehrend Bau - direkt einsetzbar |
| /commit-commands:commit-push-pr | slash | push+PR - NICHT verwenden (Stolperstein W, CEO-only push) |
| /commit-commands:clean_gone | slash | branch-cleanup - nicht relevant (single-branch main) |

## pm-skills (9)

| Name | Trigger-Keywords | Activation | CoveLab-Use-Case |
|------|------------------|------------|------------------|
| senior-pm | enterprise PM, EMV, Monte Carlo, WSJF, RICE, portfolio, risk, stakeholder, roadmap | autonomous | F-Schritt-Planung, Risk-Assessment, Phase-Roadmap-Refinement |
| scrum-master | sprint, velocity, retrospective, agile, standup, backlog grooming | autonomous | irrelevant (Solo-Founder, kein Team) |
| meeting-analyzer | meeting transcript, behavioral patterns, coaching feedback | autonomous | irrelevant (keine Meetings) |
| team-communications | 3P updates, newsletter, FAQ, incident report, status report | autonomous | Phase 5 Verkaufs-Verpackung (Foundation-Anwender-Comms) |
| jira-expert | JQL, Jira projects, workflows, automation | autonomous | irrelevant (kein Jira) |
| confluence-expert | Confluence spaces, knowledge base, templates | autonomous | irrelevant (kein Confluence) |
| atlassian-admin | Atlassian admin, users, permissions, security | autonomous | irrelevant (kein Atlassian) |
| atlassian-templates | Jira/Confluence templates, blueprints | autonomous | irrelevant |
| pm-skills | Bundle-Header (description out-of-date sagt 6, real 9) | none | nicht direkt nutzbar |

## engineering-skills Top-Level (32)

| Name | Trigger-Keywords | Activation | CoveLab-Use-Case |
|------|------------------|------------|------------------|
| senior-architect | system architecture, microservices, ADR, dependency analysis, scalability, tech stack | autonomous | ADR-Bau (0012 plus), F3.1 Repo-Refactor, Architektur-Decisions |
| adversarial-reviewer | adversarial review, breaks self-review monoculture, PR review | autonomous | F-Schritt-Plan kritisch reviewen vor Bau (Lock-Stabilitaet) |
| code-reviewer | code review TypeScript/JavaScript/Python/Go, complexity, SOLID, code smell | autonomous | Build-Script-Review (Python, bash) |
| tdd-guide | TDD, unit tests, mocks, coverage, Jest, Pytest | autonomous | Post-Lock-Release wenn Tests fuer Build-Scripts |
| senior-devops | CI/CD, infrastructure automation, containerization, IaC, deployment | autonomous | F2.4 GitHub Actions Push-Approval, F4.3 Auto-Routinen |
| senior-secops | application security, SAST/DAST, CVE remediation, secure development | autonomous | F2.4 API-Key-Provisioning, Permission-Architektur-Reviews |
| senior-security | threat modeling, STRIDE, OWASP, cryptography, security architecture | autonomous | F2.4 Secret-Management-Decisions |
| senior-prompt-engineer | prompt optimization, prompt templates, LLM eval, RAG, agentic systems | autonomous | F1.5 Subagent-Definitionen, F1.6 Hook-Patterns - HOHE Relevanz |
| tech-stack-evaluator | tech stack evaluation, TCO, security assessment, ecosystem health | autonomous | F3.2 covelab-foundation Stack-Wahl |
| cloud-security | cloud misconfig, IAM, S3 public, security group, IaC security | autonomous | irrelevant (Hostinger-VPS, kein Cloud-IaC) |
| ai-security | prompt injection, jailbreak, model inversion, data poisoning, MITRE ATLAS | autonomous | F2.4 claude-CLI-Sandboxing-Decisions |
| security-pen-testing | security audit, pentest, vulnerability scanning, OWASP Top 10 | autonomous | irrelevant fuer Fundament-Stage |
| threat-detection | threat hunting, IOC analysis, anomaly detection, telemetry | autonomous | irrelevant |
| red-team | red team, MITRE ATT-and-CK, kill-chain, offensive security | autonomous | irrelevant |
| incident-response | security incident triage, SEV classification, forensics | autonomous | irrelevant fuer Fundament-Stage |
| incident-commander | incident command (generic frontmatter) | autonomous | irrelevant |
| senior-frontend | React, Next.js, TypeScript, Tailwind, bundle optimization | autonomous | Phase 5 F5.5 Landing-Page falls Web-UI |
| senior-backend | REST APIs, microservices, database architecture, auth flows | autonomous | irrelevant (kein eigenes Backend im Universal-Fundament) |
| senior-fullstack | Next.js, FastAPI, MERN, Django scaffolding | autonomous | irrelevant |
| senior-qa | unit/integration/E2E tests, Jest, React Testing Library, coverage | autonomous | post-Lock-Release falls Tests |
| senior-ml-engineer | MLOps, model deployment, RAG, drift monitoring | autonomous | irrelevant |
| senior-data-scientist | A/B testing, causal inference, predictive analytics, statistical modeling | autonomous | irrelevant |
| senior-data-engineer | ETL/ELT, Spark, Airflow, dbt, Kafka, data modeling | autonomous | irrelevant |
| senior-computer-vision | CNN, Vision Transformer, YOLO, image segmentation | autonomous | irrelevant |
| aws-solution-architect | AWS serverless, CloudFormation, IaC | autonomous | irrelevant (Hostinger) |
| azure-cloud-architect | Azure infra, Bicep/ARM, Azure DevOps | autonomous | irrelevant |
| gcp-cloud-architect | GCP, GKE, Cloud Run, BigQuery | autonomous | irrelevant |
| ms365-tenant-manager | M365 admin, Azure AD, Teams, Exchange Online | autonomous | irrelevant |
| stripe-integration-expert | Stripe, payment integration | autonomous | irrelevant (kein Stripe) |
| email-template-builder | email templates (generic frontmatter) | autonomous | irrelevant |
| epic-design | 2.5D web, scroll storytelling, parallax, cinematic | autonomous | irrelevant |
| engineering-skills | Bundle-Header (description out-of-date sagt 23, real 32) | none | nicht direkt nutzbar |

## engineering-skills Bundles (5 Roots, 19 Sub-Skills)

Bundle-Roots sind separate Top-Level-Dirs neben skills/ - haben eigene SKILL.md plus nested skills/. Surfacing-Status: Bundle-Root surfaced wenn marketplace.json sie listet, Sub-Skills typisch nicht einzeln surfaced sondern via Bundle aktivierbar.

| Bundle | Sub-Skill-Count | CoveLab-Use-Case |
|--------|-----------------|------------------|
| a11y-audit | 1 | Phase 5 F5.5 Landing-Page WCAG-Compliance falls Web-UI |
| google-workspace-cli | 1 | irrelevant (kein Google Workspace) |
| playwright-pro | 10 (browserstack, coverage, fix, generate, init, migrate, playwright-pro, report, review, testrail) | Phase 5 Foundation-Demo-Tests falls automated E2E |
| self-improving-agent | 6 (extract, promote, remember, review, self-improving-agent, status) | HOHE Relevanz - auto-memory Curation passt direkt zur STOLPERSTEINE+session_log-Workflow. Re-Evaluation post-F1.6 |
| snowflake-development | 1 | irrelevant (kein Snowflake) |

## Nicht-Aktivierte Skills im Cache

- 18 ZIP-Bundles in engineering-skills/2.2.3/ (komprimiert, nicht entpackt, nicht surfaced)
- engineering-advanced-skills@claude-code-skills (Phase-2-Backlog, hohe Relevanz an F1.5/F2.1 erkannt - siehe patterns/plugin-evaluation-alirezarezvani.md)
- c-level-skills, marketing-skills, product-skills, finance-skills, business-growth-skills, ra-qm-skills - nicht installiert (FAHRPLAN-Treue)

## Drift-Notizen (Maintainer-Doku-Drift, BU+BW+BX-Lehre)

- pm-skills Bundle-Header description sagt 6 Skills - real 9 (Filesystem plus Surfacing konsistent)
- engineering-skills Bundle-Header description sagt 23 Skills - real 32 (Filesystem plus Surfacing konsistent)
- F1.3a Sub-Test-c-Befund "51 Filesystem" war recursive-find-Methodik-Artefakt (BX), nicht echte Drift
- Top-Level-Authority: ls auf Cache-Top-Level (Cache plus Source plus marketplace.json plus surfaced) - alle 4 Quellen konsistent

## References

- patterns/best-practice-skill-activation.md (3-Schichten Activation-Pattern)
- patterns/skill-activation-baseline.md (Tag 15 empirische Baseline, Post-Restart 54 surfaced)
- patterns/plugin-evaluation-alirezarezvani.md (pm-skills plus engineering-skills Bewertung)
- patterns/plugin-evaluation-anthropic-official.md (commit-commands Bewertung)
- reports/skills_recon_f13b_sub1b.md (Recon-Daten)
- STOLPERSTEINE.md BQ (Marketplace-Manifest-Key), BR (Snapshot-Coverage), BU+BW+BX (Drift-Klaerung)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F1.3a-F1.6

## Update-Konvention

Bei jeder Plugin-Aenderung (Install/Uninstall/Version-Update):
1. reports/skills_recon_*.md neu generieren (analog Sub-1b.1)
2. SKILL_INDEX.md aktualisieren mit Recon-Output
3. session_log.md Eintrag mit Aenderungs-Beschreibung
