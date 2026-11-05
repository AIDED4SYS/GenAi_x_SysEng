# Recherche: `AGENTS.md` und `.agents/` als gemeinsame Quelle für vier KI-Harnesses (Issue #8)

> **Status: ungeprüft.** KI-Recherche von Claude Code (Modell Claude Opus 5.5) vom 2026-11-05,
> gedacht zur Gegenprüfung durch einen anderen Chat.
> Nach [`../context.md`](../context.md) sind Rechercheergebnisse keine Belege. Jede Aussage nennt
> deshalb ihre Originalquelle oder ist als eigener Test bzw. eigene Folgerung gekennzeichnet.

## Kernaussagen

1. Die Idee trägt: Antigravity und Codex lesen `.agents/` direkt, Claude Code über Symlinks in
   `.claude/`, Copilot in VS Code über seine Standardordner (A1, A6, X2, C2, V5).
2. `"chat.instructionsFilesLocations": { ".agents/rules": true }` wirkt für eine Datei namens
   `python.md` nicht – VS Code nimmt dort nur `*.instructions.md` (V3).
3. Codex lädt nie Regeln aus `.agents/rules/`. Codex kennt für Anweisungen nur `AGENTS.md`; seine
   „Rules“ in `.codex/rules/` sind Befehlsfreigaben (X1, X3).
4. `.claude` als Ordner-Symlink auf `.agents` blockiert Claude-Code-Worktrees (C6), löscht beim
   `git pull` die Datei `.claude/settings.local.json` (T1b) und legt sie danach nicht ignoriert
   unter `.agents/` ab (T1c).
5. Empfehlung: `.agents/rules/python.instructions.md`, `.claude/rules` und `.claude/skills` als
   einzelne Symlinks, Verweis für Codex in `AGENTS.md` (Abschnitt 6, getestet in T3).

## 1. Frage

