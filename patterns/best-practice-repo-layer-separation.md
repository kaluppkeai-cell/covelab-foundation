# Best-Practice-Snapshot: Repo-Layer-Separation Framework vs Project

**Recherchiert:** 2026-05-13 (Tag 19, F3.1 Recherche-Pflicht ADR 0010)
**Anlass:** F3.1 Repo-Verzeichnis-Refactor /framework + /project - Schicht 1+2+3 vollstaendig.
**Quellen-Schichten:**
- Schicht 1: Anthropic-Produkt-Wissen (code.claude.com/docs, CLAUDE.md Loading-Mechanik)
- Schicht 2: Industry (git subtree offizielle Doku, MonoRepo-Patterns, AGENTS.md Standard)
- Schicht 3: Gegenstimmen (Migration-Complexity, Claude Code-Spezifika-Konflikte)
**Coverage:** operativ-detailliert
**F-Schritt-Relevanz:** F3.1, F3.2, F3.3

---

## KRITISCHER BEFUND (FAHRPLAN-Konflikt)

**FAHRPLAN F3.1 sieht vor:** CLAUDE.md und .claude/ nach /framework/ verschieben.

**HARD CONSTRAINT (Anthropic offiziell, code.claude.com/docs/en/claude-directory, verif. 2026-05-13):**
CLAUDE.md MUSS an Repo-Root bleiben. .claude/ MUSS an Repo-Root bleiben.

Gruende:
1. Claude Code liest CLAUDE.md automatisch nur aus `current working directory` (cwd) und Eltern-Verzeichnissen bis zur Repo-Root. Eine CLAUDE.md unter /framework/CLAUDE.md wird nur gelesen wenn Claude Code mit cwd=/framework/ gestartet wird - das ist nicht unser Setup (wir starten aus /opt/covelab/).
2. .claude/ (agents/, hooks/, skills/, settings.json) wird von Claude Code AUSSCHLIESSLICH aus cwd geladen. Verschieben nach /framework/.claude/ macht ALLE Hooks + Agents + Skills unsichtbar fuer Code-Chat.

**KONSEQUENZ:** F3.1-Scope muss vor Code-Beginn revidiert werden. CLAUDE.md und .claude/ bleiben an Root. Nur Doku-Files (patterns/, decisions/, docs/, etc.) werden verschoben. CEO-Entscheidung erforderlich.

---

## 1. CLAUDE.md Loading-Mechanik (Anthropic offiziell)

Quelle: code.claude.com/docs/en/claude-directory, code.claude.com/docs/en/best-practices (verif. 2026-05-13)

- Claude Code startet Session: Directory-Walk von cwd AUFWAERTS bis Repo-Root.
- Alle gefundenen CLAUDE.md werden KONKATENIERT (nicht ovverriding).
- Subdirectory CLAUDE.md (z.B. /src/CLAUDE.md): on-demand, nur geladen wenn Claude Code in diesem Subdirectory arbeitet.
- CLAUDE.md muss UPPERCASE sein (CLAUDE.md nicht claude.md - Filename-Case-Sensitivity).
- .claude/ liegt neben CLAUDE.md an der Root. Es gibt KEIN Support fuer .claude/ an einem Nicht-Root-Ort.
- CLAUDE.local.md: persoenliche Overrides, gitignored, an Root.

Implikation fuer Monorepo-Struktur (parent CLAUDE.md + sub-package CLAUDE.md):
- root/CLAUDE.md: Org-weite Regeln
- root/packages/api/CLAUDE.md: API-spezifische Regeln (on-demand geladen wenn cwd=api/)
- root/.claude/: gilt fuer die gesamte Repo
Dies ist das offizielle Multi-Layer-Pattern, NICHT /framework/.claude/.

---

## 2. AGENTS.md ist KEIN natives Claude Code Format (Stand April/Mai 2026)

Quelle: hivetrail.com/blog/agents-md-vs-claude-md (verif. 2026-05-13), GitHub Issue #6235.

