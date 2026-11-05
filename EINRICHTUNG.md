# Einrichtung des eigenen Repositories

Einmalige Anleitung für den Start mit diesem Projekt.
Die Einrichtung ist zweigeteilt: **Teil 1** machst du kurz selbst (Repo anlegen, klonen,
in VS Code öffnen), **Teil 2** übernimmt dein KI-Agent (z. B. GitHub Copilot) für dich.
Nach erfolgreicher Prüfung löscht der Agent diese Datei (`git rm EINRICHTUNG.md`),
sodass sie auch auf GitHub aus deinem Repository verschwindet.

---

## Teil 1: Schnellstart für Menschen (vor dem ersten Chat)

### 1. Voraussetzungen installieren (falls noch nicht vorhanden)

- [VS Code](https://code.visualstudio.com/) (mit der Erweiterung **GitHub Copilot Chat**) oder [Google Antigravity](https://antigravity.google/)
- [Git](https://git-scm.com/)
- [uv](https://docs.astral.sh/uv/getting-started/installation/) – verwaltet Python 3.12 und alle Pakete automatisch (Python muss **nicht** separat installiert werden)
- [GitHub CLI (`gh`)](https://cli.github.com/) – damit die Agenten Issues und Pull Requests lesen und anlegen können

### 2. Windows: Entwicklermodus einschalten

`.github/copilot-instructions.md`, `.claude/rules` und `.claude/skills` sind Symlinks.
Damit Git sie unter Windows ohne Administratorrechte als echte Symlinks anlegen kann:

**Einstellungen → System → Für Entwickler → Entwicklermodus** einschalten.
*(Unter macOS/Linux nicht nötig.)*

### 3. Eigenes Repository über „Use this template“ anlegen & klonen

1. Öffne das Kurs-Vorlagen-Repository auf GitHub und klicke oben rechts auf den grünen
   Button **„Use this template“ → „Create a new repository“**
   *(bitte **nicht** direkt das Vorlagen-Repo klonen).*
2. Vergib einen Namen für dein Repository und erstelle es unter deinem GitHub-Account.
3. Klone **dein eigenes** Repository mit aktivierten Symlinks:

   ```bash
   git clone -c core.symlinks=true <deine-eigene-repo-url>
   ```

   *(Falls du bereits ohne `-c core.symlinks=true` oder direkt über den VS-Code-Button
   geklont hast: kein Problem – der Agent repariert die Symlinks gleich automatisch.)*

### 4. In VS Code öffnen & an den Agenten übergeben

1. Öffne den geklonten Ordner in VS Code (`Datei → Ordner öffnen…`).
2. Installiere die empfohlenen Erweiterungen: `Strg+Shift+X` → `@recommended` eingeben.
3. Öffne den KI-Chat (z. B. **GitHub Copilot Chat** im **Agent-Modus**) und schicke
   folgende Nachricht:

   > **`Prüfe und erledige bitte meine Einrichtung nach EINRICHTUNG.md.`**

---

## Teil 2: Automatischer Check, Reparatur & Abschluss (für den KI-Agenten)

> **Anweisung für den KI-Agenten:** Beginne nicht bei Teil 1, da das Repository bereits
> geklont und im Editor geöffnet ist. Führe direkt die 5 Diagnose-Checks (A–E) aus,
> zeige eine übersichtliche Status-Tabelle (✅/❌), repariere Probleme soweit möglich
> selbstständig und entferne `EINRICHTUNG.md` erst, wenn alle 5 Punkte grün sind.

### Schritt A: Eigenes GitHub-Remote prüfen

```bash
git remote get-url origin
```

- **Erwartet:** Die URL zeigt auf das **eigene** GitHub-Repository des/der Studierenden
  und **nicht** auf das Vorlagen-Repository `AIDED4SYS/GenAi_x_SysEng`.
- **Reparatur bei Fehler:**
  - Zeigt `origin` noch auf `AIDED4SYS/GenAi_x_SysEng`, wurde das Vorlagen-Repo direkt
    geklont. Weise den Menschen darauf hin, auf GitHub über **„Use this template“** ein
    eigenes Repo zu erstellen, und biege anschließend die Remote-URL um:
    `git remote set-url origin <eigene-repo-url>`.
  - Wurde statt des Templates ein **Fork** angelegt, aktiviere die in Forks standardmäßig
    ausgeschalteten Issues und setze das eigene Repo als Standard für `gh`:
    ```bash
    gh repo edit --enable-issues
    gh repo set-default origin
    ```

### Schritt B: Python-Umgebung & Tests prüfen (`uv`)

```bash
uv run pytest
```

- **Erwartet:** `uv` ist verfügbar, `.venv` wird automatisch genutzt/erstellt und alle
  Tests laufen grün durch.
- **Reparatur bei Fehler:**
  - Ist `uv` installiert, aber die Umgebung fehlt oder ist unvollständig: führe `uv sync`
    und danach erneut `uv run pytest` aus.
  - Wird `uv` nicht gefunden: nenne den Installationsbefehl
    (`winget install --id=Astral-sh.Uv -e` unter Windows bzw.
    `curl -LsSf https://astral.sh/uv/install.sh | sh` unter macOS/Linux) und weise darauf
    hin, dass VS Code / das Terminal danach einmal neu gestartet werden muss.

### Schritt C & D: Git-Symlinks prüfen

Prüfe sowohl die Git-Einstellung als auch die drei Symlinks im Arbeitsbaum:

```bash
git config core.symlinks
```

Unter Windows (PowerShell):

```powershell
(Get-Item .github\copilot-instructions.md).LinkType   # Erwartet: SymbolicLink
(Get-Item .claude\rules).LinkType                     # Erwartet: SymbolicLink
(Get-Item .claude\skills).LinkType                    # Erwartet: SymbolicLink
```

*(Unter macOS/Linux: `test -L .github/copilot-instructions.md && test -L .claude/rules && test -L .claude/skills`)*

- **Erwartet:** `core.symlinks` ist `true` und alle drei Pfade sind echte `SymbolicLink`s
  (Hinweis: `.claude` selbst ist **kein** Symlink, sondern ein normaler Ordner).
- **Reparatur bei Fehler** (wenn ohne `-c core.symlinks=true` geklont wurde und die
  drei Pfade nur einfache Textdateien sind):
  1. Unter Windows muss der **Entwicklermodus** aktiv sein (siehe Teil 1, Punkt 2).
  2. Setze `core.symlinks`, lösche die drei Textdateien und checke sie über Git neu aus
     *(in Windows PowerShell niemals `New-Item -ItemType SymbolicLink` verwenden, da dies
     trotz Entwicklermodus Administratorrechte verlangt)*:

     ```powershell
     git config core.symlinks true
     Remove-Item -Force .github\copilot-instructions.md, .claude\rules, .claude\skills
     git checkout -- .github/copilot-instructions.md .claude/rules .claude/skills
     ```

     *(In Bash/Zsh: `git config core.symlinks true && rm .github/copilot-instructions.md .claude/rules .claude/skills && git checkout -- .github/copilot-instructions.md .claude/rules .claude/skills`)*
  3. Prüfe anschließend erneut den `LinkType` aller drei Pfade. Bleibt er leer, ist der
     Windows-Entwicklermodus noch nicht eingeschaltet.

### Schritt E: GitHub CLI (`gh`) prüfen

```bash
gh auth status
```

- **Erwartet:** Bei `github.com` angemeldet.
  *(Hinweis für Agenten mit Sandbox wie Antigravity oder Codex: `gh auth status`
  kontaktiert `api.github.com` – bei Sandbox-Netzwerkfehlern außerhalb der Sandbox
  ausführen.)*
- **Reparatur bei Fehler:**
  - Ist `gh` nicht installiert: `winget install --id GitHub.cli` (Windows) bzw.
    `brew install gh` (macOS), danach Terminal/VS Code neu starten.
  - Ist `gh` noch nicht angemeldet: den Menschen bitten, im Terminal `gh auth login`
    auszuführen (GitHub.com → HTTPS → Login with a web browser), und danach erneut prüfen.

### Schritt F: Abschluss – `EINRICHTUNG.md` löschen

Sobald **alle 5 Prüfungen (A–E) grün** sind (und `origin` **nicht** auf
`AIDED4SYS/GenAi_x_SysEng` zeigt):

1. Entferne `EINRICHTUNG.md` regulär über Git, damit die Datei auch auf GitHub aus dem
   Studierenden-Repository verschwindet:

   ```bash
   git rm EINRICHTUNG.md
   ```

   *(Hinweis für Agenten mit Sandbox: Falls `.git/index.lock` durch die Sandbox
   blockiert wird, Befehl außerhalb der Sandbox ausführen.)*
2. Weise den Menschen darauf hin, dass die Löschung (zusammen mit dem Ausfüllen des
   eigenen Projekt-Steckbriefs unter `## Projekt` in `AGENTS.md`) auf einem Branch
   committet und per Pull Request gemergt werden kann (siehe Git-Workflow in `README.md`).

