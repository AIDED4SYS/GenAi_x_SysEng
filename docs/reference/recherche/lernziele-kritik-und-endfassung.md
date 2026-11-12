# Lernziele GenAI x Systems Engineering: Kritik und Endfassung

- **Stand:** 2026-11-09
- **Entstehung:** Chat-Ausarbeitung mit KI-Agent (Claude Code Opus 5.5 ); Entscheidungen durch den Menschen
- **Status:** Ausarbeitung, keine Primärquelle. Aussagen zur Taxonomie nach Anderson & Krathwohl
  stammen aus dem Modellwissen und sind **ungeprüft**; vor Zitation über die Originalquelle belegen
  (Anderson, L. W. & Krathwohl, D. R. (Hrsg.) (2001): *A Taxonomy for Learning, Teaching, and
  Assessing*).

## Glossar

- **System of Interest (SOI):** das System, das im Projekt entwickelt wird; im Kurs das Übungsbeispiel Ampelsystem.
- **Enabling System:** AI Harness (`AGENTS.md`, Regeln, Skills, Settings etc) plus unterstützende Dateien zur Arbeitsweise (z. B. Issue-, PR-Templates, continuous integration CI), die die Entwicklung des SOI unterstützen.

## 1. Ausgangsversion der Lernziele mit Stufen

### Lernziel 1

**Erste Fassung:**

> Die Studierenden können am beispiel eines Ampelsystem als Vertical Slice entwerfen und umsetzen,
> unterstützt durch ein selbst angepasstes Enabling System + AI Harness. Studis sollen befähigt
> werden die gelernten Inhalte in Prüfungsleistung anzuwenden.

### Lernziel 2

**Erste Fassung:**

> Die Studierenden können für eine Aufgabe situativ entscheiden, ob sie das Verhalten des Agenten
> einmalig per Prompt Engineering oder dauerhaft im Repo über Context Engineering steuern und die
> Änderung umsetzen.

### Lernziel 3

**Erste Fassung:**

> Die Studierenden können die steuernden Elemente des Enabling Systems ihrem Zweck zuordnen und
> erklären. Desweiteren können sie ein unerwünschtes Agentenverhalten auf das verursachende
> Element des Repos zurückführen und den Ablageort einer Information begründet bewerten.

## 2. vollständige KI-Kritik

### Kritik der drei Lernziele
#### Lernziel 1: Ampelsystem als Vertical Slice

Taxonomiestufe: Als Anspruch ist das Erschaffen (Planen, Produzieren), weil ein neues Ergebnis entworfen und gebaut wird. Das gilt aber nur, wenn die Ampel-Aufgabe offen ist.
Annahme: Wird das Beispiel eng angeleitet, mit vorgegebenen Anforderungen und Musterarchitektur, dann sinkt es auf Anwenden.

Was trägt: Das Ziel bündelt die beiden anderen in einem einzigen, sichtbaren Ergebnis: einer durchgängigen Kette von Anforderung über Architektur zu Code und Test.

Kritik:

- **1.1 Inhalt und Kriterium fehlen:** Dem Satz fehlt das Objekt: Was genau wird als Vertical Slice entworfen, das ganze Ampelsystem oder ein Ausschnitt davon? Außerdem steht nirgends, wann ein Slice als gelungen gilt. Mögliche Maßstäbe wären:
  - Jede Anforderung lässt sich durchgängig bis zu Code und Test verfolgen.
  - Die Tests verifizieren die Anforderungen.
  - Die Tests laufen fehlerfrei durch.

  Fehlt so ein Maßstab, besteht auch lauffähiger Ampel-Code ganz ohne Rückverfolgbarkeit.
- **1.2 Leistung als Bedingung getarnt:** „Selbst angepasst“ ist eine Leistung, die sich als Bedingung tarnt. Grammatisch ist das Anpassen des Enabling Systems nur der Rahmen. Dabei ist es eigentlich eine eigene, zu prüfende Leistung, und sie überschneidet sich mit Lernziel 2. Unklar bleibt, ob sie bewertet wird. Das Nebeneinander „Enabling System + AI Harness“ macht zudem offen, ob das ein Ding ist oder zwei. In Lernziel 3 klingt es wie dasselbe.

Hinweis zum Schlusssatz: „Studis sollen befähigt werden …“ ist kein Lernziel, sondern eine Absicht der Lehrenden (ein Richtziel). Er enthält kein beobachtbares Verhalten, deshalb habe ich ihn nicht als Kritikpunkt gezählt. Er sagt aber etwas aus: Wenn der eigentliche Zweck die Übertragung auf die Prüfungsleistung ist, dann prüft keines der drei Ziele diese Übertragung. Alle drei bleiben im Ampel-Beispiel.

