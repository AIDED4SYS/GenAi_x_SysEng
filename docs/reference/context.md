# docs/reference – Stabiles Wissen

## 1. Zweck
Wissens- und Quellenbasis des Projekts (Vorlesungsskripte, Normen, Fachliteratur, Paper sowie `recherche/`), getrennt in Unterordnern nach Wissensart. Diese `context.md` steuert nach dem **IPO-Prinzip (Input – Process – Output)**, wie Mensch und Agent Quellen ablegen, ressourcenschonend durchsuchen und rückverfolgbar (*Traceability*) zitieren.

---

## 2. Input (Quellenablage durch den Menschen)
- **Hoheit über Primärquellen:** Neue Rohquellen legt ausschließlich der Mensch in den passenden Unterordnern ab.
- **Namenskonvention (vom Menschen definierbar):** `<wissensart>_<kurztitel-in-kebab-case>.<ext>` *(hier bei Bedarf eigenes Schema festlegen)*.
- **Lizenz- und Urheberrechtsschutz:** PDFs liegen nur lokal im Arbeitsverzeichnis und sind per `.gitignore` von der Versionsverwaltung ausgeschlossen.
- **Recherche-Ergebnisse (`recherche/`):** Ausarbeitungen aus KI-Recherchen werden getrennt unter `recherche/` abgelegt. Sie gelten **nicht** als belastbare Primärquelle, sondern nur als Suchhilfe: Aussagen daraus müssen über die dort genannte Originalquelle belegt oder explizit als *ungeprüft* gekennzeichnet werden.

> **Beispiel (Input):** Der Mensch legt eine heruntergeladene Datei `Skript_Kapitel2_final (1).pdf` in `docs/reference/vorlesung/` ab.

---

## 3. Process (Aufbereitung, Indexierung & gezieltes Nachschlagen)
Um das Kontextfenster des Agenten nicht durch das vollständige Einlesen großer PDF-Dokumente zu überlasten (*Context Pollution*) und unnötig hohen Token-Verbrauch zu vermeiden, arbeitet der Agent mehrstufig:

1. **Einmalige Benennung & Baseline-Schutz:** Neue, noch unreferenzierte Rohdateien benennt der Agent nach der unter *Input* definierten Konvention um. Sobald Belege im Projekt auf eine Datei verweisen, darf sie niemals inhaltlich geändert, umbenannt oder gelöscht werden.
2. **Index anlegen (bei umfangreichen Dokumenten):** Für lange PDFs legt der Agent eine schlanke Markdown-Indexdatei (`<dateiname>-index.md`) an, die Kapitelüberschriften, Schlüsselbegriffe und zugehörige Seitenbereiche auflistet, ohne die Originaldatei zu verändern.
3. **Gezielte Seitenabfrage statt Volltext-Suche:** Bei einer Fachfrage oder Entwicklungsaufgabe liest der Agent **nicht** das gesamte Dokument, sondern prüft zuerst den Index, identifiziert die relevante Stelle und liest anschließend gezielt **nur die benötigten 1–3 Seiten** in der Originaldatei nach.

> **Beispiel (Process):** Der Agent benennt die neue Datei in `vorlesung_syseng-grundlagen.pdf` um und erstellt `vorlesung_syseng-grundlagen-index.md`. Bei einer Frage zum *Architecture Definition Process* schlägt er im Index nach, findet *„Kapitel 4.3 (S. 42–45)"* und liest gezielt nur Seite 42–43 der PDF.

---

## 4. Output (Belegte Antworten & Offenlegung von Annahmen)
- **Aussagen mit Primärquelle:** Jedes Ergebnis im Chat oder in Engineering-Artefakten (`docs/requirements/`, `docs/architecture/`), das sich auf `docs/reference/` stützt, enthält den **Dateinamen** und die **exakte Fundstelle (PDF-Seite und/oder Abschnitt)**. Nur selbst in der Datei Gelesenes darf als Quelle ausgegeben werden.
- **Aussagen ohne Quelle (Offenlegungspflicht):** Fehlen passende Quellen (z. B. zu Projektbeginn) oder stammen Informationen aus dem allgemeinen Modelltraining bzw. aus `recherche/`, darf der Agent diese nutzen, muss sie aber transparent als **Annahme / Modellwissen / ungeprüft** kennzeichnen.

> **Beispiel (Output):** *„Die Stakeholder-Anforderungsdefinition dient als Eingabe für die Systemanforderungsanalyse (Quelle: `docs/reference/vorlesung/vorlesung_syseng-grundlagen.pdf`, S. 42, Abschnitt 4.3) – bzw. falls noch keine Quelle vorliegt: [Annahme / Modellwissen nach ISO/IEC/IEEE 15288]."*



