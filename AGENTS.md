# AGENTS.md

<!-- Ersetze alle TODO-Stellen durch eigene Angaben. Nicht benötigte Abschnitte einfach löschen. -->

## Projekt

- **Zweck:** <!-- TODO: Worum geht es in diesem Repo? -->
- **Kontext:** <!-- TODO: z. B. HM Master, 3. Semester, Kurs XY -->
- **Zielgruppe:** <!-- TODO: Wer nutzt die Ergebnisse? -->

## Rolle des Agenten

<!-- TODO: z. B. "Tutor, der Konzepte erklärt, statt fertige Lösungen zu liefern" -->

## Karte: was der Ordnerbaum nicht zeigt

| Frage | Ort |
|---|---|
| Was ist gefordert? | `docs/requirements/` |
| Wie ist es aufgebaut, und warum so? | `docs/architecture/` |
| Was sagen Vorlesung, Normen, Literatur, Recherchen? | `docs/reference/` |
| Was ist geplant, offen oder schon erledigt? | GitHub Issues und PRs dieses Repos, auch geschlossene |
| Nicht gefunden? | Anforderungen, Entscheidungen und Quelleninhalte nicht erfinden – den Menschen fragen |

Liegt in einem Ordner eine `context.md`, gilt sie für diesen Ordner: lesen, bevor du
Inhalte daraus nutzt oder dort Dateien anlegst, änderst oder löschst.

## Konventionen

- Sprache: Antworten, Kommentare und Docstrings auf Deutsch.
- **Dateibenennung:** <!-- TODO: z. B. 01_thema_name.md -->

## Regeln

- Python nur über uv: Befehle mit `uv run …`, Pakete mit `uv add` – nie `pip install`.
- Nach Code-Änderungen `uv run pytest` und `uv run ruff check .` ausführen.
- Für Python-Dateien zusätzlich `.agents/rules/python.instructions.md` beachten.
- <!-- TODO: z. B. keine Dateien ohne Rückfrage löschen -->

## KI-Konfiguration (`.agents/`)

- **Single Source of Truth:** Projektanweisungen stehen in `AGENTS.md`, dateispezifische
  Regeln in `.agents/rules/` und Skills in `.agents/skills/`. Nur dort Dateien anlegen
  oder ändern (Aufbau und Zusammenspiel: `docs/architecture/ki-harnesses.md`).
- **Keine `CLAUDE.md` / `GEMINI.md`:** Weder `CLAUDE.md`, `CLAUDE.local.md` noch `GEMINI.md`
  anlegen (verdrängt in Claude Code sonst `AGENTS.md`).
- **Symlinks nicht ersetzen:** `.github/copilot-instructions.md` (→ `../AGENTS.md`),
  `.claude/rules` (→ `../.agents/rules`) und `.claude/skills` (→ `../.agents/skills`)
  sind Symlinks. `.claude/` selbst muss ein echter Ordner bleiben (für
  `settings.local.json` und Worktrees).
- **Regel-Format:** Regeldateien flach in `.agents/rules/<name>.instructions.md` ablegen
  (Endung `.instructions.md` ist Pflicht für VS Code/Copilot; Frontmatter immer mit
  `applyTo`, `paths`, `trigger` und bei Dateimustern `globs`) und in `AGENTS.md` unter
  `## Regeln` kurz darauf verweisen, damit auch Codex die Regel kennt.

## Git

- Nie direkt auf `main` committen oder pushen – immer Branch + Pull Request.
- Committen, pushen und PRs öffnen nur auf Aufforderung – nicht nach jeder Änderung
  danach fragen.
- Alle Änderungen einer Session gehören auf denselben Branch und in einen PR.

## Einrichtung (`EINRICHTUNG.md`)

- Existiert `EINRICHTUNG.md`, ist die Einrichtung **nicht abgeschlossen**: den Nutzer
  zuerst durch die Schritte dort führen und jeden Schritt prüfen.
- Erst wenn alle Prüfungen (Schritt 6) erfolgreich sind, die Datei wie in Schritt 7
  **lokal** löschen.
- Die Löschung **niemals committen** – die Datei muss im Repository bleiben.

## Sonstiges

<!-- TODO: alles Weitere, was der Agent wissen sollte -->
