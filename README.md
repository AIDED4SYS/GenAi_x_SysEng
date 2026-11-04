# KI-Tutorium

<!-- TODO: Kurze Beschreibung – worum geht es in diesem Projekt? -->

## Einrichtung

Neu geklont? Die einmalige Einrichtung steht in [EINRICHTUNG.md](EINRICHTUNG.md).
Die Datei wird nach erfolgreicher Einrichtung lokal gelöscht – fehlt sie im
Arbeitsordner, ist die Einrichtung bereits erledigt.

## Anweisungen für KI-Agenten

Die Projektregeln für alle Agenten stehen in [AGENTS.md](AGENTS.md).

`.github/copilot-instructions.md` ist **keine eigene Datei**, sondern ein Symlink auf
`AGENTS.md` – GitHub Copilot liest damit automatisch dieselben Regeln.

- **Nur `AGENTS.md` bearbeiten.** Änderungen gelten sofort nach dem Speichern auch für Copilot.
- `.github/copilot-instructions.md` nicht löschen oder durch eine Kopie ersetzen,
  sonst laufen die beiden Dateien auseinander.
- Claude Code liest `AGENTS.md` direkt – eine eigene `CLAUDE.md` gibt es bewusst nicht.
- Zusätzliche Regeln nur für Python-Dateien:
  [.github/instructions/python.instructions.md](.github/instructions/python.instructions.md).
  `.claude/rules/python.md` ist ein Symlink darauf – Copilot und Claude Code nutzen
  dieselbe Datei.

## Projektstruktur

| Ordner              | Inhalt                          |
|---------------------|---------------------------------|
| `src/ki_tutorium/`  | Quellcode                       |
| `tests/`            | Tests (pytest)                  |
| `docs/`             | Dokumentation                   |

## Konfiguration

| Datei / Ordner     | Zweck                                                              |
|--------------------|--------------------------------------------------------------------|
| `pyproject.toml`   | Projekt-Konfiguration: Abhängigkeiten, Ruff- und pytest-Einstellungen |
| `uv.lock`          | Exakte Paketversionen – wird von uv gepflegt, nicht von Hand bearbeiten |
| `.python-version`  | Python-Version des Projekts (3.12)                                 |
| `.github/`         | GitHub: CI, Issue- und PR-Vorlagen, Copilot-Anweisungen            |
| `.claude/`         | Claude Code: Regeln (`rules/`) und Skills (`skills/`)              |
| `.vscode/`         | VS-Code-Einstellungen und empfohlene Erweiterungen                 |
| `.gitignore`       | Was Git ignoriert (`.venv`, Caches, `.env` …)                      |
| `.gitattributes`   | Zeilenenden: LF, Windows-Skripte CRLF                              |

## Git-Workflow

`main` wird nie direkt geändert – jede Änderung läuft über einen Branch und einen
Pull Request.

1. **Branch anlegen:** `git switch -c <kurzer-name>` (z. B. `docs-unterordner`)
2. **Arbeiten:** Alle Änderungen einer Session auf diesem einen Branch sammeln –
   nicht für jede Kleinigkeit einen eigenen Branch oder PR. Agenten committen erst,
   wenn man es ihnen sagt (siehe `AGENTS.md`).
3. **Abschließen:** committen, pushen, PR öffnen und auf GitHub mergen.
4. **Aufräumen:** GitHub löscht den Branch nach dem Merge automatisch
   („Automatically delete head branches“). Lokal bleibt er bestehen:

   ```bash
   git switch main
   git pull
   git branch -d <kurzer-name>
   ```

   `-d` löscht nur Branches, die bereits gemergt sind – es geht also nichts verloren.

Damit `git pull` auch die Verweise auf gelöschte GitHub-Branches (`origin/…`)
entfernt, einmalig einstellen:

```bash
git config --global fetch.prune true
```

## CI

Bei Code-Änderungen prüft GitHub automatisch Linting und Tests.
Details und Anleitung zum Anpassen: [.github/workflows/README.md](.github/workflows/README.md)