#### Lernziel 2: Prompt Engineering oder Context Engineering wählen

Taxonomiestufe: Bewerten, weil die Studierenden zwei Alternativen nach der Situation gegeneinander abwägen. Umsetzen ist zusätzlich Anwenden (Ausführen). Das Wissen ist prozedural und teils metakognitiv.

Was trägt: Die Entscheidung muss am Ende umgesetzt werden. Damit wird sie am Ergebnis überprüfbar und bleibt nicht nur eine Behauptung.

Kritik:

- **2.1 „Situativ“ ohne Entscheidungsmerkmale:** „Situativ“ verdeckt, was man beobachten soll. Das Ziel nennt nicht, an welchen Merkmalen der Situation sich die Entscheidung ausrichten soll. Beispiele wären: tritt die Aufgabe wieder auf, gilt sie für das ganze Team, betrifft sie alle Dateien oder nur einen Typ. Ohne diese Merkmale lässt sich eine Entscheidung nicht als richtig oder falsch einstufen. Die Bewertung hängt dann am Bauchgefühl der prüfenden Person. Zudem ist „einmalig vs. dauerhaft“ als Gegensatz zu grob. Ein Skill zum Beispiel ist dauerhaft abgelegt, wird aber nur bei Bedarf geladen. Ohne Kriterien bleibt offen, wo so ein Fall hingehört. Annahme: Im Kurs heißt Context Engineering „persistente Artefakte im Repo“.
- **2.2 Überschneidung mit Lernziel 3:** Starke Überschneidung mit Lernziel 3. „Ablageort einer Information begründet bewerten“ enthält diese Entscheidung schon fast vollständig. Lernziel 2 fügt im Kern nur zwei Dinge hinzu: die Option „gar nicht ablegen“ und das Umsetzen. Bleiben beide Ziele so, bleibt unklar, welche Leistung zu welchem Ziel gehört, und bei der Prüfung wird doppelt gezählt.

#### Lernziel 3: Elemente zuordnen, Fehlverhalten zurückführen, Ablageort bewerten

Taxonomiestufe: Das Ziel deckt drei Stufen ab. Zuordnen und erklären sind Verstehen (Klassifizieren, Erklären). Zurückführen ist Analysieren, weil man Ursache und Wirkung im Repo trennen muss. Begründet bewerten ist Bewerten, weil man gegen Kriterien urteilt. Formal ist die höchste Stufe also Bewerten, und zwar mit konzeptuellem Wissen.

Was trägt: Fehlverhalten auf das verursachende Element zurückführen ist eine echte Diagnoseaufgabe aus der Praxis. Sie ergibt ein klar beobachtbares Ergebnis: die genannte Datei plus die Begründung.

Kritik:

- **3.1 Verschachtelte Lernziele:** Es sind eigentlich drei bis vier Lernziele auf drei Stufen. Dadurch ist offen, welche Stufe das eigentliche Ziel ist und wie geprüft wird. Wer zuordnen kann, aber nicht diagnostizieren, hat das Ziel dann erreicht oder nicht? Eine einzelne Prüfungsaufgabe kann das nicht sauber entscheiden. Die Verstehen-Teile wirken eher wie Voraussetzungen für die Diagnose.
- **3.2 Fehlendes Kriterium, offener Inhalt:** Kein Kriterium, vor allem nicht beim Bewerten. Bei „begründet bewerten“ fehlt der Maßstab, zum Beispiel Single Source of Truth, Geltungsbereich oder ob die Information immer oder nur bei Bedarf geladen wird. Ohne diesen Maßstab zählt jede Begründung als „begründet“. Dazu ist „die steuernden Elemente“ inhaltlich offen. Annahme: Gemeint sind AGENTS.md, .agents/rules/, .agents/skills/ und context.md. Falls auch Settings, Hooks oder MCP dazugehören, ändert sich der Umfang deutlich.

## 3. Entscheidung und Begründung je Kritikpunkt mit Endversion

### Lernziel 1 (Hauptziel)

**Endfassung:** „Die Studierenden können aus vorgegebenen Stakeholder-Anforderungen die
Anforderungen auf System- und Subsystemebene ableiten und daraus einen Vertical Slice des System
of Interest entwerfen und umsetzen. Sie arbeiten dabei mit einem selbst angepassten Enabling
System aus AI Harness und unterstützenden Dateien, am Übungsbeispiel eines Ampelsystems. Die
Umsetzung ist gelungen, wenn sich jede Anforderung bis zu ihrer Umsetzung und Verifikation
verfolgen lässt und jedes Element der Umsetzung auf eine Anforderung zurückgeht. Die Form der
Rückverfolgung ist frei.“ (Erschaffen) Verb: ableiten, entwerfen, umsetzen. Inhalt: Anforderungen
auf System- und Subsystemebene aus vorgegebenen Stakeholder-Anforderungen, Vertical Slice des
System of Interest. Kriterium: Jede Anforderung lässt sich bis zu Umsetzung und Verifikation
verfolgen, und jedes Element der Umsetzung geht auf eine Anforderung zurück; Form frei.

