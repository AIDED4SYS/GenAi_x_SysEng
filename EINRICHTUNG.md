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
2. Vergib einen Namen für dein Repository, stelle die Sichtbarkeit auf **Private**
   *(GitHub wählt im Formular oft **Public** vor – die Sichtbarkeit wird **nicht** aus
   der Vorlage übernommen)* und erstelle es unter deinem GitHub-Account.
3. Kopiere die URL **deines eigenen** Repositorys: grüner Button **„Code“ → Reiter
   „HTTPS“** → URL kopieren (z. B. `https://github.com/max-muster/mein-projekt.git`).
4. Öffne ein Terminal (z. B. PowerShell) und wechsle **zuerst** in den Ordner, in dem
   dein Repository liegen soll (den Ordner bei Bedarf vorher im Explorer anlegen) –
   `git clone` legt den Repo-Ordner ohne Rückfrage dort an, wo das Terminal gerade
   steht:

   ```bash
   cd C:\Users\max\Studium
   ```

5. Klone **dann** dein Repository mit aktivierten Symlinks. Setze deine URL **ohne
   spitze Klammern** ein:

   ```bash
   git clone -c core.symlinks=true https://github.com/max-muster/mein-projekt.git
   ```

   *(Falls du bereits ohne `-c core.symlinks=true` oder direkt über den VS-Code-Button
   geklont hast: kein Problem – der Agent repariert die Symlinks gleich automatisch.
   Liegt der Ordner am falschen Ort, kannst du ihn einfach im Explorer verschieben.)*

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

### Den Menschen auf dem Laufenden halten

Arbeite die Schritte nacheinander ab und melde dich bei **jedem** Schritt kurz im Chat:

- **Vor dem Schritt:** wo du stehst und was du prüfst, z. B.
  `▶ Schritt B von A–F: Python-Umgebung & Tests prüfen …`
- **Nach dem Schritt:** das Ergebnis in einem Satz, z. B.
  `✅ Schritt B erfolgreich: uv ist verfügbar, alle Tests sind grün.` bzw.
  `❌ Schritt B fehlgeschlagen: uv wurde nicht gefunden.` – bei einer Reparatur
  zusätzlich, was du getan hast und ob die erneute Prüfung grün ist.
- **Muss Software auf dem Rechner installiert werden** (z. B. `uv` in Schritt B oder
  `gh` in Schritt E): **stoppe**, nenne den Installationsbefehl und frage den Menschen,
  ob er selbst installieren möchte oder du es tun sollst. Fahre erst nach seiner Antwort
  fort. Muss VS Code danach neu gestartet werden, bitte den Menschen, anschließend die
  Startnachricht aus Teil 1, Punkt 4 erneut zu schicken.
- **Am Ende:** die Status-Tabelle aller Schritte (✅/❌).

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
  - Wird `uv` nicht gefunden: **stoppen und den Menschen fragen** (siehe oben). Nenne
    den Installationsbefehl (`winget install --id=Astral-sh.Uv -e` unter Windows bzw.
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

### Schritt E: GitHub CLI (`gh`) & Sichtbarkeit des Repos prüfen

```bash
gh auth status
gh repo view --json visibility
```

- **Erwartet:** Bei `github.com` angemeldet und das eigene Repo ist `PRIVATE`
  (die Sichtbarkeit lässt sich erst prüfen, wenn `gh` angemeldet ist).
  *(Hinweis für Agenten mit Sandbox wie Antigravity oder Codex: `gh auth status`
  kontaktiert `api.github.com` – bei Sandbox-Netzwerkfehlern außerhalb der Sandbox
  ausführen.)*
- **Reparatur bei Fehler:**
  - Ist `gh` nicht installiert: **stoppen und den Menschen fragen** (siehe oben);
    Installationsbefehl `winget install --id GitHub.cli` (Windows) bzw.
    `brew install gh` (macOS), danach Terminal/VS Code neu starten.
  - Ist `gh` noch nicht angemeldet: den Menschen bitten, im Terminal `gh auth login`
    auszuführen (GitHub.com → HTTPS → Login with a web browser), und danach erneut prüfen.
  - Ist das Repo `PUBLIC`: den Menschen darauf hinweisen und fragen, ob du es auf
    privat stellen sollst. Bei Ja:
    ```bash
    gh repo edit --visibility private --accept-visibility-change-consequences
    ```
    *(Antwortet die API mit einem Fehler wie `HTTP 500`, alternativ
    `gh api -X PATCH repos/{owner}/{repo} -f visibility=private` verwenden.)*
    Danach erneut mit `gh repo view --json visibility` prüfen.

### Schritt F: Abschluss – `EINRICHTUNG.md` vormerken & ins Onboarding starten

Sobald **alle 5 Prüfungen (A–E) grün** sind (und `origin` **nicht** auf
`AIDED4SYS/GenAi_x_SysEng` zeigt):

1. Merke `EINRICHTUNG.md` regulär über Git zum Löschen vor, damit wir diese Änderung am
   Ende des anschließenden Onboardings gemeinsam als ersten Übungs-Pull-Request
   einreichen können:

   ```bash
   git rm EINRICHTUNG.md
   ```

   *(Hinweis für Agenten mit Sandbox: Falls `.git/index.lock` durch die Sandbox
   blockiert wird, Befehl außerhalb der Sandbox ausführen.)*
2. **Noch keinen Branch oder Pull Request erstellen und noch NICHT mit Requirements,
   Architektur oder dem Projekt-Steckbrief beginnen!** Die Studierenden arbeiten zum
   ersten Mal mit einem KI-Coding-Agenten und lernen Branch, Commit und Pull Request
   erst in Station 4 und 5 des Onboardings kennen.
3. Melde den Abschluss der Einrichtung:
   `✅ Schritt F erfolgreich: Einrichtung abgeschlossen (Löschung von EINRICHTUNG.md ist für unseren ersten gemeinsamen Pull Request vorgemerkt).`
4. **In die Rolle des geduldigen AI Coding Lehrers wechseln & auf den Onboarding-Skill hinweisen (Skill NICHT automatisch starten!):**
   - Nimm ab sofort die Rolle eines **geduldigen AI Coding Lehrers** ein (kurze Sätze, einfache Worte, keine Überforderung mit Requirements oder Architektur).
   - Starte den Onboarding-Skill **nicht** automatisch, sondern weise den Studierenden freundlich darauf hin, wie er ihn jetzt selbst auslöst:
     > *„Deine Einrichtung ist komplett! Bevor wir mit dem eigentlichen Projekt starten, führe ich dich in 5 kurzen Stationen (ca. 20 Minuten) wie an deinem ersten Arbeitstag durch dieses Repository. Dafür nutzen wir deinen ersten **Skill** (ein vorbereitetes Lernprogramm für mich als KI).*
     > *Tippe jetzt einfach **`/onboarding`** oder schreibe **„Starte das Onboarding"** in den Chat, dann legen wir los!"*
   - Warte danach ab, bis der Studierende das Onboarding anfordert.

