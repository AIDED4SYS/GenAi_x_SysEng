# Einrichtung nach dem Klonen

Einmalige Anleitung für alle, die dieses Repository von GitHub klonen.
**Nach erfolgreicher Einrichtung wird diese Datei lokal gelöscht (Schritt 7).**

Das Projekt ist für die Arbeit mit **VS Code** bzw. **Google Antigravity** und den vier
**KI-Harnesses** (Claude Code, GitHub Copilot, OpenAI Codex, Google Antigravity)
ausgelegt. Die Agenten führen die Projekt-Befehle selbst aus – einzurichten ist nur
das Folgende.

## 1. Voraussetzungen

- [VS Code](https://code.visualstudio.com/) (oder [Google Antigravity](https://antigravity.google/))
- [Git](https://git-scm.com/)
- [uv](https://docs.astral.sh/uv/getting-started/installation/) – verwaltet Python und
  alle Pakete; Python muss **nicht** separat installiert werden
- [GitHub CLI](https://cli.github.com/) (`gh`) – damit Agenten Issues und PRs lesen
  können. Nach der Installation einmalig `gh auth login`.

## 2. Windows: Entwicklermodus einschalten

`.github/copilot-instructions.md`, `.claude/rules` und `.claude/skills` sind Symlinks
auf `AGENTS.md` bzw. `.agents/rules` und `.agents/skills`. Damit Git sie unter Windows
korrekt anlegen kann:

**Einstellungen → System → Für Entwickler → Entwicklermodus** einschalten.

(Unter macOS/Linux nicht nötig.)

## 3. Repository klonen

```bash
git clone -c core.symlinks=true <repo-url>
```

Ohne `-c core.symlinks=true` werden die Symlinks unter Windows zu einfachen
Textdateien, und die KI-Agenten finden die verlinkten Regeln und Skills nicht.

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
gh auth status
```

Erwartet: Tests bestanden, `core.symlinks` ist `true`, `gh` ist angemeldet.
Zusätzlich müssen die drei Symlinks korrekt angelegt sein (Windows/PowerShell):

```powershell
(Get-Item .github\copilot-instructions.md).LinkType   # Erwartet: SymbolicLink
(Get-Item .claude\rules).LinkType                     # Erwartet: SymbolicLink
(Get-Item .claude\skills).LinkType                    # Erwartet: SymbolicLink
```

## 7. Diese Datei lokal löschen

Die Datei bleibt im Repository für die nächsten Personen erhalten. Gelöscht wird sie
nur im eigenen Arbeitsordner – so, dass Git die Löschung ignoriert und sie nicht
versehentlich committet wird:

```bash
git update-index --skip-worktree EINRICHTUNG.md
rm EINRICHTUNG.md
```

