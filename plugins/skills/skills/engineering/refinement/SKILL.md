---
name: refinement
description: Prepare a Jira ticket for refinement by parsing a Jira XML export, exploring the codebase, collecting open questions, and producing a phased implementation sketch with hour-range estimates. Use when the user wants to refine a ticket, prepare for refinement, mentions "refinement", "ticket vorbereiten", or provides a Jira XML export.
---

# Refinement Preparation

Bereitet ein Jira-Ticket für ein Refinement-Meeting vor: liest den Jira-XML-Export, analysiert die Codebase und erzeugt ein **schlankes Refinement-Dokument** mit betroffenen Bereichen, offenen Fragen und grobem Umsetzungsplan inkl. Stunden-Range.

## Leitprinzip: Schlank halten

Das Dokument enthält **nur, was im Meeting wirklich gebraucht wird**. Boilerplate, redundante Ticket-Inhalte und Kategorien ohne Substanz werden weggelassen.

## Quick start

1. Nutzer gibt Jira-XML als **Datei-Pfad** oder **direkt eingefügten XML-Inhalt** an.
2. Ticket parsen, Codebase analysieren, Refinement-Dokument im Session-Workspace ablegen.
3. Nutzer ergänzt Antworten im Meeting (Fragen müssen nicht alle vorab geklärt sein).

## Workflow

### 1. Input lesen

- **Datei-Pfad:** mit `view` einlesen.
- **Inline-XML:** aus der Nutzer-Nachricht parsen.
- Aus dem `<item>`-Element nur extrahieren, was im Workflow tatsächlich verwendet wird: `key`, `summary`, `description` und Akzeptanzkriterien (für eigene Analyse, **nicht** zur Wiedergabe im Dokument), Schlüsselbegriffe für die Codebase-Suche.
- Bei fehlendem/ungültigem XML: Pfad/Inhalt erneut anfragen.

### 2. Codebase analysieren

Bevor Fragen formuliert werden, **aktiv** in der Codebase suchen, was vom Ticket betroffen sein könnte:

- Aus Summary/Description Schlüsselbegriffe extrahieren (Feature-Namen, Routen, Komponenten, API-Endpoints).
- `grep`/`glob` nutzen, um wahrscheinlich betroffene Dateien zu identifizieren.
- Vorhandene Patterns prüfen (gibt es bereits ähnliche Komponenten/Hooks/Sagas, die als Vorlage dienen?).
- Ergebnisse als **"Betroffene Bereiche (Hypothese)"** mit konkreten Datei-Pfaden + Zeilen festhalten — explizit als Hypothese, nicht als Fakt.

Verifikation vor Behauptungen: **niemals** behaupten, etwas existiere, ohne es per Tool gefunden zu haben.

### 3. Fragen sammeln

Alle Fragen **auf einmal** sammeln. Nutzer beantwortet im Meeting, was beantwortbar ist.

Mögliche Kategorien — **nur aufnehmen, wenn es dort eine echte, ticket-spezifische Frage gibt**. Kategorien ohne sinnvollen Inhalt werden komplett weggelassen (kein Platzhalter, keine Zeile):

1. **Scope & Akzeptanzkriterien** — Was ist drin/raus? User-Flow konkret?
2. **Betroffene Module/Dateien** — Hypothese bestätigen lassen; vergessene Touchpoints?
3. **API/Backend-Abhängigkeiten** — Neue Endpoints? OpenAPI-Spec-Änderung? Backend-PR koordiniert?
4. **UI/UX** — Designs/Mockups? Mobile vs. Desktop? Bestehende Komponenten ausreichend?
5. **Edge Cases & Fehlerbehandlung** — Offline-Verhalten, Netzwerk-Fehler, leere/ungültige Daten, Race Conditions.
6. **Migration / Breaking Changes** — Daten-Migration, Rückwärtskompatibilität.