Issue [#8](https://github.com/AIDED4SYS/GenAi_x_SysEng/issues/8) will nur noch `AGENTS.md`,
`.agents/rules/` und `.agents/skills/` als echte Dateien pflegen. Geplant laut Issue:

- `.agents/rules/python.md` als echte Datei, Frontmatter ergänzt um `trigger: glob` und
  `globs: "**/*.py"`
- `.agents/skills/.gitkeep`
- `.claude` als Ordner-Symlink auf `.agents`
- `.github/instructions/` löschen
- `.vscode/settings.json`: `"chat.useAgentsMdFile": true`, `"chat.useAgentSkills": true`,
  `"chat.instructionsFilesLocations": { ".agents/rules": true }`
- Ziel: Codex, Antigravity, Claude Code und GitHub Copilot laden `AGENTS.md`,
  `.agents/rules/python.md` und die Skills „automatisch beim Kaltstart“.

Geprüft: Funktioniert das so, und was muss anders sein?

## 2. Vorgehen

Kürzel für die Prüfart:

- **Doku** – Herstellerdokumentation, abgerufen am 2026-11-05
- **Code** – Quelltext gelesen: VS Code (`microsoft/vscode`, Branch `main`, Commit
  `66501d439e871b12b5afdebcf6454cd416322905` vom 2026-11-05). Geladen mit
  `gh api -H "Accept: application/vnd.github.raw" repos/microsoft/vscode/contents/<pfad>`.
  Zeilenangaben beziehen sich auf diesen Stand.
- **Test** – selbst ausgeführt, Skripte und Ausgaben in Abschnitt 5
- **Beob.** – in der Claude-Code-Sitzung beobachtet, in der diese Recherche entstand
- **Schluss** – eigene Folgerung, nicht direkt belegt

Zur Qualität der Zitate:

- Die Seiten von Claude Code (memory, claude-directory, settings, worktrees) lagen im Wortlaut
  vor. Zitate daraus sind wörtlich.
- Alle anderen Seiten (VS Code, GitHub, Codex, Antigravity, Claude-Code-Seite „skills“) wurden
  mit einem Abruf-Werkzeug gelesen, das den Seiteninhalt durch ein kleines Modell zusammenfasst.
  Zitate daraus sind vermutlich wörtlich, **aber am Original prüfen**.
- Die Harnesses selbst wurden nicht live getestet: kein Lauf in Copilot, Codex oder Antigravity.

Testumgebung: Windows 11 Home 10.0.26300 · Git 2.53.0.windows.2 (systemweit `core.autocrlf=true`)
· Git Bash · Windows PowerShell 5.1.26100.9818 · Entwicklermodus an
(`AllowDevelopmentWithoutDevLicense = 1`) · ohne Administratorrechte · im Projekt
`core.symlinks=true`.

## 3. Ausgangslage im Repo (main, Commit `c68b025`)

| Pfad | Art |
|---|---|
| `AGENTS.md` | echte Datei |
| `.github/copilot-instructions.md` | Symlink → `../AGENTS.md` |
| `.github/instructions/python.instructions.md` | echte Datei, Frontmatter `applyTo: "**/*.py"` und `paths: ["**/*.py"]` (mit YAML-Kommentarzeilen) |
| `.claude/rules/python.md` | Symlink → `../../.github/instructions/python.instructions.md` |
| `.claude/skills/.gitkeep` | echte Datei |
| `.gitignore` | enthält `.claude/settings.local.json` |
| `EINRICHTUNG.md` | im Repo; im Klon der Recherche lokal gelöscht, `skip-worktree` gesetzt |

## 4. Befunde je Harness

### 4.1 Claude Code

- **C1** – Liest `AGENTS.md` direkt, solange im Arbeitsordner und darüber keine `CLAUDE.md`,
  `.claude/CLAUDE.md` oder `CLAUDE.local.md` liegt; ab v2.1.277.
  *Doku, Beob.* – [memory › AGENTS.md](https://code.claude.com/docs/en/memory#agents-md):
  „By default, Claude reads `AGENTS.md` only when you have no `CLAUDE.md` in your working
  directory or above it.“ und „Reading `AGENTS.md` directly requires Claude Code v2.1.277 or
  later.“ Beob.: In der Recherche-Sitzung war `AGENTS.md` als Projektanweisung geladen.
- **C2** – Liest nichts unter `.agents/`; braucht also `.claude/rules/` und `.claude/skills/`.
  *Doku* – memory: „**Not read**: `AGENTS.local.md`, `AGENTS.override.md`, or anything under a
  `.agents/` directory“.
- **C3** – `.claude/rules/`: alle `.md`-Dateien, auch in Unterordnern. Ohne `paths` beim Start
  geladen, mit `paths` erst, wenn Claude eine passende Datei liest, schreibt oder bearbeitet.
  Einziges Frontmatter-Feld für Regeln: `paths`.
  *Doku, Beob.* – memory: „All `.md` files are discovered recursively“, „Rules without `paths`
  frontmatter are loaded at launch“, „A path-scoped rule loads when Claude uses the Read, Write,
  or Edit tool on a matching file.“ [claude-directory](https://code.claude.com/docs/en/claude-directory):
  `rules/*.md` → Frontmatter `paths`. Beob.: `.claude/rules/python.md` war beim Start nicht im
  Kontext.
- **C4** – Symlinks in `.claude/rules/` werden unterstützt. Zeigt ein Link aus dem Arbeitsordner
  hinaus, behandelt Claude Code ihn wie einen externen Import (Freigabe nötig).
  *Doku* – memory: „The `.claude/rules/` directory supports symlinks“, „Claude Code treats a
  symlink whose target is outside your working directory like an external import.“
- **C5** – Skills aus `.claude/skills/<name>/SKILL.md`, vom Startordner bis zum Repo-Root;
  `.agents/skills` wird nicht genannt. Ein symlinkter Skill-Ordner wird einmal geladen. Beim Start
  nur die Beschreibungen, der volle Inhalt beim Aufruf.
  *Doku (über Abruf-Werkzeug)* – [skills](https://code.claude.com/docs/en/skills).
- **C6** – **Claude Code verweigert Worktrees, wenn `.claude`, `.claude/worktrees` oder das
  Worktree-Verzeichnis ein Symlink ist.** Betrifft `claude --worktree`, das Werkzeug
  `EnterWorktree` und Subagenten mit `isolation: worktree`.
  *Doku* – [worktrees › Worktree creation fails on a symlinked path](https://code.claude.com/docs/en/worktrees):
  „Claude Code refuses to create a worktree when `.claude`, `.claude/worktrees`, or the worktree
  directory itself is a symlink, and the error names the symlinked path.“ Nicht getestet.
- **C7** – `.claude/settings.local.json` schreibt Claude Code selbst, z. B. bei „Yes, and don't
  ask again“ und bei einigen `/config`-Optionen. Beim ersten Schreiben trägt es
  `**/.claude/settings.local.json` in die globale Git-Excludes-Datei ein.
  *Doku, Beob.* – [settings](https://code.claude.com/docs/en/settings): „The first time Claude
  Code writes the file in a git repository that doesn't already ignore it, it adds
  `**/.claude/settings.local.json` to your global git excludes file“. Beob.: Auf diesem Rechner
  enthält `~/.config/git/ignore` die Zeilen `**/.claude\settings.local.json` und
  `**/.claude/settings.local.json`.
- **C8** – Was Claude Code im Projektordner `.claude/` nutzt: Tabelle unten.
  *Doku* – [claude-directory](https://code.claude.com/docs/en/claude-directory),
  [worktrees](https://code.claude.com/docs/en/worktrees), [settings](https://code.claude.com/docs/en/settings).
- **C9** – Das Write-Werkzeug von Claude Code schrieb ohne Fehler durch einen Ordner-Symlink
  (`.claude` → `.agents`); die Datei lag danach im Ziel. Nur außerhalb des Projektordners getestet.
  *Test T5.*

Tabelle zu C8 – Inhalt von `.claude/` im Projekt:

| Pfad | Zweck | ins Repo? | legt Claude Code selbst an? |
|---|---|---|---|
| `settings.json` | Team-Einstellungen: `permissions`, `hooks`, `env`, Modell | ja | nein |
| `settings.local.json` | persönliche Einstellungen, gespeicherte Freigaben | nein | **ja** (C7) |
| `CLAUDE.md` | Projektanweisungen; **verdrängt `AGENTS.md`** (C1) | ja | nein |
| `AGENTS.md` | wird auch hier gelesen | ja | nein |
| `rules/*.md` | Regeln, optional mit `paths` | ja | nein |
| `skills/<name>/SKILL.md` | Skills | ja | nein |
| `commands/*.md` | ältere Form von Skills | ja | nein |
| `agents/*.md` | Subagenten | ja | nein |
| `output-styles/*.md` | Antwortstile | ja | nein |
| `workflows/*.js` | Workflow-Skripte | ja | **ja** (beim Speichern aus `/workflows`) |
| `agent-memory/<name>/` | Gedächtnis von Subagenten | ja | **ja** (wenn ein Subagent Gedächtnis nutzt) |
| `worktrees/<name>/` | Worktrees für parallele Sitzungen und Subagenten | nein (Doku empfiehlt `.gitignore`) | **ja** |
| `hooks/…` | übliche Ablage für Hook-Skripte (Beispiel in der Doku, kein fester Ort) | ja | nein |

Nicht in `.claude/`: `.mcp.json`, `.worktreeinclude` und `CLAUDE.local.md` liegen im
Projekt-Root; Transkripte, Pläne, Auto-Memory und Verlauf in `~/.claude/`.

### 4.2 GitHub Copilot in VS Code

- **V1** – VS Code kennt die Harnesses **Copilot** (Copilot SDK im Agent Host; „Start with the
  Copilot harness for day-to-day coding“), **Local** (eingebaut, im Extension Host), **Claude**
  (Claude Agent SDK) und **Codex** (über die Codex-Erweiterung oder experimentell im Agent Host).
  Ein Antigravity-Harness kommt nicht vor.
  *Doku* – [agent-harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses).
- **V2** – Laut Referenz: Der Copilot-Harness liest `.github/copilot-instructions.md` oder
  `AGENTS.md`, Regeln mit Dateimuster nur aus `.github/instructions/**/*.instructions.md` (oder
  verschachtelte `AGENTS.md`). Local liest zusätzlich `CLAUDE.md` und `.md`-Dateien in
  `.claude/rules`. „Targeted instruction files for Copilot and Local must end in
  `.instructions.md`.“ Claude-Harness: `CLAUDE.md` oder `.claude/CLAUDE.md`, dazu `.claude/rules`.
  Codex-Harness: `AGENTS.md`.
  *Doku* – [custom-instructions-reference](https://code.visualstudio.com/docs/agents/reference/custom-instructions-reference).
- **V3** – **Eine Datei `.agents/rules/python.md` erkennt VS Code nicht als Regel, auch nicht mit
  `"chat.instructionsFilesLocations": { ".agents/rules": true }`.** `getPromptFileType()` erkennt als
  Anweisungsdatei nur Dateien auf `.instructions.md`, die Datei `copilot-instructions.md` und
  `.md`-Dateien, deren Pfad `/.claude/rules/` enthält. `listFilesWithSource()` verwirft alle
  anderen Dateien.
  *Code* – `promptFileLocations.ts`, Funktionen `getPromptFileType` und `isInClaudeRulesFolder`;
  `promptFilesLocator.ts` Z. 205: `if (getPromptFileType(file) !== type || paths.has(file)) { continue; }`.
- **V4** – Die Standardordner bleiben aktiv, solange sie nicht ausdrücklich auf `false` stehen;
  eigene Einträge kommen hinzu.
  *Code* – `config.ts`, `promptSourceFolders()` ab Z. 176:
  `if (value[defaultFolder.path] !== false) { paths.push(defaultFolder); }`.
- **V5** – Standardwerte:
  - `chat.instructionsFilesLocations`: `{ ".github/instructions": true, ".claude/rules": true, "~/.copilot/instructions": true, "~/.claude/rules": true }`
  - `chat.useAgentsMdFile`: `true` – „… in Local harness sessions only“
  - `chat.useAgentSkills`: `true`
  - `chat.agentSkillsLocations` (als veraltet markiert): `{ ".agents/skills", ".github/skills", ".claude/skills", "~/.agents/skills", "~/.copilot/skills", "~/.claude/skills" }`, alle `true`
  - `github.copilot.chat.codeGeneration.useInstructionFiles`: `true`

  *Doku, Code* – [copilot-settings](https://code.visualstudio.com/docs/copilot/reference/copilot-settings);
  `DEFAULT_INSTRUCTIONS_SOURCE_FOLDERS` und `DEFAULT_SKILL_SOURCE_FOLDERS` in `promptFileLocations.ts`.
- **V6** – Zu `chat.instructionsFilesLocations`: „Additional instruction locations discovered by
  the editor and forwarded to Agent Host harnesses. Supported, but not recommended for Copilot
  workflows because other clients do not inherit this setting.“
  *Doku* – copilot-settings.
- **V7** – Skills mit gleichem Namen fasst VS Code zusammen, der erste gewinnt. Der Kommentar im
  Code nennt ausdrücklich symlinkte Skill-Ordner. Ein Symlink `.claude/skills` → `.agents/skills`
  erzeugt also keine doppelten Skills.
  *Code* – `promptsServiceImpl.ts` Z. 611–627 (Kommentar: „… when `~/.claude/skills` is a symlink
  to `~/.agents/skills` …“), außerdem Z. 1554.
- **V8** – Bei `AGENTS.md`, `CLAUDE.md` und `copilot-instructions.md` überspringt VS Code einen
  Symlink, dessen Ziel schon geladen ist. `.github/copilot-instructions.md` → `AGENTS.md` wird
  also nicht doppelt geladen.
  *Code* – `promptsServiceImpl.ts` Z. 927–946: „first look at non-symlinked files, then add
  symlinks only if target not already included“.
- **V9** – Für Regeldateien gibt es diese Symlink-Prüfung nicht; doppelte erkennt VS Code nur am
  Pfad (`ResourceSet`). Dieselbe Regel unter zwei Pfaden, z. B. `.agents/rules/…` und über
  Symlink `.claude/rules/…`, wird deshalb **vermutlich zweimal** angehängt.
  *Code, Schluss (nicht getestet)* – `promptFilesLocator.ts` Z. 196–212;
  `computeAutomaticInstructions.ts` Z. 221–226 (`context.instructions.has(uri)`).
- **V10** – Copilot CLI: Regeln mit Dateimuster nur `.github/instructions/**/*.instructions.md`;
  „The file name must end with `.instructions.md`“; `applyTo` ist Pflicht; `.claude/rules` wird
  nicht genannt; weitere Ordner über `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`.
  *Doku* – [Copilot CLI › custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions).
- **V11** – GitHub Copilot, Skills im Projekt: `.github/skills`, `.claude/skills`,
  `.agents/skills`; „Supported locations depend on the surface“.
  *Doku* – [about-agent-skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills).

### 4.3 Codex

- **X1** – Anweisungen nur über `AGENTS.md`: vom Projekt-Root bis zum Arbeitsordner, je Ordner
  höchstens eine Datei (`AGENTS.override.md` vor `AGENTS.md` vor `project_doc_fallback_filenames`),
  zusammen höchstens 32 KiB (`project_doc_max_bytes`). Ordner unterhalb des Arbeitsordners
  werden nicht geladen. Regeln mit Dateimuster gibt es nicht.
  *Doku* – [agents-md](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
  (developers.openai.com/codex/guides/agents-md leitet dorthin um).
- **X2** – Skills: `.agents/skills` vom Arbeitsordner bis zum Repo-Root, `$HOME/.agents/skills`,
  `/etc/codex/skills`. Symlinkte Skill-Ordner werden verfolgt. Gleichnamige Skills werden nicht
  zusammengeführt, beide erscheinen. `.claude/skills` wird nicht genannt.
  *Doku* – [build-skills](https://learn.chatgpt.com/docs/build-skills).
- **X3** – Codex-„Rules“ sind keine Anweisungen, sondern Starlark-Dateien (`*.rules`,
  `prefix_rule(pattern = …, decision = …)` mit `allow`, `prompt` oder `forbidden`). Sie steuern,
  welche Befehle Codex außerhalb der Sandbox ausführen darf – vergleichbar mit `permissions` in
  `.claude/settings.json`, nicht mit `.claude/rules/`. Orte: `~/.codex/rules/`, `rules/` in der
  Team-Config, `<repo>/.codex/rules/` („load only when the project `.codex/` layer is trusted“).
  Als experimentell markiert. `.agents/rules` wird nicht genannt.
  *Doku* – [rules](https://learn.chatgpt.com/docs/agent-configuration/rules). Der Vergleich mit
  `permissions` ist *Schluss*.

### 4.4 Antigravity

- **A1** – Regeln im Workspace, in jedem Ordner bis zum Repo-Root: `AGENTS.md` oder `GEMINI.md`;
  `.agents/AGENTS.md` oder `.agents/GEMINI.md`; `.agents/rules/*.md` (alt: `.agent/rules/*.md`).
  `AGENTS.md` und `GEMINI.md` sind immer aktiv und haben kein Frontmatter.
  *Doku* – [rules](https://antigravity.google/docs/rules/).
- **A2** – Frontmatter in `rules/`: `trigger` ist Pflicht (`always_on`, `model_decision`, `glob`,
  `manual`). Für `glob` braucht es `globs` (oder `glob`), kommagetrennt; Muster, die mit `*`
  beginnen, in Anführungszeichen. Beispiel der Doku: `globs: "*.proto, **/*.pb.go"`.
  → `trigger: glob` mit `globs: "**/*.py"` aus dem Issue passt dazu.
  *Doku* – rules.
- **A3** – Eine Datei ohne Frontmatter oder mit ungültigem `trigger` (z. B. `alwaysOn`) wird ohne
  Meldung verworfen. Wie Antigravity mit fremden Feldern (`applyTo`, `paths`) und mit
  YAML-Kommentaren im Frontmatter umgeht, steht nicht in der Doku.
  *Doku* – rules.
- **A4** – Unterordner von `.agents/rules/` werden nicht gelesen (außer über `.agents/rules.json`).
  *Doku* – rules.
- **A5** – Grenzen: 24.000 Byte je Regeldatei; 20.000 Token gemeinsam für globale und
  `always_on`-Regeln.
  *Doku* – rules.
- **A6** – Skills: `<workspace-root>/.agents/skills/<skill-folder>/`; das alte `.agent/skills`
  wird weiter gelesen. Name und Beschreibung sieht der Agent in jedem Zug, den vollen Inhalt liest
  er bei Bedarf. Symlinks werden nicht erwähnt.
  *Doku* – [skills](https://antigravity.google/docs/skills).
- **A7** – Antigravity ist eine eigene IDE und kein Harness in VS Code (V1). Die `chat.*`-Einstellungen
  in `.vscode/settings.json` betreffen es nicht.
  *Doku (V1), Schluss.*

## 5. Eigene Tests

Alle Tests liefen in Wegwerf-Repos (lokales `origin.git` plus Klone) im Scratchpad der Sitzung,
nicht im Projekt:
`C:\Users\nikol\AppData\Local\Temp\claude\ea4caee2805d6dd5\c9b5261f-6690-4943-b43c-91191afefebd\scratchpad\`.
Dort liegen auch die Skripte, die Test-Repos (`symtest`, `symtest_b`, `symtest_c`, `mktest`,
`mktest2`, `wtest`) und der geladene VS-Code-Quelltext (`vscode-src/`). Das Scratchpad ist
temporär und kann vom System geleert werden, **deshalb steht der Wortlaut der Skripte hier**.

> **Achtung:** Jedes Skript beginnt mit `rm -rf "$S"`. Vor dem Ausführen `S=` auf einen leeren
> Testordner setzen. Ausführen in Git Bash (unter Windows mit Entwicklermodus).
> `MSYS=winsymlinks:nativestrict` sorgt dafür, dass `ln -s` echte Windows-Symlinks anlegt.

Ablauf in allen drei Skripten: Klon A legt den alten Stand an und pusht. Klon B klont wie ein
bestehender Nutzer und legt `.claude/settings.local.json` an. A baut um und pusht, B macht
`git pull`.

| Skript | Zeilen | SHA-256 |
|---|---|---|
| `symtest.sh` | 46 | `34f97f7a2c337db3303f0f00f29a74f91d66d5ba258145fba84bb46d3d451f69` |
| `symtest_b.sh` | 33 | `81f32626b0113d328966d1f9613ffa2902b83cf6329239107e0291f83c35af03` |
| `symtest_c.sh` | 36 | `9cca651fff280a8802ab0e3f26ffb6152c2baf2b850ef726a885b1095b10d58b` |

### T1 – Umbau wie im Issue (`symtest.sh`)

```bash
#!/usr/bin/env bash
# Simuliert Issue #8 in Wegwerf-Repos: alter Stand -> neuer Stand, Pull in bestehendem Klon.
set -u
S="C:/Users/nikol/AppData/Local/Temp/claude/ea4caee2805d6dd5/c9b5261f-6690-4943-b43c-91191afefebd/scratchpad/symtest"
rm -rf "$S"; mkdir -p "$S"; cd "$S" || exit 1
export MSYS=winsymlinks:nativestrict GIT_ASK_YESNO=false GIT_TERMINAL_PROMPT=0
cfg() { git config user.name Test; git config user.email test@example.invalid; git config core.symlinks true; }

git init -q --bare -b main origin.git
git -c core.symlinks=true clone -q origin.git A 2>/dev/null; cd A; cfg; git checkout -q -b main
mkdir -p .github/instructions .claude/rules .claude/skills
printf -- '---\napplyTo: "**/*.py"\npaths:\n  - "**/*.py"\n---\n# Python-Regeln\n' > .github/instructions/python.instructions.md
ln -s ../../.github/instructions/python.instructions.md .claude/rules/python.md
: > .claude/skills/.gitkeep
printf '.claude/settings.local.json\n' > .gitignore
printf 'Einrichtung v1\n' > EINRICHTUNG.md
git add -A; git commit -qm "alter Stand"; git push -q origin main
cd ..

echo "### Klon B = bestehender Nutzer-Klon (settings.local.json + EINRICHTUNG.md skip-worktree/gelöscht)"
git -c core.symlinks=true clone -q origin.git B; cd B; cfg
echo '{"permissions":{"allow":["Bash(uv run pytest)"]}}' > .claude/settings.local.json
git update-index --skip-worktree EINRICHTUNG.md; rm EINRICHTUNG.md
git status --short --ignored; cd ..

echo "### Klon A: Issue #8 umsetzen"
cd A
git rm -rq .claude
mkdir -p .agents/rules .agents/skills
git mv .github/instructions/python.instructions.md .agents/rules/python.md
sed -i 's/^applyTo:/trigger: glob\nglobs: "**\/*.py"\napplyTo:/' .agents/rules/python.md
: > .agents/skills/.gitkeep
ln -s .agents .claude
printf 'Einrichtung v2 (4 Harnesses)\n' > EINRICHTUNG.md
git add -A; git commit -qm "Issue 8"; git push -q origin main
echo "--- Index in A:"; git ls-files -s
echo "--- Symlink-Ziel im Commit: '$(git cat-file -p HEAD:.claude)'"
cd ..

echo "### Klon B: git pull"
cd B
timeout 60 git pull --no-rebase 2>&1; echo "pull exit: $?"
echo "--- status:"; git status --short --ignored
echo "--- settings.local.json noch da?"; ls -la .claude/ 2>&1; ls -la .agents/ 2>&1
echo "--- EINRICHTUNG.md im Arbeitsordner?"; ls EINRICHTUNG.md 2>&1; git ls-files -v EINRICHTUNG.md
cd ..
```

Ausgabe (Warnungen „LF will be replaced by CRLF“ entfernt):

```text
### Klon B = bestehender Nutzer-Klon (settings.local.json + EINRICHTUNG.md skip-worktree/gelöscht)
!! .claude/settings.local.json
### Klon A: Issue #8 umsetzen
--- Index in A:
100644 2852bf7efc3322bafdcb987fd051b51f014dc8fa 0	.agents/rules/python.md
100644 e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 0	.agents/skills/.gitkeep
120000 c0ca46856636174575b3ec5e804c4530a5f99fff 0	.claude
100644 d6b130cf3de22c031b818e6f8d8ec358d2ad9987 0	.gitignore
100644 38aef6d956a03f6d5f0055b1030e4eb0c8d92855 0	EINRICHTUNG.md
--- Symlink-Ziel im Commit: '.agents'
### Klon B: git pull
From C:/Users/nikol/AppData/Local/Temp/claude/ea4caee2805d6dd5/c9b5261f-6690-4943-b43c-91191afefebd/scratchpad/symtest/origin
   513b45f..9658b65  main       -> origin/main
Updating 513b45f..9658b65
Fast-forward
 .github/instructions/python.instructions.md => .agents/rules/python.md | 2 ++
 {.claude => .agents}/skills/.gitkeep                                   | 0
 .claude                                                                | 1 +
 .claude/rules/python.md                                                | 1 -
 EINRICHTUNG.md                                                         | 2 +-
 5 files changed, 4 insertions(+), 2 deletions(-)
 rename .github/instructions/python.instructions.md => .agents/rules/python.md (67%)
 rename {.claude => .agents}/skills/.gitkeep (100%)
 create mode 120000 .claude
 delete mode 120000 .claude/rules/python.md
pull exit: 0
--- status:
--- settings.local.json noch da?
total 4
drwxr-xr-x 1 nikol 197609 0 Nov  5 10:38 .
drwxr-xr-x 1 nikol 197609 0 Nov  5 10:38 ..
drwxr-xr-x 1 nikol 197609 0 Nov  5 10:38 rules
drwxr-xr-x 1 nikol 197609 0 Nov  5 10:38 skills
total 4
drwxr-xr-x 1 nikol 197609 0 Nov  5 10:38 .
drwxr-xr-x 1 nikol 197609 0 Nov  5 10:38 ..
drwxr-xr-x 1 nikol 197609 0 Nov  5 10:38 rules
drwxr-xr-x 1 nikol 197609 0 Nov  5 10:38 skills
--- EINRICHTUNG.md im Arbeitsordner?
EINRICHTUNG.md
S EINRICHTUNG.md
```

Nachtest in B sowie frische Klone C (`core.symlinks=true`) und D (`core.symlinks=false`),
einzeln ausgeführt in Git Bash:

```bash
cd "$S/B"
echo "--- EINRICHTUNG.md Inhalt nach Pull:"; cat EINRICHTUNG.md
echo "--- irgendwo noch settings.local.json?"; find "$S/B" -name 'settings.local.json' -not -path '*/.git/*' 2>/dev/null | sed 's/^/  /'; echo "  (Ende Suche)"
echo "--- Claude-Code-typische Datei über den Symlink schreiben:"
echo '{}' > .claude/settings.local.json
git status --short --ignored
echo "--- check-ignore:"; git check-ignore -v .agents/settings.local.json || echo "  .agents/settings.local.json wird NICHT ignoriert"
cd "$S"
export MSYS=winsymlinks:nativestrict
git -c core.symlinks=true clone -q origin.git C
git -c core.symlinks=false clone -q origin.git D
echo "--- Klon D (core.symlinks=false): Typ von .claude:"; file D/.claude 2>/dev/null || ls -la D/.claude; echo "  Inhalt: '$(cat D/.claude)'"
```

```text
--- EINRICHTUNG.md Inhalt nach Pull:
Einrichtung v2 (4 Harnesses)
--- irgendwo noch settings.local.json?
  (Ende Suche)
--- Claude-Code-typische Datei über den Symlink schreiben:
?? .agents/settings.local.json
--- check-ignore:
  .agents/settings.local.json wird NICHT ignoriert
--- Klon D (core.symlinks=false): Typ von .claude:
D/.claude: ASCII text, with no line terminators
  Inhalt: '.agents'
```

Link-Typen in B und C (PowerShell):

```powershell
$S = "C:\Users\nikol\AppData\Local\Temp\claude\ea4caee2805d6dd5\c9b5261f-6690-4943-b43c-91191afefebd\scratchpad\symtest"; foreach ($r in @('B','C')) { $i = Get-Item -LiteralPath "$S\$r\.claude" -Force -ErrorAction SilentlyContinue; if ($i) { "{0}: .claude LinkType='{1}' Target='{2}' IsDir={3}; .claude\rules\python.md lesbar={4}" -f $r, $i.LinkType, ($i.Target -join ','), $i.PSIsContainer, (Test-Path -LiteralPath "$S\$r\.claude\rules\python.md") } }; $p = Get-Item -LiteralPath "$S\C\.agents\rules\python.md"; "C: .agents\rules\python.md LinkType='{0}'" -f $p.LinkType
```

```text
B: .claude LinkType='SymbolicLink' Target='.agents' IsDir=True; .claude\rules\python.md lesbar=True
C: .claude LinkType='SymbolicLink' Target='.agents' IsDir=True; .claude\rules\python.md lesbar=True
C: .agents\rules\python.md LinkType=''
```

Befunde:

- **T1a** – Der Ordner-Symlink entsteht korrekt (Mode `120000`, Ziel `.agents`), auch in frischen
  Klonen.
- **T1b** – **`git pull` löscht im bestehenden Klon `.claude/settings.local.json` ohne Meldung.**
  Die Datei ist ignoriert und liegt in einem Ordner, den Git durch einen Symlink ersetzt.
- **T1c** – **Danach geschriebene `.claude/settings.local.json` liegt als
  `.agents/settings.local.json` vor und ist nicht ignoriert** – weder über `.gitignore` noch über
  die globalen Excludes dieses Rechners (C7). Git folgt Symlinks nicht, die Muster
  `.claude/settings.local.json` und `**/.claude/settings.local.json` passen nicht auf `.agents/…`.
- **T1d** – **`EINRICHTUNG.md` ist nach dem Pull wieder da** (mit neuem Inhalt), obwohl sie lokal
  gelöscht war. `skip-worktree` bleibt gesetzt, `git status` zeigt nichts an.
- **T1e** – Ohne `core.symlinks` ist `.claude` eine Textdatei mit dem Inhalt `.agents`; Claude Code
  fände dann weder Regeln noch Skills.

### T2 – Zwischenvariante (`symtest_b.sh`)

`.claude/` bleibt ein echter Ordner, nur `rules` und `skills` sind Symlinks; zusätzlich
`.github/instructions/python.instructions.md` als Symlink auf `../../.agents/rules/python.md`.

Der erste Lauf scheiterte am Testskript: `cfg()` enthielt zusätzlich `git config core.autocrlf false;`.
Weil Klon B mit dem systemweiten `core.autocrlf=true` ausgecheckt und die Einstellung erst danach
umgestellt wurde, galten Dateien als geändert. Der Pull brach ab mit „Your local changes to the
following files would be overwritten by merge: .github/instructions/python.instructions.md“.
Die Einstellung wurde entfernt; der Wortlaut unten ist die korrigierte Fassung.

```bash
#!/usr/bin/env bash
# Variante B: .claude bleibt echter Ordner, nur rules/ und skills/ sind Symlinks nach .agents/.
set -u
S="C:/Users/nikol/AppData/Local/Temp/claude/ea4caee2805d6dd5/c9b5261f-6690-4943-b43c-91191afefebd/scratchpad/symtest_b"
rm -rf "$S"; mkdir -p "$S"; cd "$S" || exit 1
export MSYS=winsymlinks:nativestrict GIT_ASK_YESNO=false GIT_TERMINAL_PROMPT=0
cfg() { git config user.name Test; git config user.email test@example.invalid; git config core.symlinks true; }
git init -q --bare -b main origin.git
git -c core.symlinks=true clone -q origin.git A 2>/dev/null; cd A; cfg; git checkout -q -b main
mkdir -p .github/instructions .claude/rules .claude/skills
printf -- '---\napplyTo: "**/*.py"\npaths:\n  - "**/*.py"\n---\n# Python-Regeln\n' > .github/instructions/python.instructions.md
ln -s ../../.github/instructions/python.instructions.md .claude/rules/python.md
: > .claude/skills/.gitkeep
printf '.claude/settings.local.json\n' > .gitignore
git add -A; git commit -qm "alter Stand"; git push -q origin main; cd ..
git -c core.symlinks=true clone -q origin.git B; cd B; cfg
echo '{"permissions":{}}' > .claude/settings.local.json; cd ..
cd A
git rm -rq .claude/rules .claude/skills
mkdir -p .agents/rules .agents/skills
git mv .github/instructions/python.instructions.md .agents/rules/python.md
: > .agents/skills/.gitkeep
mkdir -p .claude
ln -s ../.agents/rules  .claude/rules
ln -s ../.agents/skills .claude/skills
ln -s ../../.agents/rules/python.md .github/instructions/python.instructions.md
git add -A; git commit -qm "Variante B"; git push -q origin main
echo "--- Index:"; git ls-files -s | sed 's/^/  /'; cd ..
echo "### Pull in bestehendem Klon B"
cd B; timeout 60 git pull -q --no-rebase 2>&1; echo "pull exit: $?"
echo "--- .claude/settings.local.json noch da? $(test -f .claude/settings.local.json && echo JA || echo NEIN)"
echo "--- status --ignored:"; git status --short --ignored | sed 's/^/  /'
echo "--- Copilot-Pfad lesbar: $(head -c 3 .github/instructions/python.instructions.md)  Claude-Pfad lesbar: $(head -c 3 .claude/rules/python.md)"
```

```text
--- Index:
  100644 e53534d18ac79cb062089b88671c2b72f7908880 0	.agents/rules/python.md
  100644 e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 0	.agents/skills/.gitkeep
  120000 2d5c9a97a7ae5b286899e48485ad31d0d8e94828 0	.claude/rules
  120000 2b7a412b8fa0fb7e985b0793321bd4e698f2b6cd 0	.claude/skills
  120000 cbc786b9bcc0cf7c086e10f939f667504372672f 0	.github/instructions/python.instructions.md
  100644 d6b130cf3de22c031b818e6f8d8ec358d2ad9987 0	.gitignore
### Pull in bestehendem Klon B
pull exit: 0
--- .claude/settings.local.json noch da? JA
--- status --ignored:
  !! .claude/settings.local.json
--- Copilot-Pfad lesbar: ---  Claude-Pfad lesbar: ---
```

Link-Typen in B (PowerShell, `Get-Item` je Pfad):

```text
.claude                                       LinkType='' Target='' IsDir=True
.claude\rules                                 LinkType='SymbolicLink' Target='..\.agents\rules' IsDir=True
.claude\skills                                LinkType='SymbolicLink' Target='..\.agents\skills' IsDir=True
.github\instructions\python.instructions.md   LinkType='SymbolicLink' Target='..\..\.agents\rules\python.md' IsDir=False
.agents\rules\python.md                       LinkType='' Target='' IsDir=False
```

### T3 – Empfohlener Zielzustand (`symtest_c.sh`)

Wie T2, aber die Regel heißt `.agents/rules/python.instructions.md` (Frontmatter ergänzt), und
`.github/instructions/` entfällt.

Der erste Lauf scheiterte am Testskript: Nach `git rm -r .claude/rules .claude/skills` entfernt Git
den leer gewordenen Ordner `.claude`, und `ln -s` brach mit „Bad address“ ab. Korrigiert durch
`mkdir -p .claude` (Zeile 25). **Beim Umsetzen beachten.**

```bash
#!/usr/bin/env bash
# Variante C = empfohlener Zielzustand: .claude bleibt echter Ordner, rules/ und skills/ sind Symlinks,
# Regel heißt .agents/rules/python.instructions.md, .github/instructions/ entfällt.
set -u
S="C:/Users/nikol/AppData/Local/Temp/claude/ea4caee2805d6dd5/c9b5261f-6690-4943-b43c-91191afefebd/scratchpad/symtest_c"
rm -rf "$S"; mkdir -p "$S"; cd "$S" || exit 1
export MSYS=winsymlinks:nativestrict GIT_ASK_YESNO=false GIT_TERMINAL_PROMPT=0
cfg() { git config user.name Test; git config user.email test@example.invalid; git config core.symlinks true; }
git init -q --bare -b main origin.git
git -c core.symlinks=true clone -q origin.git A 2>/dev/null; cd A; cfg; git checkout -q -b main
mkdir -p .github/instructions .claude/rules .claude/skills
printf -- '---\napplyTo: "**/*.py"\npaths:\n  - "**/*.py"\n---\n# Python-Regeln\n' > .github/instructions/python.instructions.md
ln -s ../../.github/instructions/python.instructions.md .claude/rules/python.md
: > .claude/skills/.gitkeep
printf '.claude/settings.local.json\n' > .gitignore
git add -A; git commit -qm "alter Stand"; git push -q origin main; cd ..
git -c core.symlinks=true clone -q origin.git B; cd B; cfg
echo '{"permissions":{}}' > .claude/settings.local.json; cd ..
cd A
git rm -rq .claude/rules .claude/skills
mkdir -p .agents/rules .agents/skills
git mv .github/instructions/python.instructions.md .agents/rules/python.instructions.md
sed -i 's/^applyTo:/trigger: glob\nglobs: "**\/*.py"\napplyTo:/' .agents/rules/python.instructions.md
: > .agents/skills/.gitkeep
mkdir -p .claude   # git rm entfernt den leer gewordenen Ordner .claude
ln -s ../.agents/rules  .claude/rules
ln -s ../.agents/skills .claude/skills
git add -A; git commit -qm "Variante C"; git push -q origin main
echo "--- Index:"; git ls-files -s | sed 's/^/  /'
echo "--- Frontmatter:"; sed -n '1,8p' .agents/rules/python.instructions.md | sed 's/^/  /'; cd ..
echo "### Pull in bestehendem Klon B"
cd B; timeout 60 git pull -q --no-rebase 2>&1; echo "pull exit: $?"
echo "--- .claude/settings.local.json noch da? $(test -f .claude/settings.local.json && echo JA || echo NEIN)"
echo "--- .github/instructions noch da? $(test -e .github/instructions && echo JA || echo NEIN)"
echo "--- status --ignored:"; git status --short --ignored | sed 's/^/  /'
echo "--- Claude-Pfad lesbar: $(head -c 3 .claude/rules/python.instructions.md)"
```

```text
--- Index:
  100644 2852bf7efc3322bafdcb987fd051b51f014dc8fa 0	.agents/rules/python.instructions.md
  100644 e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 0	.agents/skills/.gitkeep
  120000 2d5c9a97a7ae5b286899e48485ad31d0d8e94828 0	.claude/rules
  120000 2b7a412b8fa0fb7e985b0793321bd4e698f2b6cd 0	.claude/skills
  100644 d6b130cf3de22c031b818e6f8d8ec358d2ad9987 0	.gitignore
--- Frontmatter:
  ---
  trigger: glob
  globs: "**/*.py"
  applyTo: "**/*.py"
  paths:
    - "**/*.py"
  ---
  # Python-Regeln
### Pull in bestehendem Klon B
pull exit: 0
--- .claude/settings.local.json noch da? JA
--- .github/instructions noch da? NEIN
--- status --ignored:
  !! .claude/settings.local.json
--- Claude-Pfad lesbar: ---
```

Link-Typen in B (PowerShell, `Get-Item` je Pfad):

```text
.claude                                  LinkType='' Target='' IsDir=True
.claude\rules                            LinkType='SymbolicLink' Target='..\.agents\rules' IsDir=True
.claude\skills                           LinkType='SymbolicLink' Target='..\.agents\skills' IsDir=True
.claude\rules\python.instructions.md     LinkType='' Target='C:\…\symtest_c\B\.agents\rules\python.instructions.md' IsDir=False
.agents\rules\python.instructions.md     LinkType='' Target='' IsDir=False
```

Befund: Pull ohne Fehler, `settings.local.json` bleibt erhalten und ignoriert, beide Links sind
Ordner-Symlinks, die Regel ist über `.claude/rules/` lesbar.

### T4 – Symlink anlegen unter Windows

```powershell
$S = "C:\Users\nikol\AppData\Local\Temp\claude\ea4caee2805d6dd5\c9b5261f-6690-4943-b43c-91191afefebd\scratchpad\mktest"; if (Test-Path $S) { Remove-Item -Recurse -Force $S -Confirm:$false }; New-Item -ItemType Directory -Force "$S\.agents\rules" | Out-Null; Set-Location $S; git init -q; git config core.symlinks true
"Admin: " + ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
"PS-Version: " + $PSVersionTable.PSVersion
"--- Variante 1: New-Item -ItemType SymbolicLink"
try { New-Item -ItemType SymbolicLink -Path "link_newitem" -Target ".agents" -ErrorAction Stop | Out-Null; $i = Get-Item "link_newitem"; "  ok: LinkType={0} Target='{1}'" -f $i.LinkType, ($i.Target -join ',') } catch { "  FEHLER: " + $_.Exception.Message }
"--- Variante 2: cmd mklink /D"
$out = cmd /c "mklink /D link_mklink .agents 2>&1"; "  " + ($out -join ' '); $i = Get-Item "link_mklink" -ErrorAction SilentlyContinue; if ($i) { "  LinkType={0} Target='{1}'" -f $i.LinkType, ($i.Target -join ',') }
"--- Was git daraus macht:"
git add -A 2>$null; git ls-files -s; foreach ($l in @('link_newitem','link_mklink')) { $t = git cat-file -p ":$l" 2>$null; if ($t) { "  {0} -> '{1}'" -f $l, $t } }
```

```text
Admin: False
PS-Version: 5.1.26100.9818
--- Variante 1: New-Item -ItemType SymbolicLink
  FEHLER: Für diesen Vorgang sind Administratorrechte erforderlich.
--- Variante 2: cmd mklink /D
  symbolische Verknüpfung erstellt für link_mklink <<===>> .agents
  LinkType=SymbolicLink Target='.agents'
--- Was git daraus macht:
120000 c0ca46856636174575b3ec5e804c4530a5f99fff 0	link_mklink
  link_mklink -> '.agents'
```

Zweiter Versuch mit Unterordner und Backslash im Ziel (Ordner `mktest2`):

```powershell
New-Item -ItemType Directory -Force "$S\.agents\rules", "$S\.claude" | Out-Null; Set-Location $S; git init -q; git config core.symlinks true; cmd /c "mklink /D .claude\rules ..\.agents\rules" | Out-Null; git add -A 2>$null; git ls-files -s; "gespeichertes Ziel: '" + (git cat-file -p ":.claude/rules") + "'"
```

```text
120000 2d5c9a97a7ae5b286899e48485ad31d0d8e94828 0	.claude/rules
gespeichertes Ziel: '../.agents/rules'
```

Befund: `New-Item -ItemType SymbolicLink` braucht in Windows PowerShell 5.1 Administratorrechte,
auch mit Entwicklermodus. `cmd /c mklink /D` geht ohne. Git speichert das Ziel mit `/`; der Blob
`2d5c9a97…` ist derselbe wie mit `ln -s` in T2 und T3.

### T5 – Schreiben durch einen Ordner-Symlink (Write-Werkzeug von Claude Code)

```powershell
$S = "C:\Users\nikol\AppData\Local\Temp\claude\ea4caee2805d6dd5\c9b5261f-6690-4943-b43c-91191afefebd\scratchpad\wtest"; if (Test-Path $S) { Remove-Item -Recurse -Force $S -Confirm:$false }; New-Item -ItemType Directory -Force "$S\.agents\skills\demo" | Out-Null; Set-Content -Path "$S\.agents\skills\demo\SKILL.md" -Value "alt" -Encoding utf8; Set-Location $S; cmd /c "mklink /D .claude .agents" | Out-Null; (Get-Item "$S\.claude").LinkType
```

Ausgabe `SymbolicLink`. Danach schrieb das Write-Werkzeug `…\wtest\.claude\skills\neu\SKILL.md`
(„File created successfully“); `ls` zeigte die Datei unter `…\wtest\.agents\skills\neu\SKILL.md`
(96 Byte). Nur außerhalb des Projektordners getestet.

## 6. Folgerungen für Issue #8 (Schluss)

Was jeder Harness mit dem Zielzustand **laut Issue** liest:

| Harness | `AGENTS.md` | `.agents/rules/python.md` | `.agents/skills/` |
|---|---|---|---|
| Claude Code | ✅ C1 | ✅ über `.claude/rules` (C3) | ✅ über `.claude/skills` (C5) |
| Copilot, Local-Harness | ✅ V5 | ⚠️ nur über `.claude/rules` und den Symlink; der Eintrag `.agents/rules` wirkt nicht (V3) | ✅ V5, V7 |
| Copilot, Copilot-Harness | ✅ V2 | ❌ vermutlich nicht, nur `*.instructions.md` (V2, V10) | ✅ wahrscheinlich (V11) |
| Codex | ✅ X1 | ❌ X1, X3 | ✅ X2 |
| Antigravity | ✅ A1 | ✅ A1, A2 | ✅ A6 |

Nötige Änderungen:

1. Regel `.agents/rules/python.instructions.md` nennen (V3; Claude Code und Antigravity lesen jede
   `.md`-Datei, C3, A1) und in `.vscode/settings.json` setzen:
   `"chat.instructionsFilesLocations": { ".agents/rules": true, ".claude/rules": false }`.
   Das `false` verhindert, dass VS Code dieselbe Regel zusätzlich über den Symlink findet (V4, V9).
   Mit dem Namen `python.md` wäre der Eintrag wirkungslos und kann entfallen.
2. Für Codex in `AGENTS.md` auf die Regel verweisen, etwa „Für Python-Dateien zusätzlich
   `.agents/rules/python.instructions.md` beachten.“ (X1, X3). Codex liest sie dann bei Bedarf,
   nicht automatisch.
3. `.claude` nicht als Ganzes verlinken, sondern `.claude/rules` → `../.agents/rules` und
   `.claude/skills` → `../.agents/skills` (C6, C8, T1b, T1c, T3). Der Ordner-Symlink würde
   Worktrees blockieren, persönliche Einstellungen beim Pull löschen und sie danach ungeschützt in
   `.agents/` ablegen.

Vorgeschlagenes Frontmatter (ohne YAML-Kommentare, solange A3 offen ist):

```yaml
---
applyTo: "**/*.py"
paths:
  - "**/*.py"
trigger: glob
globs: "**/*.py"
---
```

Weitere Punkte:

4. „Beim Kaltstart“ gilt nur für `AGENTS.md`. Regeln mit Dateimuster greifen erst bei passenden
   Dateien; von Skills ist anfangs nur die Liste bekannt (C3, C5, A6, V2).
5. `chat.useAgentsMdFile` und `chat.useAgentSkills` stehen ohnehin auf `true` (V5) und können
   entfallen. Ein Eintrag im Arbeitsbereich würde nur eine `false`-Benutzereinstellung überstimmen.
6. Die Akzeptanzkriterien prüfen nur Dateitypen. `pytest` und `ruff` sagen über die Änderung
   nichts aus, und die CI läuft wegen ihres `paths`-Filters für diesen PR nicht. Vorschlag: pro
   Harness ein Funktionstest (Regel taucht beim Bearbeiten einer `.py`-Datei auf); in Antigravity
   prüfen, dass die Regel mit Trigger „Glob“ erscheint (A3); `git ls-files -s` zeigt Mode `120000`
   für die Links.
7. Ändert der PR `EINRICHTUNG.md`, erscheint die Datei in Klonen mit `skip-worktree` wieder (T1d).
   Zum Bearbeiten vorher `git update-index --no-skip-worktree EINRICHTUNG.md` und
   `git checkout -- EINRICHTUNG.md`.
8. Symlinks mit `cmd /c mklink /D` oder Git Bash anlegen, nicht mit `New-Item` (T4). Nach
   `git rm -r .claude/rules .claude/skills` den Ordner `.claude` ggf. neu anlegen (T3).
9. Regeln flach in `.agents/rules/` ablegen (A4); jede Regel braucht ein gültiges `trigger` (A3).
10. Sollen auch Copilot CLI oder GitHub.com die Regel sehen: zusätzlich
    `.github/instructions/python.instructions.md` als Symlink (V10, T2). In VS Code Local dann
    eventuell doppelt (V9).

## 7. Offen – für die Gegenprüfung

Wichtigstes zuerst:

1. **V3, V4, V9** – VS-Code-Quelltext am angegebenen Stand nachlesen. Besser noch live: Wird
   `.agents/rules/python.md` bzw. `python.instructions.md` mit dem Eintrag in
   `chat.instructionsFilesLocations` geladen? (Chat → verwendete Referenzen.)
2. **V2, V6, V10** – Reicht VS Code Regeln aus `chat.instructionsFilesLocations` wirklich an den
   Copilot-Harness weiter, und nur mit Endung `.instructions.md`?
3. **C6** – Worktree-Verweigerung bei symlinktem `.claude`: nur Doku-Aussage, nicht getestet.
4. **A2, A3** – Liest Antigravity eine Regeldatei mit fremden Feldern (`applyTo`, `paths`) und mit
   YAML-Kommentaren im Frontmatter? Die heutige Datei enthält Kommentare.
5. **V9** – Wird dieselbe Regel unter zwei Pfaden wirklich zweimal angehängt? Das beträfe schon den
   heutigen Stand (`.github/instructions/…` plus `.claude/rules/python.md`).
6. **C1, C2** – Betrachtet wurde die Claude-Code-Erweiterung. Der in VS Code eingebaute
   Claude-Harness liest laut V2 nur `CLAUDE.md` – liest er `AGENTS.md` trotzdem?
7. **V11, X2** – Doppelte Skills in Copilot CLI oder Codex, wenn zwei Skill-Ordner auf dasselbe
   Ziel zeigen? (Codex: gleichnamige Skills „both can appear“.)

## 8. Übersicht: Ablageorte je Tool – einzeln und gemeinsam

Nachträglich ergänzt (gleiche Sitzung). Zeilen „Anweisungen“, „Regeln“, „Skills“ beruhen auf den
Befunden in Abschnitt 4. Die Zeilen „Hooks“, „Team-Einstellungen“, „Befehlsfreigaben“ und
„Persönlich / global“ wurden nur grob recherchiert (H1–H4 unten, Doku über Abruf-Werkzeug) und
**nicht getestet**. „Nicht recherchiert“ heißt: keine Quelle gelesen – nicht „gibt es nicht“.

Belege zu den Hooks:

- **H1 – Claude Code:** Hooks im Schlüssel `hooks` der Settings-Dateien (`.claude/settings.json`,
  `.claude/settings.local.json`, `~/.claude/settings.json`), dazu Plugins und Frontmatter von
  Skills/Subagenten; keine eigene Hook-Datei im Projekt. Events u. a. `PreToolUse`, `PostToolUse`,
  `SessionStart`, `Stop`; Eingabe als JSON auf stdin (`tool_name`, `tool_input`, `cwd`); blocken mit
  Exit-Code 2 oder `hookSpecificOutput.permissionDecision: "deny"`. Unter Windows Git Bash, ohne Git
  Bash PowerShell; Feld `shell` wählbar. – [hooks](https://code.claude.com/docs/en/hooks)
- **H2 – Codex:** `hooks.json` oder `[hooks]` in `config.toml`, in `~/.codex/` und `<repo>/.codex/`;
  Projekt-Hooks nur, wenn die Projektebene `.codex/` vertraut ist, und erst nach Freigabe über
  `/hooks`. Events u. a. `PreToolUse`, `PostToolUse`, `SessionStart`, `Stop`; Eingabe snake_case
  (`tool_name`, `tool_input`); blocken wie Claude Code (Exit-Code 2 oder `permissionDecision: "deny"`);
  `commandWindows` für Windows. – [hooks](https://learn.chatgpt.com/docs/hooks)
- **H3 – GitHub Copilot:** `.github/hooks/*.json` mit `"version": 1` und camelCase-Events
  (`preToolUse`, `postToolUse`, `sessionStart` …), Befehle getrennt als `bash` und `powershell`.
  Der Copilot-Harness in VS Code liest zusätzlich `.claude/settings.json` und
  `.claude/settings.local.json` (PascalCase-Alias mit Claude-ähnlichen Feldern, gleiche Argumente
  nicht garantiert); Local nur mit `chat.useClaudeHooks`. „Applicable hooks from multiple sources run
  together“ → derselbe Hook an zwei Orten läuft vermutlich zweimal. In VS Code als Preview markiert.
  – [VS Code hooks](https://code.visualstudio.com/docs/copilot/customization/hooks),
  [GitHub about-hooks](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-hooks)
- **H4 – Antigravity:** `.agents/hooks.json` im Workspace, global `~/.gemini/config/hooks.json`.
  Events `SessionStart`, `PreToolUse`, `PostToolUse`, `PreInvocation`, `PostInvocation`, `Stop`;
  `PreToolUse` kann `"deny"` oder `"ask"` liefern. Windows-Verhalten nicht dokumentiert.
  – [hooks](https://antigravity.google/docs/hooks)

### 8.1 Einzeln: wo jedes Tool von sich aus sucht

| Was | Claude Code | Codex | GitHub Copilot | Antigravity |
|---|---|---|---|---|
| Eigener Ordner | `.claude/` | `.codex/` | `.github/` (+ `.vscode/` für VS Code) | `.agents/` |
| Anweisungen fürs ganze Projekt | `CLAUDE.md` oder `.claude/CLAUDE.md`; sonst `AGENTS.md` (C1) | `AGENTS.md`, `AGENTS.override.md` hat Vorrang (X1) | `.github/copilot-instructions.md` oder `AGENTS.md` (V2) | `AGENTS.md` oder `GEMINI.md`, auch in `.agents/` (A1) |
| Regeln nur für bestimmte Dateien | `.claude/rules/*.md`, Feld `paths` (C3) | gibt es nicht (X1) | `.github/instructions/*.instructions.md`, Feld `applyTo` (V2, V10) | `.agents/rules/*.md`, Felder `trigger: glob` + `globs` (A1, A2) |
| Skills | `.claude/skills/<name>/SKILL.md` (C5) | `.agents/skills/<name>/` (X2) | `.github/skills/`, liest auch `.claude/skills` und `.agents/skills` (V5, V11) | `.agents/skills/<name>/` (A6) |
| Hooks | `.claude/settings.json`, Schlüssel `hooks` (H1) | `.codex/hooks.json` oder `.codex/config.toml` (H2) | `.github/hooks/*.json` (H3) | `.agents/hooks.json` (H4) |
| Team-Einstellungen | `.claude/settings.json` (C8) | `.codex/config.toml` (H2) | `.vscode/settings.json` (in VS Code) | nicht recherchiert |
| Befehlsfreigaben | `permissions` in `.claude/settings.json` (C8) | `.codex/rules/*.rules` (X3) | nicht recherchiert | nicht recherchiert |
| Persönlich / global | `~/.claude/`, `.claude/settings.local.json`, `CLAUDE.local.md` (C1, C7) | `~/.codex/`, `~/.agents/skills` (X1, X2) | `~/.copilot/` (V10) | `~/.gemini/` (A1, A6) |

### 8.2 Gemeinsam: alle vier Tools in einem Repo (Empfehlung aus Abschnitt 6)

| Was | Claude Code | Codex | GitHub Copilot | Antigravity |
|---|---|---|---|---|
| Eigener Ordner | `.claude/` bleibt ein echter Ordner, darin zwei Symlinks | `.codex/` erst bei Bedarf | `.github/` + `.vscode/` | `.agents/` = der gemeinsame Ordner |
| Anweisungen fürs ganze Projekt | `AGENTS.md` (keine `CLAUDE.md` anlegen) | `AGENTS.md` | `AGENTS.md`; optional `.github/copilot-instructions.md` als 🔗 für GitHub.com | `AGENTS.md` |
| Regeln nur für bestimmte Dateien | 🔗 `.claude/rules` → `../.agents/rules` | ↪ Verweis in `AGENTS.md` (liest bei Bedarf) | ⚙ `.agents/rules/` über `chat.instructionsFilesLocations` in `.vscode/settings.json` ⚠️ | 📄 `.agents/rules/python.instructions.md` |
| Skills | 🔗 `.claude/skills` → `../.agents/skills` | 📄 `.agents/skills/` | 📄 `.agents/skills/` (Standardordner) | 📄 `.agents/skills/` |
| Hooks | `.claude/settings.json` ruft das gemeinsame Skript auf (z. B. `.agents/hooks/*.py`) | `.codex/hooks.json` ruft das Skript auf | in VS Code nichts extra (liest `.claude/settings.json` mit); `.github/hooks/` nur für CLI und GitHub.com | `.agents/hooks.json` ruft das Skript auf |
| Team-Einstellungen | `.claude/settings.json` | `.codex/config.toml` | `.vscode/settings.json` | nicht recherchiert |
| Befehlsfreigaben | `permissions` in `.claude/settings.json` | `.codex/rules/*.rules` | nicht recherchiert | nicht recherchiert |
| Persönlich / global | `~/.claude/`, `.claude/settings.local.json`; **kein** `CLAUDE.local.md`, sonst wird `AGENTS.md` nicht mehr gelesen | `~/.codex/`, `~/.agents/skills` | `~/.copilot/` | `~/.gemini/` |

Zeichen: 📄 echte Datei bzw. echter Ordner · 🔗 Symlink · ↪ Verweis im Text · ⚙ über eine
Einstellung · ⚠️ nicht getestet (V2, V6, V10)

Im Vergleich zu 8.1 ändert sich nur in den ersten vier Zeilen etwas: Anweisungen, Regeln und Skills
liegen einmal in `AGENTS.md` bzw. `.agents/`, jedes Tool erreicht sie über sein Gegenstück. Ab
„Hooks“ bleibt es fast wie einzeln, weil es dafür keinen gemeinsamen Standard gibt; gemeinsam ist
höchstens das Hook-Skript (Schluss, nicht getestet).

## 9. Quellen

Alle abgerufen am 2026-11-05.

- Issue: [AIDED4SYS/GenAi_x_SysEng#8](https://github.com/AIDED4SYS/GenAi_x_SysEng/issues/8),
  gelesen mit `gh issue view 8`
- Claude Code:
  [memory](https://code.claude.com/docs/en/memory) ·
  [skills](https://code.claude.com/docs/en/skills) ·
  [claude-directory](https://code.claude.com/docs/en/claude-directory) ·
  [settings](https://code.claude.com/docs/en/settings) ·
  [worktrees](https://code.claude.com/docs/en/worktrees)
- VS Code, Doku:
  [custom-instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions) ·
  [custom-instructions-reference](https://code.visualstudio.com/docs/agents/reference/custom-instructions-reference) ·
  [copilot-settings](https://code.visualstudio.com/docs/copilot/reference/copilot-settings) ·
  [agent-skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills) ·
  [agent-harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
- VS Code, Quelltext (Commit `66501d43…`; letzter Commit der Datei in Klammern):
  [promptFileLocations.ts](https://github.com/microsoft/vscode/blob/66501d439e871b12b5afdebcf6454cd416322905/src/vs/workbench/contrib/chat/common/promptSyntax/config/promptFileLocations.ts) (`c1bc28bd`, 2026-07-28) ·
  [config.ts](https://github.com/microsoft/vscode/blob/66501d439e871b12b5afdebcf6454cd416322905/src/vs/workbench/contrib/chat/common/promptSyntax/config/config.ts) (`09a72906`, 2026-04-22) ·
  [promptFilesLocator.ts](https://github.com/microsoft/vscode/blob/66501d439e871b12b5afdebcf6454cd416322905/src/vs/workbench/contrib/chat/common/promptSyntax/utils/promptFilesLocator.ts) (`0695d911`, 2026-10-13) ·
  [promptsServiceImpl.ts](https://github.com/microsoft/vscode/blob/66501d439e871b12b5afdebcf6454cd416322905/src/vs/workbench/contrib/chat/common/promptSyntax/service/promptsServiceImpl.ts) (`d42ecd20`, 2026-11-03) ·
  [computeAutomaticInstructions.ts](https://github.com/microsoft/vscode/blob/66501d439e871b12b5afdebcf6454cd416322905/src/vs/workbench/contrib/chat/common/promptSyntax/computeAutomaticInstructions.ts) (`f7c83828`, 2026-10-01)
- GitHub Docs:
  [Copilot CLI › custom instructions](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions) ·
  [about-agent-skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
- Codex (developers.openai.com/codex/… leitet auf learn.chatgpt.com um):
  [agents-md](https://learn.chatgpt.com/docs/agent-configuration/agents-md) ·
  [build-skills](https://learn.chatgpt.com/docs/build-skills) ·
  [rules](https://learn.chatgpt.com/docs/agent-configuration/rules)
- Antigravity:
  [rules](https://antigravity.google/docs/rules/) ·
  [skills](https://antigravity.google/docs/skills) ·
  [hooks](https://antigravity.google/docs/hooks)
- Hooks (Abschnitt 8):
  [Claude Code hooks](https://code.claude.com/docs/en/hooks) ·
  [Codex hooks](https://learn.chatgpt.com/docs/hooks) ·
  [VS Code hooks](https://code.visualstudio.com/docs/copilot/customization/hooks) ·
  [GitHub Copilot about-hooks](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-hooks)
