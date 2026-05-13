# best-practice-mcp-rw-mount

Erstellt: 2026-05-12 (Tag 16 Mittag, F2.1 Sub-1)
Anlass: F2.1 MCP-rw-Mount-Aenderung erfordert Recherche-Pflicht (ADR 0010 Schicht 1.5 Coverage-Gap im RESEARCH_INDEX bestaetigt).
Quellen-Schichten: Schicht 1 (Anthropic Produkt-Wissen-Skill - MCP nicht Kern-Anthropic-Produkt, Routing zu Schicht 2/3) plus Schicht 2 (modelcontextprotocol/servers official README plus DeepWiki) plus Schicht 3 (Docker Docs bind-mount Mode plus Issues moby/awesome-compose).

## Quellen-Liste

1. github.com/modelcontextprotocol/servers/blob/main/src/filesystem/README.md
2. npmjs.com/package/@modelcontextprotocol/server-filesystem
3. github.com/modelcontextprotocol/servers/issues/3133 (Feature-Request base-dir plus allowed-dirs Trennung, open Stand 2025-12-17)
4. deepwiki.com/modelcontextprotocol/servers/2.2-filesystem-tools-reference
5. docs.docker.com/engine/storage/bind-mounts (Mount-Modes ro/rw, recursive-readonly Kernel-5.12-plus)
6. github.com/moby/moby/issues/47357 (Python-SDK read_only-Flag-Inkonsistenz-Randfall, Stand 2024-02)
7. github.com/docker/awesome-compose/issues/199 (Anonymous-Volume-Konflikt bei ro-Bind, Stand 2022-01)

## Pflicht-Elemente (Quellen-Konsens)

### 1. Zwei unabhaengige Schutz-Layer

Mount-Mode-Layer (Kernel/Docker): bind-mount-Flags rw (Default) oder ro (Suffix :ro oder read_only:true in compose). Wirkt vor jedem Schreib-System-Call - EROFS ist Kernel-Antwort.

Application-Layer (server-filesystem): Allowlist via Positional-Args (statisch) oder Roots-Protokoll (dynamisch). validatePath() prueft jeden Tool-Aufruf gegen Allowlist. Pfade ausserhalb sind fuer MCP-Tools unsichtbar.

Beide Layer wirken zusammen: Operation muss bei BEIDEN durchgehen. ro-Mount blockt selbst wenn Allowlist erlaubt. Allowlist-Restriktion blockt selbst wenn Mount rw ist.

### 2. Docker-Compose Mount-Mode Syntax

Kurz-Syntax:
- /host/path:/container/path:ro (read-only)
- /host/path:/container/path (read-write Default)

Lang-Syntax (empfohlen fuer Klarheit):
volumes:
  - type: bind
    source: /host/path
    target: /container/path
    read_only: true

Recursive-readonly fuer Submounts erfordert Linux-Kernel 5.12 oder neuer. Ubuntu 24 (Kernel 6.x) auf VPS srv1489118 erfuellt das.

### 3. Selektive Mount-Mode-Mischung

Moeglich via mehrere bind-mounts mit unterschiedlichen Modes auf demselben Container:
- Parent /opt/covelab zu /mnt/repo:ro
- Sub /opt/covelab/docs zu /mnt/repo/docs (rw-Overlay)
- Sub /opt/covelab/patterns zu /mnt/repo/patterns (rw)
- Sub /opt/covelab/decisions zu /mnt/repo/decisions (rw)

Tradeoffs:
- Pro: Defense-in-Depth, Kernel-Schutz fuer sensible Pfade (.env, .git, config, backups, .venv, *.bak) ist hart und nicht via Hook-Drift umgehbar.
- Con: Komplexere Konfiguration. Datei-Binds (STOLPERSTEINE.md, session_log.md) brauchen pro Datei einen separaten bind-mount und sind brittle bei rename oder Restart. Wildcards (HANDOVER_*.md) nicht unterstuetzt.

### 4. Allowlist via Positional-Args bleibt staerkster Application-Layer-Schutz

server-filesystem prueft validatePath() bei jeder Tool-Operation. Allowlist gesetzt via Positional-Args zur Startzeit (z.B. server-filesystem /mnt/repo). Pfade ausserhalb sind unsichtbar fuer alle Tools.

Roots-Protokoll erlaubt dynamische Aenderung zur Laufzeit aber wird vom Anthropic-Claude-Client als Standardpraxis nicht aktiv genutzt - Default ist statische Allowlist aus Positional-Args.

## Konkrete CoveLab-Bewertung

### Aktuelle Konfiguration (Stand Tag 14 BN-Stolperstein, zu verifizieren in Sub-2)

- Image: covelab/mcp-fs:0.1 (custom node:22-alpine plus mcp-proxy plus server-filesystem).
- Mount: /opt/covelab zu /mnt/repo mit rw=false (BJ-EROFS, BN docker-inspect bestaetigt).
- Allowlist Positional-Arg vermutlich /mnt/repo (einziger Pfad - zu verifizieren in Sub-2-Recon).

### Empfehlung Variante A - Single-Mount-Flip

Architektur: /opt/covelab zu /mnt/repo:rw (einziger bind-mount, ro-Flag entfernen). Schutz weiter via:
1. Application-Layer-Allowlist (server-filesystem Positional-Args) - aktuell /mnt/repo, eventuell post-F4.1-Cleanup auf engere Liste eingrenzen.
2. F1.6-Hook-Erweiterung (pre_tool_use_covelab.py BLOCK_PATTERNS) - Loose-End F1.6-Sub-3 Erweiterung auf vollstaendige ADR-0005-Top-Level-Allowlist (F4.1-Cleanup-Begleit-Task). Wirkt nur fuer Code-Chat-Operationen, nicht fuer CTO-MCP-direct.
3. CTO-Self-Check R36 Punkt 4 (ADR-0005-Konformitaet) vor jedem write_file-Aufruf.
4. Git-Diff-CEO-Pre-Push-Review vor origin/main-Sync.

