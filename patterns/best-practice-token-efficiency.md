# best-practice-token-efficiency.md

**Authority:** F4.0 Token-Effizienz-Audit (FAHRPLAN), ADR 0010 Best-Practice-Recherche-Pflicht
**Erstellt:** 2026-05-13 Tag 17 Vormittag, F4.0-Sub-1 (Schicht-2-plus-3-Snapshot)
**Vorgaenger-Recherche:** Tag 16 Nachmittag Schicht-1 (8 Quellen, dokumentiert in HANDOVER_20260512T132000Z_CTO.md Sektion 6)

---

## 1. Geltungsbereich plus Authority

Dieser Snapshot bindet alle F-Schritte F4.0-Sub-2 bis Sub-6 plus alle kuenftigen CTO-Code-Prompt-Entscheidungen mit Token-Implikation. Er ergaenzt patterns/best-practice-multi-agent.md (Subagent-Architektur) plus patterns/best-practice-subagent-frontmatter.md (Subagent-Defines) plus AGENTS.md/CLAUDE.md (Universal- plus Projekt-Layer). Direkter Bezug: CEO-Anforderung Tag 16 Nachmittag "alle prompts sind sehr lang, qualität darf in keiner weise leiden, in zukunft wenn ihr voll autonom arbeitet, müssen wir auch effizient sein". Quality-first-Constraint ist nicht-verhandelbar.

---

## 2. Schicht-2 Primaer-Quellen (Anthropic-Docs plus tiefe Sekundaer-Analysen)

### 2.1 Prompt-Caching (Anthropic-Docs plus Erlaeuterungen)

Quellen: platform.claude.com/docs/en/build-with-claude/prompt-caching (Anthropic primary), platform.claude.com/docs/en/about-claude/pricing (Anthropic primary), mager.co Tiefen-Analyse 29. April 2026, claudebuddy.art Practical Guide Mai 2026, dev.to/whoffagents 5-min-TTL-Change April 2026.

Kern-Mechanik:
- **Cache-Reads kosten 10% des Base-Input-Token-Preises** (90% Reduktion auf cached Prefix).
- **Cache-Writes kosten 125% (5-min-TTL) ODER 200% (1h-TTL) des Base-Input-Preises** als Aufschlag fuer den ersten Schreib-Pass.
- **Mindest-Cache-Groesse: 1024 Tokens fuer Sonnet/Opus, 2048 fuer Haiku, 4096 fuer Haiku 4.5.** Inhalte unter dieser Schwelle werden NICHT cached, auch nicht mit cache_control-Markierung.
- **TTL-Default seit Anfang 2026: 5 Minuten** (war 60 Minuten). 1h-TTL verfuegbar via cache_control type=ephemeral_1h gegen Aufpreis.
- **TTL refresht pro Cache-Hit** (Sliding-Window), nicht statisch ab Erst-Schreib.
- **Workspace-Isolation seit 5. Feb 2026** (war Organization-Level vorher). Caches isoliert pro Workspace innerhalb derselben Organisation.

Cache-Reihenfolge fuer Hash-Match (kritisch fuer Cache-Hit-Rate):
1. Tools (komplette Tool-Liste)
2. System-Message
3. Message-History (chronologisch)

Cache-Match ist **exact-string-match**, NICHT semantisch. Jede Aenderung am Anfang invalidiert alle nachfolgenden Blocks. Cache wird auf identischen Prefix-Bytes (inkl. Whitespace) gehasht.

Automatic-Caching vs Explicit:
- **Automatic (claude.ai App + Browser plus Claude Code CLI):** Anthropic markiert automatisch das letzte cacheable-Block als Breakpoint. User hat keinen direkten Cache-Control. Recommended fuer Multi-Turn-Chats. Cache-Verhalten ist Anthropic-Backend-kontrolliert.
- **Explicit (Anthropic API direkt mit cache_control-Field):** User platziert cache_control auf spezifische Blocks. Mehr Control, mehr Manual-Tracking-Aufwand. Bis zu 4 Cache-Breakpoints pro Request.

Konsequenz fuer CoveLab (claude.ai-App plus Claude Code CLI Stack): **Automatic-Caching ist aktive Default-Realitaet, kein Explicit-Setup noetig. Optimierung erfolgt INDIREKT durch Cache-Stable-Prefix-Disziplin (siehe Sektion 7).**

