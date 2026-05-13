# Code-Prompt-Schema — CTO-zu-Code-Chat

**Erstellt:** 2026-05-13 (Tag 17, F4.0 Sub-4)
**Authority:** AGENTS.md (Code-Prompt-Hygiene-Sektion) + patterns/best-practice-token-efficiency.md
**Zweck:** Operativ-detailliertes Schema fuer CTO-zu-Code-Chat-Prompts mit File-Reference-Pattern.

---

## Problem (Pre-Sub-4)

CTO-Prompts enthielten Build-Script-Inhalt doppelt:
1. Im CTO-Chat-Context (CTO schreibt den Prompt)
2. Im Code-Chat-Context (Code-Chat empfaengt den Prompt)

Bei 50+ Zeilen Python/Bash = doppelte Token-Kosten plus Copy-Paste-Fehleranfaelligkeit.

---

## Loesung: File-Reference-Pattern (Sub-4-Standard ab Tag 17)

### Schwelle

Build-Scripts ueber 30 Zeilen: CTO schreibt Script via MCP zuerst, Code-Chat fuehrt Pfad aus.
Inline nur noch: single-line-SSH-Befehle, git-Kommandos, kurze Shell-Ketten unter 5 Zeilen.

### CTO-Workflow

1. CTO schreibt Script via MCP write_file nach /mnt/repo/tools/ (persistent, git-trackbar)
   ODER nach /mnt/repo/reports/ (einmaliger Recon, CC-Klasse-konform)
2. CTO-Prompt AUSFUEHRUNG enthaelt nur: Pfad + Aufruf + Pflicht-Checks
3. Code-Chat: py_compile-Check + python3-Ausfuehrung + git add/commit

### Prompt-Template (R13-konform)

```
TASK: [eine Aufgabe]
MODUS: Build | Bei Fehler: stopp + melden
CONTEXT: HEAD [SHA], ADR-Refs, Stolperstein-Warns [Liste: BK, BS, W, Q]
AUSFUEHRUNG:
    ssh hostinger 'python3 -m py_compile /opt/covelab/tools/build_TASK.py && echo AST_OK'
    ssh hostinger 'python3 /opt/covelab/tools/build_TASK.py'
    ssh hostinger 'cd /opt/covelab && git add PFAD1 PFAD2'
    ssh hostinger 'cd /opt/covelab && git diff --cached --name-only'
    ssh hostinger 'cd /opt/covelab && git commit -m "MSG"'
    ssh hostinger 'cd /opt/covelab && git log -1 --oneline'
PFLICHT-RUECKMELDUNG:
    1. AST_OK-Bestaetigung
    2. Script-Output (max 5 Zeilen)
    3. git diff --cached Dateiliste
    4. git log -1 --oneline
```

### Kein Inner-Backtick-Fence (CI-Lehre)

AUSFUEHRUNG-Block: plain-Text mit Indentierung, KEIN bash-Code-Fence innerhalb des Outer-Blocks.

---

## Script-Ablage-Konvention

| Zweck | Ziel-Pfad | git-tracked |
|-------|-----------|-------------|
| Einmaliger Recon/Report | /mnt/repo/reports/build_TASK.py | nein (.gitignore reports/) — git add -f bei Bedarf |
| Wiederverwendbares Build-Tool | /mnt/repo/tools/build_TASK.py | ja (ADR-0005-Allowlist tools/) |
| Temp-Script (Session-einmalig) | /tmp/build_TASK.py | nein (VPS-temp) |

Faustegel: >3 Wiederverwendungen oder Closure-relevantes Artefakt → tools/. Einmalig → /tmp/.

---

## Eingebettete Text-Templates (commit-msg, session_log-Append)

Statt Pseudo-Code-Skelett im Prompt: CTO schreibt Template-Files via MCP als separate .txt-Files.

Beispiel:
- /mnt/repo/reports/commit_msg_TASK.txt (CTO via MCP write_file)
- Code-Chat: git commit -F /opt/covelab/reports/commit_msg_TASK.txt

---

## Token-Einsparung (Referenz-Empirie)

- Build-Script 50 Zeilen inline: ~400 Token pro CTO-Prompt + ~400 Token im Code-Chat-Context = ~800 Token doppelt
- File-Reference-Pattern: ~20 Token (Pfad + Aufruf) + Script on-disk = ~760 Token gespart pro Sub-Schritt
- Bei 10 Sub-Schritten pro F-Schritt: ~7600 Token Einsparung (bei Sonnet 4.6: ca USD 0.023 pro F-Schritt)

Quelle: patterns/best-practice-token-efficiency.md Sektion 4 (Embedded-Build-Script-Cache-Killer).

---

## Bezug zu AGENTS.md

Dieses File ist operativ-detaillierte Erweiterung der AGENTS.md-Sektion "Code-Prompt-Hygiene (CTO-Pflicht)".
AGENTS.md bleibt knappe Konvention-Liste, dieses File ist das ausfuehrliche Nachschlagewerk.

## References

- patterns/best-practice-token-efficiency.md (Schicht-2+3 Recherche, F4.0 Sub-1)
- AGENTS.md "Code-Prompt-Hygiene (CTO-Pflicht)"
- STOLPERSTEINE.md CI (kein Inner-Backtick-Fence), BS (Build-Script-Pattern), Q (kein Inline-Heredoc)
- decisions/0005-permission-architecture-commit-yes-push-no.md (Allowlist tools/)
