# GenAi x SysEng

<!-- TODO: Kurze Beschreibung – worum geht es in diesem Projekt? -->

## Einrichtung & Onboarding

Neu erstellt? Die einmalige Einrichtung steht in [EINRICHTUNG.md](EINRICHTUNG.md) –
nach dem Klonen und Öffnen in VS Code übernimmt dein KI-Agent (z. B. GitHub Copilot)
den System-Check, merkt `EINRICHTUNG.md` zum Löschen vor und weist dich im Anschluss
darauf hin, mit **`/onboarding`** (oder *„Starte das Onboarding“*) die geführte
5-Stationen-Tour (`.agents/skills/onboarding/SKILL.md`) durch das Repository zu starten.
Fehlt `EINRICHTUNG.md` im Repository, ist die Einrichtung bereits abgeschlossen.

## Anweisungen für KI-Agenten

Alle Regeln, Konventionen und die zentrale Konfiguration für die vier genutzten
KI-Harnesses (Claude Code, GitHub Copilot, OpenAI Codex, Google Antigravity) stehen
in [AGENTS.md](AGENTS.md).

Anweisungen, Regeln (`.agents/rules/`) und Skills (`.agents/skills/`) werden
ausschließlich dort gepflegt – siehe Abschnitt „KI-Konfiguration“ in [AGENTS.md](AGENTS.md)
sowie die Übersicht zum Zusammenspiel der Harnesses unter
[docs/architecture/ki-harnesses.md](docs/architecture/ki-harnesses.md).

## Projektstruktur

| Ordner                | Inhalt                          |
|-----------------------|---------------------------------|
| `src/genai_x_syseng/` | Quellcode                       |
| `tests/`              | Tests (pytest)                  |
| [`docs/requirements/`](docs/requirements/) | Anforderungen und Akzeptanzkriterien |
| [`docs/architecture/`](docs/architecture/) | Architektur, Schnittstellen und Entscheidungen |
| [`docs/reference/`](docs/reference/) | Quellen und Recherche |

Die dateispezifischen Leitplanken stehen in [`docs/requirements/context.md`](docs/requirements/context.md),
[`docs/architecture/context.md`](docs/architecture/context.md) und
[`docs/reference/context.md`](docs/reference/context.md). Sie sichern die Traceability
(Nachverfolgbarkeit) von Anforderungen über Architektur bis zu Code und Tests.

## Konfiguration

| Datei / Ordner     | Zweck                                                              |
|--------------------|--------------------------------------------------------------------|
| `pyproject.toml`   | Projekt-Konfiguration: Abhängigkeiten, Ruff- und pytest-Einstellungen |
| `uv.lock`          | Exakte Paketversionen – wird von uv gepflegt, nicht von Hand bearbeiten |
| `.python-version`  | Python-Version des Projekts (3.12)                                 |
| `.agents/`         | Zentrale Regeln (`rules/`) und Skills (`skills/`) für alle KI-Agenten |
| `.claude/`         | Claude Code: Symlinks auf `.agents/rules` und `.agents/skills` sowie lokale Einstellungen |
| `.github/`         | GitHub: CI, Issue- und PR-Vorlagen, Symlink `copilot-instructions.md` |
| `.vscode/`         | VS-Code-Einstellungen und empfohlene Erweiterungen                 |
| `.gitignore`       | Was Git ignoriert (`.venv`, Caches, `.env` …)                      |
| `.gitattributes`   | Zeilenenden: LF, Windows-Skripte CRLF                              |

## Git-Workflow

`main` wird nie direkt geändert – jede Änderung läuft über einen Branch und einen
Pull Request.

1. **Arbeiten:** Alle Änderungen einer Session sammeln – nicht für jede Kleinigkeit
   einen eigenen Branch oder PR. Agenten committen, pushen und öffnen PRs erst, wenn
   man es ihnen sagt (siehe `AGENTS.md`).
2. **Branch anlegen:** spätestens vor dem ersten Commit mit
   `git switch -c <kurzer-name>` (z. B. `docs-unterordner`). Noch nicht committete
   Änderungen nimmt der neue Branch mit.
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