### 2.2 Cache-Killer (Anti-Patterns die Cache-Hit-Rate zerstoeren)

Quelle: mager.co plus claudebuddy.art plus dev.to/whoffagents.

- **Dynamische Timestamps im System-Prompt-Prefix** (Current time: ISO-String): jeder Request hat neuen Prefix-Hash, niemals Cache-Hit.
- **Variable User-IDs, Request-UUIDs, Trace-IDs** im Prefix.
- **Tool-Listen-Aenderung pro Request** (z.B. dynamisch geladene Tools): zerstoert Tool-Cache, kaskadiert auf System plus Message-History.
- **Model-Switch midstream** (Sonnet zu Opus): Cache ist per Model. Switch = neuer Cache-Lane, kompletter Re-Write.
- **Extended-Thinking-Setting-Aenderung** (Budget, on/off): aendert Message-Block-Struktur, invalidiert Cache.
- **Whitespace-Drift** (verschiedene Zeilenenden, Trailing-Spaces, Reformatierung): exact-byte-match toleriert das nicht.
- **Hinzufuegen oder Loeschen frueher Conversation-Turns**: alle nachfolgenden Blocks invalidiert.

### 2.3 Subagents (Anthropic-Docs plus Praxis-Berichte)

Quellen: code.claude.com/docs/en/sub-agents (Anthropic primary), nimbalyst.com Subagents-Guide 2026, claude-world.com Tutorial, kdnuggets.com 7 Practical Ways Mai 2026, anthropic GitHub Issue 10212.

Kern-Mechanik:
- **Subagent = isoliertes 200K-Token-Kontext-Fenster** mit eigenem System-Prompt, eigenem Tool-Subset, eigener Model-Wahl. Spawnt von Parent-Agent via Task-Tool (auch Agent-Tool gennant).
- **Subagent-Output ist nur Final-Result** (Concise Summary), NICHT die internen Tool-Calls, File-Reads, Reasoning-Blocks. Diese bleiben im Subagent-Context isoliert plus werden verworfen.
- **Subagent-Defines: ~/.claude/agents/<name>.md (User-Scope) oder .claude/agents/<name>.md (Project-Scope).** Frontmatter: name, description, tools. Body = System-Prompt.
- **Token-Multiplier: ca 7x** bei Subagent-heavy Sessions (jeder Subagent hat eigenen Context plus eigene Tools plus eigene Defines).
- **Forked-Subagents (Claude Code v2.1.117+, experimental):** erben Full-Conversation-History von Parent, fuer Side-Tasks ohne Re-Explain. Activate via CLAUDE_CODE_FORK_SUBAGENT=1. Tool-Calls bleiben isoliert, nur Final-Result kommt zurueck.

Wann Subagents lohnen sich (Cost-Benefit-Positiv):
- **Read-heavy Tasks**: file searches, codebase exploration, log dumps, multi-file analysis. Verbose Tool-Output bleibt isoliert.
- **Parallel Exploration**: 10 verschiedene Queries auf Codebase parallel via parallele Subagents.
- **Heavy-Context Tasks**: ein Task der allein 50K-plus Tokens an Context braeuchte. Subagent macht es, returnt 500-Token-Summary.

Wann Subagents NICHT lohnen (Cost-Benefit-Negativ):
- **Kurze Single-Tool-Tasks** (single git command, single file read): Overhead aus Subagent-Defines plus Tool-Definitions plus Round-Trips uebersteigt Ersparnis.
- **Tightly-Coupled Tasks**: Output von Task-A wird zur Frage fuer Task-B - Subagent-Isolation verhindert Sequential-Reasoning.

Cache-Interaktion: Subagents profitieren von eigenem Cache-Lane wenn ihre System-Prompt-Frontmatter stabil bleibt. **90%+ der Tokens in heavy Subagent-Sessions sind Cache-Reads** (ksred.com Empirie), was den 7x-Multiplier auf effektive 1.5-2x-Kosten reduziert.

### 2.4 Anthropic-Postmortem 23. April 2026 (Negativ-Praezedenz)

Quelle: anthropic.com/engineering/april-23-postmortem.

