# docs/architecture – Architektur

## 1. Zweck

Dieser Ordner enthält die Ergebnisse der **Architecture Definition** nach
ISO/IEC/IEEE 15288. Die Dokumente beschreiben Systemkontext, Elemente,
Verantwortlichkeiten und Schnittstellen sowie die begründeten Entscheidungen, die
aus den Anforderungen abgeleitet wurden.

## 2. Input

- Anforderungen aus [`docs/requirements/`](../requirements/) mit ihren `REQ-IDs`
- Stakeholder-Bedürfnisse, Systemgrenzen und Nutzungsszenarien
- Randbedingungen und belastbare Quellen aus [`docs/reference/`](../reference/)
- Ergebnisse von Verifikation, Validierung und Änderungsanträgen

Die Architektur darf keine Anforderungen oder Quelleninhalte erfinden. Unklare,
widersprüchliche oder fehlende Vorgaben werden als offene Frage beziehungsweise
Annahme gekennzeichnet und zur Klärung zurückgegeben.

## 3. Struktur und Qualitätskriterien

Architekturdokumente enthalten – soweit für die Entscheidung relevant – mindestens:

1. **Kontext und Systemgrenze:** externe Stakeholder, Nachbarsysteme und Datenflüsse
2. **Elemente und Verantwortlichkeiten:** klare Zerlegung mit einer Verantwortung je
   Element
3. **Schnittstellen:** bereitgestellte und benötigte Dienste, Daten, Protokolle und
   relevante Qualitätsmerkmale
4. **Anforderungsbezug:** referenzierte `REQ-IDs` für jede wesentliche Entscheidung
5. **Alternativen und Entscheidung:** betrachtete Optionen, Kriterien, Trade-offs und
   begründete Auswahl
6. **Verifikation und Validierung:** Nachweis, wie Anforderungen und Architektur
   geprüft werden

Für einzelne Architekturentscheidungen kann ein leichtgewichtiges ADR-Format
verwendet werden:

```markdown
## ADR-001 – Kurzer Entscheidungstitel

**Status:** vorgeschlagen | akzeptiert | abgelöst
**Anforderungen:** REQ-001, REQ-002
**Kontext:** ...
**Alternativen:** ...
**Entscheidung:** ...
**Begründung und Trade-offs:** ...
**Verifikation:** ...
```

Diagramme, zum Beispiel Mermaid, ergänzen die textliche Beschreibung und ersetzen sie
nicht. Struktur und Schnittstellen werden vor Detail-Code festgelegt.

## 4. Process

1. Systemkontext, Stakeholder und externe Schnittstellen abgrenzen.
2. Anforderungen und Qualitätsmerkmale auf Architekturauswirkungen analysieren.
3. Elemente und Schnittstellen entwerfen, bevor Implementierungsdetails festgelegt
   werden.
4. Mindestens zwei sinnvolle Alternativen vergleichen, wenn eine Entscheidung
   wesentliche Auswirkungen hat.
5. Entscheidung, Annahmen, offene Punkte und Trade-offs dokumentieren.
6. Architektur gegen Anforderungen sowie Risiken und Änderungsfolgen prüfen.

## 5. Traceability und Output

Jede wesentliche Architekturaussage verweist auf mindestens eine `REQ-ID` oder ist als
Annahme beziehungsweise technische Randbedingung gekennzeichnet. Die Traceability
führt von der Anforderung über Architekturentscheidung und Schnittstelle zu Code und
Test:

`REQ-ID → Architektur → Schnittstelle/Element → Code → Test`

Ändert sich eine Anforderung, werden betroffene Architekturentscheidungen,
Schnittstellen, Implementierungen und Tests auf Auswirkung geprüft. Eine
Architekturentscheidung gilt erst als abgeschlossen, wenn ihre Anforderungen
verifiziert und die Eignung für den vorgesehenen Nutzungskontext validiert wurde.
Quellen aus `docs/reference/` werden mit Dateiname und exakter Fundstelle angegeben.
