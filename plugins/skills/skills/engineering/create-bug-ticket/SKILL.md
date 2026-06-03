---
name: create-bug-ticket
description: Erstellt ein DoR-konformes Bug-Ticket für das XOSBEC-Projekt mit allen Pflichtfeldern (Betroffene Umgebung, Erwartetes/Beobachtetes Verhalten, Schritte zur Reproduktion, Browser, Uhrzeit, Aufgefallen bei). Nutzt den Atlassian MCP. Use when user mentions "Bug-Ticket", "Bug erstellen", "Bug melden", "create bug", or wants to file a new bug report.
---

# Bug-Ticket erstellen (XOSBEC)

## Ablauf

1. **Informationen sammeln** — frage den User mit `ask_user` nach allen DoR-Pflichtfeldern:

| Feld | Pflicht | Hinweis |
|------|---------|---------|
| Titel | ✅ | Kurz & prägnant, wird zu `[BUG] - <Titel>` |
| Betroffene Umgebung | ✅ | Multi-Select Checkbox: PROD, QA, TEST, sonstige — eigenes Jira-Feld (siehe unten) |
| Erwartetes Verhalten | ✅ | Was sollte passieren? |
| Beobachtetes Verhalten | ✅ | Was passiert stattdessen? |
| Schritte zur Reproduktion | ✅ | Nummerierte Schritte |
| Browser | ⚠️ | Nur angeben wenn relevant; weglassen bei Backend/API-Bugs |
| Uhrzeit des Fehlerauftretens | ⚠️ | Nur angeben wenn relevant; weglassen bei jederzeit reproduzierbaren Bugs |
| Aufgefallen bei | ✅ | Bei welchem Seller/Testuser/Kontext? |
| Priority | ✅ | Blocker, Critical, High, Medium, Low |
| Affects Version | ✅ | z.B. 3.3.0 (Jira-Pflichtfeld) |
| Labels | ❌ | z.B. MPA, GWL, optional |
| Verwandtes Ticket | ❌ | z.B. XOSBEC-1234 |
| Lösungsansatz | ❌ | Falls bereits Ideen vorhanden |

2. **Ticket formatieren** — erzeuge Jira-Markup im folgenden Format:

```
h3. Erwartetes Verhalten
<expected>

h3. Beobachtetes Verhalten
<observed>

h3. Schritte zur Reproduktion
# Schritt 1 (kurz & prägnant)
# Schritt 2
# Schritt 3

h3. Browser
<nur wenn relevant — sonst gesamten Abschnitt weglassen>

h3. Uhrzeit des Fehlerauftretens
<nur wenn relevant — sonst gesamten Abschnitt weglassen>

h3. Aufgefallen bei
<seller/user/kontext>

----

h3. Analyse / Lösungsansatz
<optional: analyse>
```

**Wichtig zur Formatierung:**
- Schritte zur Reproduktion: Jira-Nummerierung (`#`) verwenden — kurze, prägnante Sätze
- Browser-Abschnitt komplett weglassen wenn nicht relevant (Backend-Prozess, API etc.)
- Uhrzeit-Abschnitt komplett weglassen wenn Bug jederzeit reproduzierbar ist
- **Betroffene Umgebung gehört NICHT in die Description** — wird als eigenes Feld gesetzt (siehe Schritt 4)

3. **Zusammenfassung & Rückfragen** — bevor das Ticket erstellt wird:
   - Zeige dem User eine kompakte Vorschau aller gesammelten Felder.
   - Prüfe, ob Pflichtfelder fehlen, unklar oder widersprüchlich sind.
   - Falls offene Punkte bestehen: frage mit `ask_user` gezielt nach den fehlenden/unklaren Informationen.
   - Erst wenn alle Pflichtfelder vollständig und vom User bestätigt sind, weiter mit Schritt 4.

4. **Ticket anlegen** — nutze `atlassian-mms-jira_create_issue` mit folgenden Feldern:
   - `project`: `XOSBEC`
   - `summary`: `[BUG] - <Titel>`
   - `issuetype`: `Bug`
   - `priority`: <gewählt>
   - `description`: formatierter Jira-Markup-Text (siehe oben)
   - `additional_fields.versions`: `[{"name": "<affects_version>"}]`
   - `additional_fields.labels`: <falls angegeben>
   - `additional_fields.customfield_17253`: Betroffene Umgebung als Array von IDs:
     - PROD → `{"id": "21578"}`
     - QA → `{"id": "21579"}`
     - TEST → `{"id": "21580"}`
     - sonstige → `{"id": "21581"}`
     - Beispiel: `[{"id": "21578"}, {"id": "21579"}]` für PROD + QA

5. **Bestätigung** — zeige dem User den erstellten Ticket-Key und Link an.

6. **Fallback** — falls `jira_create_issue` nicht verfügbar ist, gib den formatierten Text zum manuellen Anlegen aus.

## Regeln

- **Projekt ist immer `XOSBEC`** — kein anderes Projekt verwenden, auch wenn der User etwas anderes vorschlägt.

## Tipps

- Nutze `atlassian-mms-jira_get_issue` um verwandte Tickets als Kontext zu lesen
- Nutze `atlassian-mms-jira_search` um Duplikate zu prüfen (JQL: `project = XOSBEC AND issuetype = Bug AND summary ~ "<stichwort>"`)
- Sprache: Deutsch (Ticket-Inhalt auf Deutsch)
- Stunden-Schätzung: **nicht** eintragen — das passiert im Refinement