Drei Bugs zwischen 4. Maerz plus 20. April 2026 fuehrten zu Claude-Code-Quality-Reports plus "usage limits draining faster". Relevanter Bug fuer F4.0:
- **26. Maerz 2026:** Anthropic shipped Thinking-Block-Cleanup fuer idle-uebergreifende-1h-Sessions als Effizienz-Improvement. Bug: Cleanup lief jeden Turn statt einmalig. Konsequenz: kontinuierliche Cache-Misses, da Prefix sich jeden Turn aenderte (Thinking-Blocks wurden discarded). Fix 10. April 2026 in v2.1.116.
- **16. April 2026:** System-Prompt-Instruction zur Verbositaets-Reduktion eingebaut, hatte negativen Quality-Effekt auf Coding. Revert 20. April 2026.

Konsequenz fuer F4.0: **Aggressive Verbositaets-Cuts in System-Prompt-Layer haben empirisch Quality-Drop verursacht. Anthropic selbst hat es probiert plus zurueckgenommen. Caveman-Style ist deshalb risk-positiv, mid-compression ist sicherer.**

---

## 3. Schicht-3 Gegenstimmen plus Quality-Tradeoff

### 3.1 arXiv 2604.00025 (Maerz 2026): Brevity-Constraints verbessern Quality

Quelle (sekundaer-referenziert in marketingagent.blog plus mejba.me): 31 Modelle, 1485 Probleme, brevity-constraints verbessern large-model accuracy um 26 Prozentpunkte auf Problemen wo Verbositaet zu Fehlern fuehrt. Mechanismus: reduzierte Overelaboration, weniger Hedging plus tangential reasoning.

Konsequenz fuer F4.0: **Brevity ist Quality-positiv bei richtigem Mass.** CEO-Constraint Quality-first ist kompatibel mit aggressiver Output-Diaet wenn die Diaet auf Filler plus Hedging plus Repetition zielt, NICHT auf substantive Reasoning oder Fact-Density.

### 3.2 IntuitionLabs Token-Optimization-Tradeoff

Quelle: intuitionlabs.ai Token-Optimization Mai 2026.

Direktes Zitat-Kern: brevity-vs-clarity-Tradeoff ist real. Excessive truncation harms factual completeness. SCOPE-Approach (Zhang et al. 2025) macht Generative-Summarization plus Re-Write von Prompt-Chunks - aber bei Verlust von "consequential details" sinkt Model-Performance. Empfehlung: Compression always followed by checks.

Konsequenz fuer F4.0: **Komprimierte STARTPROMPT-Versionen muessen empirisch validated werden (vorher-nachher-Test mit identischer Aufgabe, Quality-Score-Vergleich). Sub-6 Closure-Messung ist Pflicht, nicht Optional.**

### 3.3 Token-Economics-2026 Position

Aus Schicht-1 (Tag-16-Vorlauf): "Compress prompts and you will confuse the model, get worse answers, and spend more tokens on retries". Position ist gueltig fuer aggressive Caveman-Style-Cuts plus semantische Compression. Position ist NICHT gueltig fuer Filler-Removal plus Cache-Stable-Prefix-Optimierung plus Subagent-Delegation. F4.0 zielt explizit auf Letzteres, nicht Erstes.

### 3.4 Caveman-Skill Empirie

Quelle: mejba.me April 2026 (Selbst-Test).

Caveman-Skill (GitHub JuliusBrussee/caveman, 5400 Stars): forciert Telegramm-Stil fuer Claude-Code-Output. Empirisch: 73% Token-Reduktion fuer gleichen Inhalt im Output-Anteil. ABER: Output ist nur ein Teil der Session-Tokens. Combined-Savings nur 4-5% Total-Token-Reduktion pro Session (Input plus Conversation-History plus Tools dominieren).

Lite-Variante (40-50% Kompression) via CLAUDE.md-Zeile: "Be concise. No filler. No hedging. State conclusions first, reasoning second" - fast identische Quality-Vorteile wie Caveman plus weniger Quality-Risiko.

Konsequenz fuer F4.0-Sub-5: **Concise-Output-Skill fuer CoveLab nicht Caveman-aggressive, sondern Lite-Style. CEO-Format-Regel R11 (max 5 Zeilen, Fakten-Optionen-Empfehlung-Naechster-Schritt) ist bereits Lite-Style empirisch validiert.**

