# AGENTS.md

> Anweisungen für KI-Agenten (z. B. Claude Code, Copilot, Codex), die in diesem Ordner arbeiten.
> Ersetze alle `<!-- TODO -->`-Stellen durch eigene Angaben. Nicht benötigte Abschnitte einfach löschen.

---

## 1. Projektüberblick

- **Name:** <!-- TODO: z. B. KI-Tutorium -->
- **Zweck:** <!-- TODO: Worum geht es in diesem Ordner? -->
- **Kontext:** <!-- TODO: z. B. HM Master, 3. Semester, Kurs XY -->
- **Zielgruppe:** <!-- TODO: Wer nutzt die Ergebnisse? -->

## 2. Rolle des Agenten

<!-- TODO: Welche Rolle soll der Agent einnehmen? z. B. "Tutor, der Konzepte erklärt, statt fertige Lösungen zu liefern" -->

## 3. Arbeitsumgebung

- **Editor:** Visual Studio Code (ausschließlich)
- **Betriebssystem:** Windows 11
- **Sprache(n) / Tools:** Python 3.12 (verwaltet über **uv**), pytest, Ruff
- **Wichtige VS-Code-Erweiterungen:** Python, Pylance, Python Debugger, Ruff

### Python-Setup

- Python wird **ausschließlich über uv** verwaltet. Es gibt kein `py`, kein globales `pip install`.
- Paketstruktur: Code in `src/ki_tutorium/`, Tests in `tests/`, Doku in `docs/`.
- Abhängigkeiten stehen in `pyproject.toml`, exakte Versionen in `uv.lock` (nicht von Hand bearbeiten).

## 5. Befehle

| Zweck                     | Befehl                       |
|---------------------------|------------------------------|
| Setup / Umgebung syncen   | `uv sync`                    |
| Ausführen                 | `uv run ki-tutorium`         |
| Skript ausführen          | `uv run python <datei>.py`   |
| Testen                    | `uv run pytest`              |
| Linten                    | `uv run ruff check .`        |
| Formatieren               | `uv run ruff format .`       |
| Paket hinzufügen          | `uv add <paket>`             |
| Dev-Tool hinzufügen       | `uv add --dev <paket>`       |

## 6. Konventionen & Stil

- **Antwortsprache:**  Deutsch
- **Code-Kommentare:** Deutsch
- **Dateibenennung:** <!-- TODO: z. B. 01_thema_name.md -->
- **Formatierung:** Ruff (Einstellungen in `pyproject.toml`, Zeilenlänge 88)

## 7. Regeln (Do / Don't)

**Do:**
- Python-Befehle immer mit `uv run …` ausführen
- Nach Code-Änderungen `uv run pytest` und `uv run ruff check .` ausführen
- <!-- TODO -->

**Don't:**
- Kein `pip install` und kein `python -m pip` – stattdessen `uv add`
- <!-- TODO: z. B. keine Dateien ohne Rückfrage löschen -->

## 8. Git-Workflow

- **Branches:** <!-- TODO -->
- **Commit-Nachrichten:** <!-- TODO: z. B. Deutsch, kurz, im Imperativ -->
- **Commits nur nach Rückfrage:** <!-- TODO: ja / nein -->

## 9. Sonstige Hinweise

### Einrichtung (`EINRICHTUNG.md`)

- Existiert `EINRICHTUNG.md` im Arbeitsordner, ist die Einrichtung **noch nicht abgeschlossen**.
  Den Nutzer dann zuerst durch die Schritte dort führen und jeden Schritt prüfen.
- Erst wenn alle Prüfungen aus Schritt 6 erfolgreich sind, die Datei wie in Schritt 7
  beschrieben **lokal** löschen (`git update-index --skip-worktree` + `rm`).
- Die Löschung von `EINRICHTUNG.md` **niemals committen** – die Datei muss im Repository bleiben.

<!-- TODO: alles Weitere, was der Agent wissen sollte -->
