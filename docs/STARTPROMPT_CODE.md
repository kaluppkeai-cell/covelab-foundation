# STARTPROMPT_CODE

**Verwendung:** Session-Start fuer neuen Code-Chat. CEO kopiert 1:1 in Code-Chat.
**Letzter Update:** 2026-05-13 (Tag 17, F4.2b — r22-reader-Integration, Sections 4-15 ersetzt)
**Vorgaenger:** Volltext-15-Sektionen-Block (~78K Token). Jetzt: Bash S1-S3 + r22-reader (~5-10K Token).

```
TASK: Session-Start-Block fuer neuen CTO-Chat generieren.
Bash-Sektionen 1-3 direkt ausfuehren. R22-Kontext via r22-reader-Subagent.

MODUS: Read-only. Kein Write/Commit/Push.

CONTEXT:
- r22-reader Subagent verfuegbar: /opt/covelab/.claude/agents/r22-reader.md
- r22-reader liest alle R22-Pflicht-Files und gibt kompakten 500-1000-Token-Summary zurueck.
- Sections 1-3 bleiben als direkte Bash-Ausfuehrung (Wallclock-Triple-Check, Git, Push-Befehl).

AUSFUEHRUNG:
cd /opt/covelab/ || { echo FATAL_NO_REPO; exit 1; }
UTC_NOW=$(date -u +%Y-%m-%dT%H:%M:%SZ)
UTC_DATE=$(date -u +%Y-%m-%d)

echo "============================================"
echo "SESSION-START-BLOCK CTO-Chat"
echo "Generiert: $UTC_NOW"
echo "============================================"

echo
echo "=== S1: WALLCLOCK ==="
echo "System UTC:       $(date -u)"
echo "RTC:              $(timedatectl | grep -i 'rtc time' | sed 's/^[ \t]*//')"
echo "Google HTTP date: $(curl -sI https://www.google.com 2>&1 | grep -i '^date:' | sed 's/^[Dd]ate: //')"
echo "VPS-Datum heute:  $UTC_DATE"

echo
echo "=== S2: GIT-STAND ==="
git fetch origin main 2>&1 | tail -2
LH=$(git rev-parse HEAD)
OH=$(git rev-parse origin/main)
echo "Local HEAD:  $LH"
echo "Origin HEAD: $OH"
echo "Commit-Msg:  $(git log -1 --format=%s HEAD)"
echo "Sync:        $([ "$LH" = "$OH" ] && echo SYNCED || echo DIVERGED)"
echo "Working-Tree:"; git status --short | head -10
echo "Letzte 5 Commits:"; git log --oneline -5

echo
echo "=== S3: PUSH-BEFEHL (CEO-only, nicht Code-Chat) ==="
echo 'ssh hostinger "cd /opt/covelab && git push origin main"'

echo
echo "=== S4-S15: R22-KONTEXT via r22-reader-Subagent ==="

Rufe jetzt den r22-reader-Subagenten auf.
Gib dessen Output direkt und ungekuerzt zurueck.
Kein Preamble vor dem r22-reader-Output, kein Postamble danach.

echo
echo "============================================"
echo "ENDE SESSION-START-BLOCK | $(date -u +%Y-%m-%dT%H:%M:%SZ)"
echo "============================================"

PFLICHT-RUECKMELDUNG:
1. Wallclock S1 ausgegeben (System + RTC + Google).
2. Git S2 ausgegeben (HEAD + Sync-Status + letzte 5 Commits).
3. Push-Befehl S3 ausgegeben.
4. r22-reader-Output ausgegeben (500-1000 Token Summary).
5. Bei FATAL_NO_REPO: STOPP, melden, kein Output-Versand.
6. Bei r22-reader-Fehler: Fehlermeldung ausgeben, Sections S1-S3 trotzdem liefern.
```
