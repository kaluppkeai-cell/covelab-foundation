# Best-Practice-Snapshot: inotify-Watcher + systemd-Integration

**Recherchiert:** 2026-05-13 (Tag 18, F4.4)
**Anlass:** F4.4 MCP-Write-Loop-Trigger — inotifywait-Watcher + systemd-Service fuer tasks/in/
**Quellen:** oneuptime.com (Maerz 2026), systutorials.com (April 2026), codelucky.com (Aug 2025), outcompute/inotifycron GitHub

## Pflicht-Elemente (alle Quellen einig)

### Install
```
apt install -y inotify-tools
```

### inotifywait-Event fuer File-Drop-Pattern
- **close_write** ist der korrekte Event fuer "File vollstaendig geschrieben" (nicht `create` — create feuert bevor Inhalt vollstaendig ist)
- `-m` = monitor-Modus (kein Exit nach erstem Event)
- `--format '%f'` = nur Filename ausgeben (kein Pfad-Prefix in Output)
- `--quiet` / `-q` = keine Info-Meldungen in Stdout

Korrekte Loop-Pattern:
```bash
inotifywait -m -e close_write --format '%f' /opt/covelab/tasks/in/ | while read -r filename; do
    # verarbeite $filename
done
```

### systemd Service Unit (minimales Produktions-Template)
```ini
[Unit]
Description=CoveLab Task Watcher
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/covelab
ExecStart=/opt/covelab/tools/task_watcher.sh
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

Deployment:
```bash
cp covelab-task-watcher.service /etc/systemd/system/
systemctl daemon-reload
systemctl enable covelab-task-watcher
systemctl start covelab-task-watcher
systemctl status covelab-task-watcher
journalctl -u covelab-task-watcher -f   # Live-Logs
```

### inotify Kernel-Limits (Ubuntu 24 Defaults ausreichend fuer CoveLab)
- max_user_watches default 8192 (Tasks-Dir mit wenigen Files: kein Problem)
- max_user_instances default 128
- Erhoehung nur noetig bei >500 parallelen Watches

## CoveLab-Bewertung

| Aspekt | Entscheidung | Begruendung |
|--------|-------------|-------------|
| Event-Typ | close_write | File vollstaendig nach MCP write_file |
| Tool | inotifywait (inotify-tools) | Standard Ubuntu, kein extra Daemon |
| Service-User | root | VPS single-user Setup, docker exec braucht root |
| Restart-Policy | always + RestartSec=5 | Resilient gegen claude -p Exit-Codes |
| Error-Dir | tasks/errors/ | Klar vom processed/-Verzeichnis getrennt |
| Loop-Style | pipe in while read | Einfachste stabile Variante, keine Race-Condition bei serial-Verarbeitung |
| Concurrency | seriell (eine Task pro Iteration) | Reicht fuer CoveLab-Volumen, verhindert Rate-Limit-Burning |

## Folge-Aktionen

- F4.4: task_watcher.sh + systemd-Unit committen + 3-Task-Smoke-Test
- F4.5: COORDINATION.md-Status-Loop (CTO prueft tasks/out/ bei jedem Turn)
