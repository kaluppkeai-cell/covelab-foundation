# Best-Practice-Snapshot: VPS-seitiger Push-Approval-Mechanismus

**Recherchiert:** 2026-05-13 (Tag 18, F2.6)
**Anlass:** F2.6 Push-Approval — CEO-Entscheidung: alles auf VPS, keine GitHub-Actions-Abhaengigkeit
**Quellen:** Recherche GitHub Actions (Vorlauf), CEO-Direktive VPS-First, Caddy-Docs, Flask-Patterns

## Entscheidung: VPS-Web-Dashboard statt GitHub Actions

GitHub Actions Required-Reviewers (mobile Push-Notification) benoetigt:
- Oeffentliches Repo ODER GitHub Team-Plan fuer private Repos
- Haengigkeit von GitHub-Infrastruktur
- Self-Hosted-Runner-Setup (zusaetzlicher systemd-Service)

VPS-Web-Dashboard (gewaehlt):
- Funktioniert auf jedem Plan, komplett lokal
- Caddy (bereits vorhanden) uebernimmt TLS + Basic-Auth
- Flask-App (50 Zeilen) laeuft als systemd-Service
- CEO oeffnet push.covelab.tech auf Handy: Commits pruefen + Push-Button

## Architektur

```
CEO Handy Browser
    |
    HTTPS push.covelab.tech
    |
    Caddy (TLS + Basic-Auth)
    |
    Flask App :8765 (localhost-only)
    |
    git push origin main (auf /opt/covelab)
```

## Caddy Block (push.covelab.tech)

```
push.covelab.tech {
    basicauth {
        ronald <BCRYPT_HASH>
    }
    reverse_proxy localhost:8765
}
```

Hash-Generierung: `caddy hash-password --plaintext PASSWORT`
Caddy-Doku: https://caddyserver.com/docs/caddyfile/directives/basicauth

## Flask App (push_dashboard.py)

Kernfunktionen:
- GET / → zeigt git log origin/main..HEAD --oneline (pending Commits)
- POST /push → fuehrt git push origin main aus, zeigt Ergebnis
- Kein eigenes Auth-Management (geht an Caddy)
- Laeuft auf 127.0.0.1:8765 (nur localhost, kein direkter Zugriff von aussen)

## Sicherheits-Pflicht-Elemente

- Basic-Auth via Caddy (bcrypt, nicht plaintext)
- Flask bind auf 127.0.0.1 (NICHT 0.0.0.0 — kein direkter externer Zugriff)
- R34: Passwort NIE in Git oder Chat
- Stolperstein T-Analogie: Output (Push-Ergebnis) via Python capture, nicht Shell-Redirect

## Folge-Aktionen

1. CEO: DNS A-Record push.covelab.tech → 187.124.164.242
2. Code-Chat: Flask-App + systemd-Service deployen (push-dashboard.service)
3. Code-Chat: Caddy-Config push.covelab.tech Block + reload
4. CEO: Passwort setzen (caddy hash-password, in Caddy-Config eintragen)
5. Test: push.covelab.tech auf Handy oeffnen, Login, Push-Button testen