---

## 4. Schicht-1.5 Coverage-Check (Stolperstein-plus-Pattern-Beruehrungen)

Pruefung: Welche bestehenden Stolpersteine plus Patterns sind Token-Effizienz-relevant?

- **BR-Schicht-1.5-Lehre (Recherche-Pflicht).** ADR 0010. Recherche-Snapshots reduzieren Re-Recherche-Tokens fuer kuenftige F-Schritte. Direkt-Bezug: dieser Snapshot selbst.
- **Stolperstein BT (Session-Restart fuer Plugin-Skills):** Skill-Surfacing nach Install braucht Restart. Konsequenz: Skills laufen im Session-Start-Context, sind Cache-Stable wenn nicht haendisch geaendert. Concise-Output-Skill aus F4.0-Sub-5 ist Cache-stable nach Install.
- **Stolperstein BW (Skill-Surfacing-Authority marketplace.json):** Surfacing kostet Tokens pro Session-Start. Mehr Skills surfaced gleich mehr Bytes im Session-Reminder gleich mehr Tokens.
- **Stolperstein BY plus BZ (Plugin-Installation client-side, aber agents plus hooks Repo-Files):** F1.5 Subagent-Defines plus F1.6 Hooks landen in /opt/covelab/.claude/, aktiv erst post-F2.4. Vor F2.4 wirken sie nicht. F4.0-Sub-3 STARTPROMPT-Diaet wirkt sofort plus client-side-unabhaengig.
- **Pattern best-practice-multi-agent.md plus best-practice-subagent-frontmatter.md** (vor Tag 17 existent): Subagent-Architektur bereits dokumentiert. F4.0 baut darauf auf, dupliziert nicht.

Keine direkten Stolperstein-Konflikte mit F4.0-Plan.

---

## 5. CoveLab-Cache-Killer-Audit (aktuelle Anti-Patterns)

Pruefung des aktuellen CoveLab-Stack gegen Cache-Killer-Liste aus Sektion 2.2:

- **STARTPROMPT_CTO.md Wallclock-Triple-Check:** dynamische Timestamp-Generierung pro Session-Start. **CACHE-KILLER aktiv.** Auswirkung: erste Session-Tokens sind nie Cache-Hit, voller Re-Write-Aufschlag. F4.0-Sub-3 muss Timestamp aus Cache-Prefix herausnehmen (z.B. in Message-Tail statt System-Prompt, oder nur on-demand erfragt statt eingebettet).
- **HANDOVER-Files Wallclock-Filename:** Filename selbst ist nicht im Cache-Prefix (kein Tool-System-Message-Anteil). Aber HANDOVER-Inhalt wird bei R22-Lesung in Context geladen mit Timestamps drin. Cache-Hit unwahrscheinlich pro Session weil neuer HANDOVER neue Bytes.
- **MCP-Tool-Liste (CoveLab MCP plus Gmail plus Google Drive):** stabil pro Session, keine dynamische Tool-Liste. **Cache-stable.**
- **userMemories (von Anthropic-Backend injected):** aenderlich pro Memory-Update (Tag-Wechsel etc.). Cache-Update bei Memory-Sync.
- **R22-Pflicht-Lesung 9 Files (ca 30-50K Tokens):** wird in Message-History gestapelt. Cache-Hit moeglich wenn identischer File-Inhalt plus identische Lese-Reihenfolge plus keine intermediate-Aenderung. Praktisch ist erste Session-Stunde gut, danach Cache-Expiry (5-min-TTL).
- **session_log.md plus STOLPERSTEINE.md plus COORDINATION.md:** append-only, jede Session laedt neuen Tail. Cache-Killer wenn tail-N waechst zwischen Sessions.
- **Code-Prompts mit embedded Build-Scripts (3-8K Tokens pro Prompt):** pro Code-Chat-Turn neuer Inhalt, kein Cache-Reuse-Potential. F4.0-Sub-4 zielt auf Schema-Komprimierung, nicht Build-Script-Komprimierung.