- AGENTS.md ist ein offener Standard (Linux Foundation AAIF, 2025, Sourcegraph + OpenAI + Google + Cursor + Factory).
- Claude Code unterstuetzt AGENTS.md NICHT nativ (GitHub Issue #6235, tausende Upvotes, kein Timeline von Anthropic per April 2026).
- Standard-Workaround: Symlink `ln -s AGENTS.md CLAUDE.md` (bewirkt: Claude Code liest AGENTS.md-Inhalt als CLAUDE.md).
- CoveLab-Fazit: Unser AGENTS.md ist ein EIGENES Framework-Organisations-Dokument, nicht Claude-Code-nativ. Das ist OK fuer unseren Use-Case (CTO-Chat liest es via MCP, CLAUDE.md importiert es als Text-Referenz). Kein Change noetig.

---

## 3. Git Subtree Split fuer F3.2 Extraktion

Quellen: manpages.ubuntu.com/git-subtree, atlassian.com/git/tutorials/git-subtree, ariya.io (verif. 2026-05-13)

git subtree split ist der Standard-Weg fuer "Subdirectory in neues Repo extrahieren mit History-Erhalt":

```
# Step 1: Branch mit nur /framework/-History erstellen
git subtree split --prefix=framework -b framework-only

# Step 2: Neues leeres Repo erzeugen (auf GitHub: kaluppkeai-cell/covelab-foundation)
# Step 3: Framework-Branch in neues Repo pushen
cd /tmp/covelab-foundation && git init
git pull /opt/covelab framework-only
git remote add origin git@github.com:kaluppkeai-cell/covelab-foundation.git
git push -u origin main
```

Eigenschaften:
- History-Erhalt: nur Commits die /framework/ betreffen werden in neues Repo uebernommen.
- Keine .gitmodule-Overhead (Vorteil ggue. git submodule).
- Wiederholbar: erneuter split produziert identische Commit-IDs (deterministic).
- Nachteil: groessere Repo-Groesse weil History eingebettet.

Wann Subtree vs Submodule:
- Subtree: code soll Teil des Parent-Repos werden, externe Herkunft transparent (unser Fall F3.2).
- Submodule: explizite Trennung gewuenscht, strict version-pinning noetig, separation-of-concerns wichtig.

Fuer CoveLab: git subtree split ist klar richtig (F3.2 Extraktion fuer Verkauf + Demo). git submodule wuerde komplexen Sync-Aufwand bedeuten.

---

## 4. Revised F3.1 Design (Empfehlung basierend auf Constraints)

### 4a. Was BLEIBT an Root (unveraenderlich)

- CLAUDE.md (Root-Requirement Anthropic)
- AGENTS.md (unser Framework-Org-Dok, Root-Referenz in CLAUDE.md)
- .claude/ (Root-Requirement Anthropic fuer Hooks/Agents/Skills)
- UNIVERSAL_NORDSTERN.md (Vision-File, aktiv referenziert in session_start_covelab.py via relativer Pfad)
- COORDINATION.md (aktiv referenziert in session_start_covelab.py + session_end_covelab.py)
- STOLPERSTEINE.md (aktiv referenziert in r22-reader-Subagent)
- session_log.md (aktiv referenziert in r22-reader-Subagent + COORDINATION)
- HANDOVER_*.md (aktiv referenziert in r22-reader + STARTPROMPT)
- README.md (Repo-Root-Konvention)
- .gitignore

### 4b. Was verschoben werden KANN nach /framework/

Framework-Layer-Docs die KEINE aktiven Script-Pfad-Referenzen haben:
- decisions/0007, 0010, 0011 (Universal-Process-ADRs)
- patterns/best-practice-*.md (Recherche-Snapshots, keine Script-Referenzen)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md
- docs/STARTPROMPT_CTO.md, STARTPROMPT_CODE.md
- docs/SKILL_INDEX.md, RESEARCH_INDEX.md

### 4c. Was verschoben werden KANN nach /project/

CoveLab-spezifische Docs:
- decisions/0001-0006 (CoveLab-spezifische ADRs)
- docs/TECHNICAL_LESSONS.md, POSTGRES_OPERATIONS.md
- patterns/ CoveLab-spezifische (cleanup-konvention.md, test-mobile-only.md)
- reports/ (CoveLab-Recon-Outputs)

### 4d. Was NICHT verschoben werden sollte (Risiko > Nutzen)

Files mit aktiven Script-Pfad-Referenzen in Hooks, Subagents, Headless-Wrapper:
- UNIVERSAL_NORDSTERN.md (session_start_covelab.py liest es per relativem Pfad)
- COORDINATION.md (session_start + session_end + log_task_result.py liest/schreibt)
- STOLPERSTEINE.md (r22-reader.md Subagent referenziert)
- session_log.md (r22-reader.md + session_end Hook)
- .claude/ gesamt (absolute Pflicht an Root)

Aufwand fuer Pfad-Updates in allen Scripts waere gross und Fehler-anfaellig (Stolperstein CK-Klasse).

### 4e. Minimal-Viable-F3.1 Empfehlung

Option A (Maximal - vollstaendige Verzeichnis-Trennung): Alle oben genannten Files verschieben, ALLE Script-Pfad-Referenzen updaten, README-Updates, Cross-Reference-Sweep. Effort: 6-8h realistisch.

Option B (Minimal - nur Docs-Trennung, Operatives bleibt an Root): /framework/docs/ + /framework/decisions-universal/ + /framework/patterns/ als Ziel. Root-Files (Vision, Coordination, Logs, .claude/) bleiben. Effort: 2-3h. Klarer Nutzen fuer F3.2 git subtree split.

Option C (Symbolic-Separation ohne Datei-Verschiebung): LAYER-MAP.md an Root erklaert welche Files Framework-Layer vs Project-Layer sind. /framework/ und /project/ enthalten nur README-Pointer-Files. Kein Migrations-Risiko. Effort: 30min. ABER: git subtree split kann /framework/ dann NICHT sauber extrahieren (weil die eigentlichen Dateien noch an Root liegen). Nicht empfohlen fuer F3.2.

**Empfehlung: Option B** - Docs-Trennung macht git subtree split vorbereitet, ohne Script-Risiko.

---

## 5. CLAUDE.md @import Syntax

Quelle: code.claude.com/docs (verif. 2026-05-13), medium.com/@n913239 (verif. 2026-05-13)

Claude Code unterstuetzt @import in CLAUDE.md fuer File-Referenzen:
```
@/framework/docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md
```
Beim Session-Start laedt Claude Code den referenzierten File-Inhalt in Context.

Nutzen fuer CoveLab: Root-CLAUDE.md kann duenn bleiben + Framework-Docs via @import referenzieren. Das ist auch ein Token-Disziplin-Tool (nur geladen wenn CLAUDE.md in Context, nicht zusaetzlich via r22-reader).

Caveat: @import erhoeht Context pro Session. Nicht fuer alle Framework-Docs, nur fuer aktiv-session-relevante (z.B. UNIVERSAL_NORDSTERN-Summary).

---

## 6. Gegenstimmen (Schicht 3)

Quelle: medium.com/@bijit211987 Complete Guide to CLAUDE.md (Mai 2026), feature-sliced.design/blog/monorepo (Dez 2025).

- "Start with the messier structure that works, not the ideal structure you hope to build toward." Refactoring live Repos mit vielen Script-Referenzen ist riskant. Validierung nach jedem Move ist Pflicht.
- Subdirectory CLAUDE.md laden nur on-demand (wenn cwd im Subdirectory). Fuer unseren Headless-Wrapper (cwd=/opt/covelab/) werden /framework/CLAUDE.md und /project/CLAUDE.md NICHT automatisch geladen.
- git subtree split verlangsamt sich bei grossen Repos. Bei 60+ Commits (CoveLab Stand Tag 19) ist das moderat OK, aber Laufzeit pruefen.
- Verzeichnis-Refactors brechen alle bestehenden Bookmark-Pfade in HANDOVER-Files, STOLPERSTEINE-Eintraegen, r22-reader. Vollstaendiger Pfad-Sweep nach Refactor ist Pflicht.

---

## 7. Konsequenzen fuer F3.1 Scope-Revision

CEO muss entscheiden welche Option (A/B/C). Empfehlung CTO: Option B.

Pflicht-Aktionen vor Code-Start:
1. CEO-Entscheidung Option A/B/C
2. FAHRPLAN F3.1-Scope-Update (CLAUDE.md + .claude/ bleiben an Root = Pflicht-Korrektur)
3. Bei Option B: Pfad-Liste der zu verschiebenden Files definieren (CTO liefert)
4. Dry-Run via git mv --dry-run vor echtem Commit
5. Vollstaendiger grep-Sweep nach alten Pfaden nach Refactor (z.B. grep -r "decisions/0007" /opt/covelab/)

---

## 8. Tool-Empfehlung F3.2 (nach F3.1)

git subtree split ist der richtige Weg. Kein Bedarf fuer zusaetzliche Tooling (Nx, Turborepo, Lerna - diese sind fuer JavaScript-Package-Management, nicht unser Use-Case).

Nach F3.1 (wenn /framework/ existiert mit Framework-Docs):
```
git subtree split --prefix=framework -b covelab-foundation-init
```
Dann neues Repo covelab-foundation auf GitHub anlegen, Branch pushen.

---

## 9. Stolperstein-Warnungen fuer F3.1

- CK-Klasse: alle Pfad-Referenzen in Scripts muessen gecheckt werden (grep vor Commit)
- CC-Klasse: .gitignore-Pruefung nach Verzeichnis-Verschiebung (patterns/ + decisions/ korrekt tracked?)
- CD-Klasse: keine Executable-Files via MCP-direct bewegen (nur via Code-Chat-Build-Pattern)
- Stolperstein A: kein Datum in Filenames generieren ohne date -u Wallclock-Check

---

## References

- code.claude.com/docs/en/claude-directory (CLAUDE.md + .claude/ Loading-Mechanik, Primary Authority)
- code.claude.com/docs/en/best-practices (CLAUDE.md Best-Practices)
- manpages.ubuntu.com/git-subtree (git subtree split Mechanik)
- atlassian.com/git/tutorials/git-subtree (git subtree vs submodule)
- hivetrail.com/blog/agents-md-vs-claude-md (AGENTS.md vs CLAUDE.md Cross-Tool)
- medium.com/@n913239 Complete Guide to CLAUDE.md (Loading-Details, @import)
- feature-sliced.design/blog/frontend-monorepo-explained (Monorepo-Boundaries)
- docs/UNIVERSAL_FUNDAMENT_FAHRPLAN.md F3.1 + F3.2
- decisions/0010 Best-Practice-Recherche-Pflicht