**Meine Entscheidungen:**

- **Kritik 1.1 teilweise angenommen:** Durchgängige Rückverfolgbarkeit ist ein Ziel. Nicht
  übernommen: „Tests verifizieren die Anforderungen“ und „Tests laufen fehlerfrei“, weil sich die
  Rolle der Tests erst im Prozess des Erschaffens klärt und Tests unterschiedliche Aufgaben
  tragen. Die Traceability bezieht sich auf das SOI, gilt in beide Richtungen und ist
  lösungsneutral formuliert.
- **Kritik 1.2 nicht passend:** Die Kritik traf nicht das Gemeinte. Klarstellung: Das Enabling
  System besteht aus dem AI Harness plus unterstützenden Dateien (`AGENTS.md`, Issue- und
  PR-Templates, Dateien zur Arbeitsweise). Die Studierenden erstellen nicht nur Inhalte zum
  Konfigurationsstand des SOI, sondern passen im Verlauf auch das Enabling System an; es kommen
  Elemente hinzu. Die Anpassung wird in Lernziel 1 nicht bewertet; sie steht dort nur als
  Bedingung, die formative Prüfung liegt in Lernziel 2.
- **Weitere Entscheidungen:**
  - Vorgegeben sind nur die Stakeholder-Anforderungen; die Ableitung auf System- und
    Subsystemebene leisten die Studierenden selbst. Damit bleibt die Stufe **Erschaffen**.
  - Die eigene Leistung liegt vor allem im Ableiten und Strukturieren, weniger im Code, den der
    Agent schreibt.
  - Die Ampel ist nur Übungsfeld und steht deshalb in der Bedingung, nicht im Inhalt.
  - Der Schlusssatz der ersten Fassung („Studis sollen befähigt werden …“) entfällt als Richtziel;
    die Lernziele bleiben so auf die Prüfungsleistung übertragbar.

### Lernziel 2

**Endfassung:** „Die Studierenden können für eine konkrete Aufgabe bewerten, welche Anteile
eines Prompts (Rolle, Kontext, Aufgabe, Beispiel, Format, Randbedingungen) das Enabling System
bereits passend bereitstellt und welche sie im Prompt ergänzen oder überschreiben müssen. Für
jeden unpassenden Anteil entscheiden sie, ob er nur für diese Aufgabe oder dauerhaft nicht passt,
und passen im zweiten Fall das Enabling System an. Die Bewertung ist gelungen, wenn der Prompt
genau die Anteile enthält, die das Enabling System für diese Aufgabe nicht oder nicht passend
liefert, und jeder unpassende Anteil zutreffend als momentan oder dauerhaft eingeordnet ist.“
(Bewerten) Verb: bewerten, entscheiden, anpassen. Inhalt: die sechs Prompt-Anteile im Abgleich
mit dem Enabling System, Einordnung unpassender Anteile als momentan oder dauerhaft. Kriterium:
Der Prompt enthält genau die fehlenden oder unpassenden Anteile; jeder unpassende Anteil ist
zutreffend als momentan oder dauerhaft eingeordnet.

**Meine Entscheidungen:**

- **Kritik 2.1 teilweise angenommen:** Der Kritik fehlten Informationen. Rolle, Kontext, Aufgabe,
  Beispiel, Format und Randbedingungen stehen teilweise schon in den Repo-Dateien. Gelernt werden
  soll, welche Anteile des Prompt Engineering eine Aufgabe braucht und welche das Repo schon
  liefert; z. B. passt die Rolle aus `AGENTS.md` nicht zu jeder Aufgabe. Diese Abwägung ist
  **Bewerten**. Das Schema der sechs Anteile ist im Kurs eingeführt.
- **Kritik 2.2 angenommen:** „Gar nicht ablegen“ fehlte als Option; es gab eine Überschneidung
  mit Lernziel 3. Abgrenzung: Lernziel 3 klärt, *wo im Repo* etwas hingehört; Lernziel 2 klärt,
  *ob es überhaupt ins Repo* gehört.