**Bewusst weggelassen** (außer bei expliziter Relevanz im Ticket):
- *i18n / Übersetzungen* — wird im Umsetzungsplan als kleiner Punkt mitgenommen, nicht als Frage-Kategorie.
- *Tests & Test-Strategie* — gehört in den Umsetzungsplan, nicht in offene Fragen.
- *Performance / PWA-Offline* — nur erwähnen, wenn es konkrete Auswirkungen gibt (z. B. Dexie-Schema-Änderung). Standard "keine Auswirkung erwartet" wird **nicht** geschrieben.
- *Rollout / Feature-Flags* — bei kleinen Features oder Bugs nicht relevant; nur aufnehmen, wenn das Ticket gestaffeltes Rollout, Feature-Flags oder mehrere Stages explizit erfordert.

### 4. Umsetzungsplan skizzieren

**Phasen als Tracer-Bullet-Slices** (vertikal, vom Backend-Call bis zur UI), jede Phase mit **Stunden-Range**.

Bei kleinen Tickets reichen 1–2 Phasen — keine künstlichen 4 Phasen erzwingen. Tests und i18n werden als kleine Punkte in der passenden Phase erwähnt, nicht als eigene Phase.

#### Stunden-Heuristik (KI-gestützte Entwicklung)

Schätzungen müssen berücksichtigen, dass die Umsetzung **mit AI-Coding-Assistenz** erfolgt. Das halbiert nicht alles, beschleunigt aber Boilerplate, Test-Gerüste und Pattern-übernahme deutlich.

- Untere Grenze = optimistisch, klare Fragen geklärt, AI übernimmt Boilerplate effizient.
- Obere Grenze = realistisch inkl. Unbekannten, Review-Zyklen, manueller QA.
- Als grober Anker für eine **Frontend-Änderung mit klarem Pattern-Vorbild im Repo**: 1–3h für Tracer-Bullet, 2–5h für Hauptlogik inkl. Edge Cases & Tests.
- Größere Phasen-Ranges nur, wenn Spec-Änderungen, neue Sagas/Reducer, Migrationen oder echte Unbekannte vorliegen.
- Bei range-bestimmenden offenen Fragen: explizit markieren (z. B. *„abhängig von Scope-Frage X"*).
- **Gesamt-Range** am Ende als Summe.

### 5. Refinement-Dokument speichern

Speichere das Dokument im **Session-Workspace**:

```
<session-folder>/files/<TICKET-KEY>-refinement.md
```

Den konkreten Pfad aus dem `<session_context>` der laufenden Session ableiten. Falls die Datei existiert, mit `edit` aktualisieren statt überschreiben.

Verwende die Vorlage aus [TEMPLATE.md](TEMPLATE.md).

### 6. Zusammenfassung an den Nutzer

Kurz im Chat (≤ 8 Zeilen):
- Ticket-Key + Summary
- Hauptfundstelle in der Codebase (1 Zeile)
- Anzahl offener Fragen + Range-bestimmende Kern-Frage
- Gesamt-Stunden-Range
- Pfad zum Refinement-Dokument

Nicht den ganzen Plan im Chat wiederholen.

## Checkliste vor Abschluss

- [ ] XML erfolgreich geparst, Ticket-Key extrahiert
- [ ] Codebase-Hypothese mit konkreten Datei-Pfaden + Zeilen untermauert
- [ ] Fragen sind ticket-spezifisch; leere Kategorien komplett weggelassen
- [ ] Keine Sektionen für Beschreibung, Akzeptanzkriterien, Performance ohne Impact, Rollout bei kleinen Tickets, Notizen
- [ ] Schätzungen berücksichtigen AI-gestützte Entwicklung (eher knapp ansetzen)
- [ ] Jede Phase hat Stunden-Range; Gesamt-Range vorhanden
- [ ] Dokument unter `<session-folder>/files/<KEY>-refinement.md` gespeichert
- [ ] Kurz-Summary im Chat
