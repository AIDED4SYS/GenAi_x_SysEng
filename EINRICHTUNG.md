# Einrichtung nach dem Klonen

Einmalige Anleitung für alle, die dieses Repository von GitHub klonen.
**Nach erfolgreicher Einrichtung wird diese Datei lokal gelöscht (Schritt 7).**

Das Projekt ist für die Arbeit mit **VS Code** und **KI-Agenten** (Claude Code, Copilot)
ausgelegt. Die Agenten führen die Projekt-Befehle selbst aus – einzurichten ist nur
das Folgende.

## 1. Voraussetzungen

- [VS Code](https://code.visualstudio.com/)
- [Git](https://git-scm.com/)
- [uv](https://docs.astral.sh/uv/getting-started/installation/) – verwaltet Python und
  alle Pakete; Python muss **nicht** separat installiert werden

## 2. Windows: Entwicklermodus einschalten

`.github/copilot-instructions.md` und `.claude/rules/python.md` sind Symlinks. Damit Git
sie unter Windows korrekt anlegen kann:

**Einstellungen → System → Für Entwickler → Entwicklermodus** einschalten.

(Unter macOS/Linux nicht nötig.)

## 3. Repository klonen

```bash
git clone -c core.symlinks=true <repo-url>
```

Ohne `-c core.symlinks=true` wird der Symlink unter Windows zu einer einfachen
Textdatei, und Copilot sieht die Projektregeln nicht.

## 4. Umgebung einrichten

Im Projektordner:

```bash
uv sync
```

Installiert Python 3.12 (falls nötig) und alle Abhängigkeiten in `.venv/`.

## 5. In VS Code öffnen

- Ordner in VS Code öffnen
- Empfohlene Erweiterungen installieren: `Strg+Shift+X` → `@recommended`
- VS Code nutzt die `.venv` automatisch (siehe `.vscode/settings.json`)

## 6. Einrichtung prüfen

```bash
uv run pytest
git config core.symlinks
```

Erwartet: Tests bestanden, `core.symlinks` ist `true`.
Zusätzlich müssen beide Symlinks korrekt angelegt sein (Windows/PowerShell):

```powershell
(Get-Item .github\copilot-instructions.md).LinkType   # Erwartet: SymbolicLink
(Get-Item .claude\rules\python.md).LinkType           # Erwartet: SymbolicLink
```

## 7. Diese Datei lokal löschen

Die Datei bleibt im Repository für die nächsten Personen erhalten. Gelöscht wird sie
nur im eigenen Arbeitsordner – so, dass Git die Löschung ignoriert und sie nicht
versehentlich committet wird:

```bash
git update-index --skip-worktree EINRICHTUNG.md
rm EINRICHTUNG.md
```
