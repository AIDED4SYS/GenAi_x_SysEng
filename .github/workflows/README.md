# Workflows (GitHub Actions)

In diesem Ordner liegen die automatischen Prüfungen, die auf GitHub laufen.
GitHub behandelt nur `.yml`/`.yaml`-Dateien als Workflows – diese README wird ignoriert.

| Datei    | Zweck                                  |
|----------|----------------------------------------|
| `ci.yml` | Linting (Ruff) und Tests (pytest)      |

---

## CI – was sie macht

Die CI richtet auf einem GitHub-Server das Projekt mit uv ein und führt aus:

1. `uv sync --locked` – Abhängigkeiten installieren (schlägt fehl, wenn `uv.lock` nicht zur `pyproject.toml` passt)
2. `uv run ruff check .` – Linting
3. `uv run ruff format --check .` – Formatierung prüfen
4. `uv run pytest` – Tests

Am Commit bzw. Pull Request erscheint danach ✅ oder ❌.

## Wann sie startet

- bei einem **Push auf `main` auf GitHub** – lokale Commits lösen nichts aus, erst `git push`
- bei jedem **Pull Request**
- jeweils **nur**, wenn sich eine dieser Dateien ändert:
  `*.py`, `pyproject.toml`, `uv.lock`, `.python-version`, `.github/workflows/ci.yml`

Reine Änderungen an Markdown, `docs/` oder `.vscode/` lösen nichts aus.

### Ablauf mit Branches

1. **Arbeiten auf einem Branch** (z. B. `neue-funktion`) und pushen
   → keine CI, da `push` auf `main` beschränkt ist.
2. **Pull Request öffnen** (`neue-funktion` → `main`)
   → CI läuft (`pull_request`).
   Prüft: Ist die Änderung in Ordnung, **bevor** sie in `main` kommt?
3. **Pull Request in `main` mergen**
   → CI läuft (`push` auf `main`).
   Prüft: Funktioniert `main` **nach** dem Zusammenführen?

### Warum beide Prüfungen (2 und 3)?

Zwischen Schritt 2 und Schritt 3 kann Zeit vergehen. Wird in dieser Zeit eine andere
Änderung in `main` übernommen, können Konflikte entstehen, die bei Schritt 2 noch gar
nicht vorhanden waren. Schritt 3 prüft deshalb den tatsächlichen Endstand.

Wer direkt auf `main` pusht (ohne Pull Request), durchläuft nur Schritt 3 –
die CI läuft dann genau einmal.

---

## `ci.yml` anpassen

### Dateien, bei denen die CI startet

Die Liste steht einmal unter `push` und wird mit `*code_dateien` für `pull_request`
wiederverwendet. Änderungen also nur an dieser einen Stelle:

```yaml
    paths: &code_dateien
      - "**.py"
      - "pyproject.toml"
      - "data/**"          # Beispiel: neu hinzugefügt
```

Soll die CI bei **jeder** Änderung laufen, beide `paths`-Zeilen samt Liste entfernen.

### Weitere Branches

```yaml
  push:
    branches: [main, develop]
```

### Prüfschritt ergänzen

Neuen Schritt unter `steps:` anfügen, z. B. Typprüfung mit mypy
(vorher lokal `uv add --dev mypy`):

```yaml
      - name: Typprüfung
        run: uv run mypy src
```

### CI manuell starten

Unter `on:` ergänzen – danach erscheint auf GitHub unter **Actions → CI** ein
Button **Run workflow**:

```yaml
on:
  workflow_dispatch:
  push:
    ...
```

### CI vorübergehend abschalten

Auf GitHub: **Actions → CI → ⋯ → Disable workflow**.
Die Datei bleibt erhalten, die CI startet aber nicht mehr, bis sie wieder aktiviert wird.

### Nach Änderungen prüfen

Die Datei lässt sich lokal gegen das offizielle GitHub-Schema prüfen:

```bash
uvx --from check-jsonschema check-jsonschema --builtin-schema vendor.github-workflows .github/workflows/ci.yml
```
