# docs/requirements – Anforderungen

## 1. Zweck

Dieser Ordner enthält die abgestimmten Ergebnisse der **Stakeholder Needs and
Requirements Definition** und der **Requirements Analysis** nach ISO/IEC/IEEE 15288.
Anforderungen beschreiben das benötigte Verhalten und die Randbedingungen des Systems
aus Sicht der Stakeholder. Sie schreiben keine technische Lösung vor, sofern dies nicht
selbst eine begründete Randbedingung ist.

## 2. Input

- Stakeholder-Bedürfnisse, Nutzungsszenarien und Systemgrenzen
- geltende Randbedingungen, Normen und rechtliche Vorgaben
- belastbare Quellen aus [`docs/reference/`](../reference/)
- Ergebnisse aus Verifikation und Validierung sowie Änderungsanträge

Fehlen Informationen oder ist eine Stakeholder-Aussage mehrdeutig, fragt der Mensch
oder Agent nach. Anforderungen und Quelleninhalte dürfen nicht erfunden werden.
Annahmen werden ausdrücklich als solche gekennzeichnet.

## 3. Format und Qualitätskriterien

Jede Anforderung erhält eine unveränderliche, eindeutige ID im Format `REQ-<nummer>`.
Eine Anforderung enthält mindestens:

```markdown
### REQ-001 – Kurzer, eindeutiger Titel

**Beschreibung:** Das System muss ...

**Akzeptanzkriterien:**
- ...

**Quelle / Begründung:** ...
```

Anforderungen sind:

- eindeutig, atomar und widerspruchsfrei,
- vom Problem bzw. Stakeholder-Bedürfnis her formuliert,
- mit „muss“ und beobachtbarem Verhalten beschrieben,
- prüfbar durch messbare Akzeptanzkriterien,
- priorisiert und mit Quelle oder Begründung versehen.

Lösungsentscheidungen gehören in [`docs/architecture/`](../architecture/). Wenn eine
technische Randbedingung erforderlich ist, wird ihre Notwendigkeit begründet.

## 4. Process

1. Stakeholder-Bedürfnis und Systemgrenze klären.
2. Bedürfnis in eine oder mehrere atomare Anforderungen überführen.
3. Eindeutige `REQ-ID`, Akzeptanzkriterien, Priorität und Quelle ergänzen.
4. Anforderungen auf Verständlichkeit, Vollständigkeit, Widerspruchsfreiheit und
   Prüfbarkeit prüfen.
5. Jede Änderung nachvollziehbar dokumentieren; bestehende IDs nicht wiederverwenden.

## 5. Traceability und Output

Jede `REQ-ID` wird in der zugehörigen Architekturdokumentation referenziert. Die
Architektur verweist weiter auf betroffene Code-Elemente und Tests. So entsteht die
Kette:

`Stakeholder-Bedürfnis → REQ-ID → Architekturentscheidung/Schnittstelle → Code → Test`

Ein Ergebnis gilt erst als umgesetzt, wenn die Anforderung verifiziert (nachweislich
erfüllt) und, sofern erforderlich, validiert (für den vorgesehenen Zweck geeignet)
wurde. Quellen aus `docs/reference/` werden mit Dateiname und exakter Fundstelle
angegeben; ungeprüfte Rechercheergebnisse werden als solche gekennzeichnet.
