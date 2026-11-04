---
# Copilot liest applyTo, Claude Code liest paths – beide gelten nur für Python-Dateien.
# Diese Datei ist zugleich .claude/rules/python.md (Symlink).
applyTo: "**/*.py"
paths:
  - "**/*.py"
---

# Python-Regeln

Allgemeine Projektregeln (uv, Befehle, Struktur) stehen in `AGENTS.md`.
Hier nur, was speziell beim Schreiben von Python-Code gilt.

## Code-Stil

- Type Hints für alle Funktionsparameter und Rückgabewerte
- Formatierung und Imports übernimmt Ruff – keinen Stil von Hand „korrigieren“

## Struktur

- Keine Logik auf Modulebene; Einstiegspunkt ist `main()`

## Tests

- Tests in `tests/`, Dateien heißen `test_<modul>.py`
- Für neue Funktionen immer auch einen Test schreiben
