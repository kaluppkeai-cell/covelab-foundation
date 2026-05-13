# Best-Practice-Snapshot: VPS-only + Mobile-Bedienung

**Recherchiert:** 2026-05-10 (Tag 13 Spaetnachmittag-2, CTO-Chat) + Tag-14-Update Tag 14 morgens
**Anlass:** CEO-Vision "alles auf Hostinger, Handy-Bedienung" — Phase 2 / 4 Architektur-Entscheidung
**Quellen:** code.claude.com Remote-Control + Web + Routines, mindstudio.ai routines + headless-mode + dispatch, builder.io claude-code-mobile (Maerz 2026), claudefa.st VPS-Setup + scheduled-tasks + remote-control, amux.io claude-code-headless (April 2026), virtua.cloud + hostinger.com Claude-Code-VPS-Hosting, stanislas.blog Netclode (Februar 2026), GitHub-Actions-Approval-Patterns, code.claude.com automate-work-with-routines

## Drei Anthropic-offizielle Modi fuer Mobile-Bedienung 2026

### 1. Remote Control (Februar 2026)
- Lokale Session bridgt zu iOS / Android via QR-Code
- Code, Files, MCP bleiben LOKAL
- Claude Mobile App wird zum Window in lokale Session
- Voraussetzung: Lokales Geraet muss laufen
- Verworfen fuer CoveLab: eliminiert lokales Geraet NICHT

### 2. Claude Code on the Web (Cloud Sandbox)
- Sessions in Anthropic-managed VMs
- Full Mobile-Zugriff via claude.ai
- Restriktionen: nur GitHub-Repos (kein self-hosted Git), Code-Upload zu Anthropic-Cloud, Network-Access standardmaessig restriktiv
- Pro / Max / Team-Plan
- Verworfen fuer CoveLab: Vendor-Lock, kein Self-hosted-VPS-Integration

### 3. Headless claude -p auf VPS (Self-hosted)
- claude-CLI installiert auf eigener Infrastruktur
- Trigger via SSH, cron, inotify, Webhook, oder File
- API-Key statt Max-Subscription (Industry-Recommendation: Max scoped fuer first-party Claude Code Invocations)
- Vorteil: alles auf eigenem Server, keine Vendor-Lock
- Nachteil: Setup-Aufwand, Auth-Management
- **EMPFOHLEN fuer CoveLab**

## CoveLab-Empfehlung: MCP-Write-Loop-Pattern (CEO-Idee Tag 13)

Statt Telegram-Bot oder externer Webhook:
1. CEO und CTO im claude.ai Mobile App (existing UI, kein extra Channel)
2. CTO schreibt via MCP write_file nach /opt/covelab/tasks/in/task_TIMESTAMP.json
3. inotify-Watcher auf VPS triggert claude -p Headless-Wrapper
4. claude -p arbeitet ab, schreibt Result nach /opt/covelab/tasks/out/result_TIMESTAMP.json
5. CTO liest via MCP read_file, praesentiert dem CEO im Chat

Vorteile:
- Single-UI fuer CEO (nur claude.ai mobile)
- Keine externe Bot-Abhaengigkeit
- Datenfluss bleibt im gleichen System (VPS → MCP → CTO-Chat)
- Async-Notifikation via COORDINATION.md-Status-Loop (CTO prueft bei jedem Turn)
- Push-Approval via GitHub Mobile App native Push-Notifications

## Push-Automation Pattern

Statt CEO-PowerShell-Push:
- GitHub Actions Workflow: trigger on push to main
- Self-Hosted Runner ODER GitHub-managed Runner
- Approval via GitHub Mobile App Push-Notification
- Required Reviewer = CEO (single-source-of-truth fuer Approval)
- Approval-Webhook optional an MS Teams / Slack / Custom-API

## Sicherheits-Checklist (amux + Industry Pitfalls)

- timeout 30m als hard cap (Stuck-Agent-Schutz)
- --dangerously-skip-permissions NUR im sandboxed Container
- Exit-Code pruefen, nicht nur Output teen
- Concurrency-Cap (xargs -P, semaphore, queue) — verhindert Rate-Limit-Burning
- Cost-Limit (Anthropic-API Budget-Alert) — verhindert Retry-Loop-Cost
- Niemals ~/.claude/ im Container schreiben (corrupt session state)
- ANTHROPIC_API_KEY via env var, NICHT build arg (build args land in image layers)
- Pin npm-Version (zB @anthropic-ai/[email protected]) fuer reproduzierbare Builds