**Befund:** drei aktive Cache-Killer-Quellen identifiziert (STARTPROMPT-Timestamp, append-Files-Tail-Wachstum, Code-Prompt-Build-Scripts). F4.0-Sub-3 plus Sub-4 adressieren Erst- plus Drittens. Zweitens ist strukturell schwer aufzuloesen (append-only ist R7-Prio-1-Konsistenz-Anforderung), aber: Tail-N kann reduziert werden (60 zu 30, 100 zu 50) ohne Substanz-Verlust solange tabellarisch oder gefiltert. Pflicht-Lesung-Optimierung gehoert in F4.0-Sub-3-Scope.

---

## 6. Mess-Methode (Baseline plus Validation, Tools)

### 6.1 Tools (extern, fuer Claude Code CLI plus indirekt CTO-Chat)

- **ccusage (npm ryoppippi/ccusage, 4.8K-plus GitHub-Stars):** CLI parsed lokal JSONL-Session-Files. Zeigt Daily/Monthly/Session-Token plus Cache-Read plus Cache-Write separat. Goldstandard fuer Claude Code CLI-Messung.
- **Claude-Code-Usage-Monitor (Maciek-roboblog):** real-time Terminal-Dashboard, ML-burn-rate-Predictions, plant-Detection. Ergaenzung zu ccusage fuer Live-Monitoring.
- **ClaudeFast Code Kit:** Status-Line-Monitor plus ContextRecoveryHook (recovered ca 15K Tokens pro Session am Source). Newer, commercial.

**Konsequenz fuer CoveLab:** ccusage als Baseline-Tool. Installation via npx ccusage (client-side auf Code-Chat-Windows-Maschine, parsed C:\Users\ronal\.claude\projects\\*\\*.jsonl).

### 6.2 Mess-Methodik fuer F4.0-Sub-2 Baseline

**Pre-Optimization-Baseline (Sub-2):**
1. ccusage daily fuer 3-5 letzte CoveLab-Code-Chat-Sessions plus durchschnittliche Tokens/Session berechnen.
2. CTO-Chat-Baseline indirekt: Anzahl R22-Files plus deren Byte-Groesse plus geschaetzte Tokens-Konversion (1 Token gleich ca 3.5-4 Zeichen Deutsch, ca 4 Zeichen Englisch). Direkter Token-Read aus claude.ai nicht moeglich, Approximation reicht fuer Baseline.
3. Cache-Hit-Rate-Approximation via ccusage Cache-Read-vs-Cache-Write-Verhaeltnis (hohes Read-Verhaeltnis = guter Cache).
4. STARTPROMPT_CTO.md plus STARTPROMPT_CODE.md aktuelle Groesse messen (wc -c).
5. Letzte 5 HANDOVER-Files Groesse messen.

**Post-Optimization-Validation (Sub-6):**
1. ccusage daily fuer 3-5 Sessions nach Sub-3 plus Sub-4 plus Sub-5 implementiert.
2. Quality-Score-Vergleich: gleiche F-Schritt-Klasse (z.B. Recon-Sub-Schritt mit Read-Heavy-Mandate) vorher-nachher, manuelle CEO-Review der Output-Qualitaet.
3. Total-Token-Delta plus Cache-Read-Anteil-Delta plus STARTPROMPT-Byte-Delta dokumentieren.
4. Target: 30-50% Total-Token-Reduktion bei gleicher oder besserer Quality. Caveat: Anthropic-Postmortem (Sektion 2.4) zeigt Quality-Drop-Risk - Stop-Loss bei jeglichem messbaren Quality-Drop.

---

## 7. Direktiven fuer F4.0-Sub-2 bis Sub-6

### Sub-2 Mess-Baseline (Pflicht-Output)
- ccusage installieren auf Code-Chat-Windows-Maschine plus npx-Test
- Baseline-Report patterns/skill-activation-baseline.md-aehnlich plus reports/f40_sub2_baseline.md
- STARTPROMPT-CTO plus STARTPROMPT-CODE plus durchschnittliche HANDOVER-Bytes plus Token-Approximation tabellarisch

### Sub-3 STARTPROMPT-Diaet (Hauptdiaet)
Sub-3 Token-Disziplin-Konvention (REVIDIERT 2026-05-12 post-Sub-2-Empirie)

