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

## CI

Bei Code-Änderungen prüft GitHub automatisch Linting und Tests.
Details und Anleitung zum Anpassen: [.github/workflows/README.md](.github/workflows/README.md)