- **Weitere Entscheidungen:**
  - Das Schema der sechs Prompt-Anteile ist im Kurs eingeführt und bildet den Inhalt des Ziels.
  - Entscheidungsmerkmal: Passt ein Anteil nur für diese Aufgabe nicht (Prompt) oder dauerhaft
    nicht (Enabling System anpassen)? Fehler im Repo sollen korrigiert werden.
  - Das Mitentwickeln des Enabling Systems gehört zu Lernziel 2 und wird nur formativ
    geprüft, also mit Rückmeldung, ohne Note.

### Lernziel 3

**Endfassung:** „Die Studierenden können für eine Information, die bereits im Repository
abgelegt ist oder neu abgelegt werden soll, das zuständige Element des Enabling Systems bestimmen
und die Absicht hinter dessen Gestaltung begründen. Anlass kann auch ein beobachtetes
unerwünschtes Agentenverhalten sein, dessen Ursache sie auf ein Element zurückführen (z.B fehler
in der Agentenanweisung AGENTS.md). Die Begründung ist gelungen, wenn sie sich auf mindestens ein
Gestaltungsprinzip (z. B. Single Source of Truth, Separation of Concerns) und auf das
Ladeverhalten des Elements stützt. Mehrere Ablageorte sind zulässig, sofern die Begründung
trägt.“ (Analysieren) Verb: bestimmen, begründen, zurückführen. Inhalt: zuständiges Element des
Enabling Systems für eine bestehende oder neue Information, Absicht hinter dessen Gestaltung,
Ursache eines unerwünschten Agentenverhaltens. Kriterium: Begründung stützt sich auf mindestens
ein Gestaltungsprinzip und auf das Ladeverhalten; mehrere Ablageorte zulässig, sofern die
Begründung trägt.

**Meine Entscheidungen:**

- **Kritik 3.1 angenommen:** Im Ziel stecken mehrere Lernziele ineinander; die Taxonomie stimmt.
  Lösung: Beschränkung auf **Analysieren**. *Zuordnen* und *erklären*
  entfallen als Lernziel, bleiben aber Lehrinhalt.
- **Kritik 3.2 teilweise angenommen:** „Steuernde Elemente“ ist ungenau. Beim Ablageort reicht
  *begründen*, *bewerten* ist nicht nötig. Weil Text überall auf das Sprachmodell wirkt, ist jede
  Datei ein Element des Enabling Systems oder des SOI; gemeint sind alle Elemente des Enabling
  Systems.
- **Weitere Entscheidungen:**
  - Ablageort meint beides: bestehende Information und solche, die noch nirgends steht. Ziel ist,
    die Absicht hinter der Gestaltung des Repos zu erkennen.
  - Die Prüfungsfälle gehen über die „Karte“ in `AGENTS.md` hinaus, sonst sinkt das Ziel auf
    Anwenden.
  - Unerwünschtes Agentenverhalten bleibt im Ziel, aber nur als Anlass, nicht als zweite Leistung.
  - Kriterium: Variante C, Gestaltungsprinzipien plus Ladeverhalten (statt dokumentierter
    Absicht, schlüssiger Begründung oder Funktionsprobe).
  - Geprüft wird am Kursende, weil das Ladeverhalten schrittweise eingeführt wird.
  - Keine Mindestanzahl an Fällen; die Qualität im Einzelfall reicht.

## 4. Die Taxonomie-Frage am eigenen Projekt

**Frage:** Welches der drei Lernziele könnte ein Sprachmodell heute für die Studierenden
miterledigen? Und ist das ein Problem für das Projekt, oder ist es der Punkt?

**Antwort nach Diskussion:** Am ehesten kann ein Sprachmodell Teile von Lernziel 1
miterledigen: Die Inhalte einer Ampel (Anforderungen, Code, Tests) erzeugt es aus den
Trainingsdaten fast selbst. Schwierig bleibt aber, die Architektur des Repositorys über viele
Dateien und Sitzungen stimmig zu halten, denn das Modell hat kein Gedächtnis zwischen Sitzungen.
Am wenigsten kann es Lernziel 2 miterledigen, weil nur die Studierenden wissen, ob ein Anteil nur
für diese Aufgabe oder dauerhaft nicht passt. 

**Offener Punkt: stimmige Repo-Architektur kein Kriterium**

Eine stimmige Repo-Architektur ist essentiell, wird aber bisher in keinem Kriterium gemessen. Lernziel 1 prüft nur die Rückverfolgbarkeit.
**Mögliche Erweiterung der Kriterien:** Die Schwierigkeit entsteht beim Erschaffen über viele Artefakte und den ganzen Prozess; sie ist eine Eigenschaft des Produkts. Kriterium den Agenten über das Enabling System so zu steuern, dass die Architektur stimmig bleibt fehlt bisher. 