Effort: 1-2h (reine docker-compose oder docker-run Konfig-Edit plus Container-Restart).

### Variante B - Selektive Mount-Mode-Mischung

Architektur: /opt/covelab zu /mnt/repo:ro plus rw-Overlay-Mounts fuer docs/ plus patterns/ plus decisions/. Top-Level-Dateien (STOLPERSTEINE.md, session_log.md, HANDOVER_*) via Datei-Binds einzeln gemountet.

Effort: 3-4h. Komplexere Konfiguration, brittle bei rename/Restart Datei-Bind-Operationen.

### Empfehlung Variante A wegen folgenden Gruenden

1. BN-Stolperstein-Lehre: F2.1 reine Konfig-Aenderung 1-2h, nicht 3-4h Mount-Mischung.
2. UNIVERSAL_NORDSTERN-Klonbarkeit: Universal-Fundament soll fuer Fremd-Anwender klonbar sein. Komplexe Mount-Mischung verkompliziert Phase-5-Onboarding-Doku.
3. Defense-in-Depth ausreichend via 4 Application-Layer (Allowlist plus Hook plus Self-Check plus Git-Diff-Review). Kernel-Hardening hat Grenzwert wenn Top-Level-Dateien betroffen sind.
4. STOLPERSTEINE.md plus session_log.md plus HANDOVER_* sind Top-Level-Dateien die in jedem Fall schreibbar sein muessen - Datei-Bind-Aufwand pro Datei plus Wildcard-Mangel macht Variante B unsauber.
5. Verbots-Pfade (.env, config/, .git/, backups/, archive/, .venv/, *.bak*) werden ueber Application-Layer-Allowlist-Engrenzung plus Hook-PreToolUse-BLOCK_PATTERNS abgedeckt (F4.1-Cleanup-Backlog plus F1.6-Hook-Erweiterung).

### Sekundaere Hardening-Optionen (post-F2.1, NICHT jetzt)

- Application-Layer-Allowlist auf engere Liste eingrenzen (Pflicht-Lese-Pfade plus Schreibe-Pfade getrennt) - braucht zwei server-filesystem-Instanzen oder Custom-Fork. Backlog.
- Pre-Commit-Hook im Repo der ADR-0005-Allowlist erzwingt (server-side git-Hook unter /opt/covelab/.git/hooks/pre-commit). F4.3-Backlog.
- CI-Pruefung im GitHub Actions Push-Approval-Workflow. F2.4-Backlog.

## Folge-Aktionen (F2.1 Sub-Sequenz)

1. Sub-1 (dieser Snapshot): Recherche plus Snapshot committen via Code-Chat-Prompt (CTO-MCP read-only bis Sub-3 done).
2. Sub-2 Code-Chat-Recon: docker inspect mcp-fs Mounts-Sektion plus Suche nach docker-compose.yml oder docker run Skript fuer mcp-fs (z.B. /opt/covelab/docker/mcp-fs/ oder systemd-Unit oder /opt/covelab/scripts/). Output reports/f21_mcp_recon.md.
3. Sub-3 CEO Container-Lifecycle (AZ-Lehre): docker-compose oder Skript-Patch nach Sub-2-Befunden, CEO restartet Container via lokale PowerShell. CTO bereitet 1:1-kopierbaren Restart-Befehl vor.
4. Sub-4 CTO MCP-Test: Nach Restart Test-write_file auf /opt/covelab/patterns/-Pfad. Wenn pass: F2.1 fail-zu-pass plus BN-Stolperstein-Update plus session_log-Eintrag.

## Coverage-Klassifikation

operativ-detailliert: enthaelt konkrete docker-compose-Syntax (Kurz plus Lang), Mount-Mode-Mischung-Tradeoffs, server-filesystem-Allowlist-Mechanik, CoveLab-Variante-A/B-Vergleich mit Empfehlung-Begruendung, Sub-Sequenz-Plan mit konkreten Operationen.

## Stolperstein-Bezuege

- BJ (covelab-MCP-Mount-EROFS): Ziel-Aufloesung dieses F-Schritts.
- BN (MCP-Tool-Sichtbarkeit ist nicht gleich MCP-rw-Mount): Praezision dass Approval-Layer und Mount-Layer unabhaengig sind. Bestaetigt durch Schicht-2-Doku.
- AZ (Container-Lifecycle CEO-only): Sub-3 ist explizit CEO-Operation via lokale PowerShell.
- AY (Caddy ACME retry-loop nach Reload): analoge Lehre - bei Mount-Aenderung Container-Restart noetig, nicht reload. mcp-fs ist node-Container ohne Caddy-Bezug aber gleiches Anti-Pattern (Stop/Restart statt Hot-Reload).

## References

- decisions/0008-mcp-server-variante-c-mvp.md (mcp-fs-Image-Architektur)
- decisions/0009-schreib-faehigkeit-foundation.md (F-Schritt-Anker, jetzt UNIVERSAL_FUNDAMENT_FAHRPLAN F2.1)
- decisions/0010-best-practice-recherche-pflicht.md (Schicht 1.5 Coverage-Check-Pflicht)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F2.1
- STOLPERSTEINE.md BJ plus BN plus AZ
- docs/RESEARCH_INDEX.md (Coverage-Gap-Update mit diesem Commit)
