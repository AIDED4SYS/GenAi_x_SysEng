# AGENTS.md

<!-- Ersetze alle TODO-Stellen durch eigene Angaben. Nicht benötigte Abschnitte einfach löschen. -->

## Projekt

- **Zweck:** <!-- TODO: Worum geht es in diesem Repo? -->
- **Kontext:** <!-- TODO: z. B. HM Master, 3. Semester, Kurs XY -->
- **Zielgruppe:** <!-- TODO: Wer nutzt die Ergebnisse? -->

## Rolle des Agenten

<!-- TODO: z. B. "Tutor, der Konzepte erklärt, statt fertige Lösungen zu liefern" -->

## Konventionen

- Antworten, Kommentare und Docstrings auf Deutsch.
- **Dateibenennung:** <!-- TODO: z. B. 01_thema_name.md -->

## Regeln

- Python nur über uv: Befehle mit `uv run …`, Pakete mit `uv add` – nie `pip install`.
- Nach Code-Änderungen `uv run pytest` und `uv run ruff check .` ausführen.
- <!-- TODO: z. B. keine Dateien ohne Rückfrage löschen -->

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