Original-Direktive STARTPROMPT-Diaet SUPERSEDED: Sub-2-Mess-Empirie (ccusage 7-Tage, Commit e6475e9) zeigte STARTPROMPT-Files sind klein (4.7 KB CTO plus 10 KB CODE), NICHT Hauptdiaet-Target. Snapshot-Sektion-5-Annahme war Memory-Drift aus Pre-Mess-Phase.
Echte Hebel (Sub-2-Daten): session_log 146 KB plus STOLPERSTEINE 72 KB Tail-Wachstum (zusammen ca 58.900 Token-Approx pro Session-Start bei Full-Read) plus Heavy-Code-Chat-Sessions (Top-Session 282M Token war ca 55% der 7-Tage-Total).
Konvention in AGENTS.md "## Token-Disziplin"-Sektion (Universal-Layer): Cache-Hit-Disziplin plus Tail-Limits (session_log tail-80, STOLPERSTEINE tail-120, COORDINATION head-40) plus Heavy-Session-Disziplin plus Code-Prompt-Hygiene plus Subagent-Use-Cases plus Mess-Pflicht.
STARTPROMPT_CTO.md ergaenzt: Tail-Limits explizit in R22-Pflicht-Lesung-Liste, Wallclock-NIE-aus-CEO-Greeting-Lehre dokumentiert.
Subagent-Defines fuer R22-Reader plus Recon-Runner: F1.5-Roadmap-Erweiterung, NICHT in Sub-3-Scope (Phase-Gate-Disziplin).
Audit-Doku: reports/f40_sub3_konvention.md (6 Sektionen Audit-Recap plus Re-Scope-Begruendung plus Konvention plus Mess-Plan).
Effort actual ca 1.5h (Konvention plus 3 MCP-Writes plus 1 Code-Chat-Edit-Run plus Atomic-Commit). Schaetzung war 1-2h aus FAHRPLAN, getroffen.

### Sub-4 Code-Prompt-Schema-Komprimierung
- Code-Prompt-Schema R13 (TASK/MODUS/CONTEXT/AUSFUEHRUNG/PFLICHT-RUECKMELDUNG) bewahren als Skelett
- Boilerplate-Sektionen kuerzen (z.B. "wenn Fehler, Rueckmeldung" als verkuerzte Pflicht-Klausel)
- Build-Script-Templates standardisieren fuer Recycling (Common-Pattern-Bibliothek in docs/code-prompt-templates.md plus Verweis statt Inline-Wiederholung)
- Target: 40-60% Reduktion pro Build-Script-Boilerplate

### Sub-5 Concise-Output-Skill
- Lite-Style nicht Caveman: "Be concise, no filler, no hedging, state conclusion first, reasoning second"
- Als ~/.claude/skills/concise-output/SKILL.md plus REPO-Persistierung in /opt/covelab/.claude/skills/concise-output/SKILL.md (Universal-Fundament-Layer)
- Always-Active-Flag (statt on-demand)
- CEO-Format-R11 ist Empirie-Basis (5-Zeilen-Discipline schon empirisch geuebt)

### Sub-6 Closure-Messung plus FAHRPLAN-Pass
- Post-Implementation ccusage-Lauf
- Quality-Test mit Recon-F-Schritt-Klasse (z.B. ein Sub-Recon mit gleichem Pattern wie F2.1-Sub-2 Re-Run mit neuem STARTPROMPT)
- Bericht reports/f40_sub6_closure.md
- FAHRPLAN F4.0 fail-zu-pass nur wenn Token-Delta-Reduktion plus Quality-aequivalent oder besser

---

## 8. Naechste Schritte (post-F4.0-Sub-1)

1. CEO-Freigabe Sub-1 Snapshot (dieser File)
2. F4.0-Sub-1 fail-zu-pass via Code-Chat-Build (FAHRPLAN-Edit plus session_log-Append plus RESEARCH_INDEX-Update plus Atomic-Commit)
3. F4.0-Sub-2 Baseline-Run via Code-Chat (ccusage-Install plus Baseline-Report)
4. F4.0-Sub-3 STARTPROMPT-Diaet draft (CTO-MCP write_file fuer NEUE Datei-Versionen, dann Code-Chat-Build fuer Replace plus Test)

---

**Snapshot-Ende.** Authority bindet F4.0-Sub-2 bis Sub-6 plus alle kuenftigen CTO-Code-Prompt-Entscheidungen mit Token-Implikation.