## Bessere Optionen aktuell nicht verwendet (dokumentiert)

- **Anthropic Routines** (Cloud Cron via claude.ai): Vendor-Lock, Anthropic-managed-Infra, gut fuer einfache Schedules. Verworfen weil wir Self-hosted wollen.
- **Claude Code on the Web**: bessere Mobile-UX laut Stan-Blog (stanislas.blog). Verworfen wegen Vendor-Lock + GitHub-only Restriktion.
- **Telegram-Bot via Dispatch / Channels** (Anthropic-offiziell): extra UI fuer CEO + Bot-Maintenance + externer Channel. Verworfen weil MCP-Write-Loop sauberer ist.
- **Slack / Discord Channels** (Anthropic-offiziell): gleiche Argumente wie Telegram.
- **Netclode** (Stan-Blog Self-hosted iOS-App + Kubernetes + Tailscale): Komplexitaet, neuer Stack, mehrere SDKs. Backlog post-Lock.
- **Happy / Nimbalyst** (third-party Mobile-Apps): erfordern lokale Session, eliminieren Cockpit nicht.
- **Hostinger Claude-Code-Hosting-Plan + Kodee-MCP-Agent**: Vendor-Lock auf Hostinger-spezifische Tools. Wir nutzen Hostinger-VPS aber nicht das Kodee-Layer (eigenes mcp-fs).
- **MindStudio** (no-code Agent-Builder + 1000+ Integrationen): externe Plattform, Vendor-Lock, monatliche Subscription.
- **Trigger.dev**: event-driven Platform fuer agentic tasks. Vendor-Lock.
- **OpenCode auf VPS**: Alternative-Stack, nicht Claude-native, broader Provider-Choice.
- **Cline** (VS-Code-Extension Open-Source): Editor-first, nicht Headless-fokussiert.
- **OpenAI Codex Web**: Cloud Sandbox mit ChatGPT iOS-App-Integration. Vendor-Wechsel von Claude.
- **GitHub Actions auf Cloud-Runner**: token-Cost Anthropic-API plus runner-minutes. Wir bevorzugen Self-Hosted-Runner auf VPS.
- **Anthropic Managed Agents** (cloud abstraction): zu wenig Kontrolle fuer Self-hosted-Vision.

## Pflicht-Anpassungen vor Implementation

- F2.1 (CTO-Schreibrechte rw-Mount) ist kritisch — ohne MCP-Write keine MCP-Write-Loop
- F2.4 muss Anthropic-API-Key-Provisioning als Secret-Management abdecken
- F4.5 (NEU): COORDINATION.md-Status-Loop sodass CTO bei jedem Turn pruefen kann ob neue Results da sind
- Hooks-System (F1.6) sollte SessionEnd-Hook fuer Backup automatisch triggern

## Folge-Aktionen Plan-Update

- F2.4 Spec-Erweiterung: API-Key-Provisioning + Push-Automation
- F2.5 Spec: Local-Cockpit-Eliminierung (alle Build-Scripts auf VPS migrieren, lokale settings.json auf VPS)
- F4.4 Umgewandelt: MCP-Write-Loop statt Telegram-Bot (Effort 2h)
- F4.5 Neu: COORDINATION.md-Status-Loop fuer Async-Notifikation (1h)

## Tag-14-Update (heute Morgen Erkenntnis)

covelab-MCP zeigt write_file und edit_file Tools im CTO-Chat sichtbar verfuegbar. Approval-Dialog funktioniert in Desktop-App (nicht in Mobile-App). Test-Schreibe-Versuch liefert dennoch EROFS read-only file system. Bestaetigt: **Approval-Layer (Tool-Sichtbarkeit + User-Confirm) und Mount-Layer (Container-rw-Konfig) sind unabhaengig**. F2.1 unveraendert fail. Aufwand drastisch reduziert auf 1-2h: nur mcp-fs Docker-Compose mount on rw fuer Allowlist-Pfade aendern, kein neuer ADR noetig, kein Code-Aufwand.
