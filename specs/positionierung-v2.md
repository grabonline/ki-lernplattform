# KI-Lerntool — Positionierung v2: Second Brain / Meta-Tool

**Datum:** 2026-04-20
**Status:** Positionierungs-Pivot nach Knowunity-Analyse
**Ersetzt (teilweise):** Spec v1 Abschnitt 3, 4, 8, 9

---

## Kern-These (eine Zeile)

> **„Das persoenliche Second Brain fuer Schueler — verbindet alle Tools, die sie eh nutzen, und baut daraus einen vernetzten Langzeit-Vault."**

Wir sind **nicht** Konkurrent zu Knowunity, NotebookLM, OneNote, ChatGPT. Wir sind **ein Layer drueber**.

---

## Was das aendert

### Vorher (Spec v1)
- „Nachhilfe-Killer"
- Ersetzt Tools
- Schueler laedt alles bei uns hoch
- Wir bauen eigene AI-Tutor-Features

### Jetzt (v2)
- **„Second Brain fuer die Schule"**
- Integriert vorhandene Tools
- Schueler nutzt weiter was er eh nutzt, wir ziehen zusammen
- Unsere KI macht **Validierung + Verknuepfung + Langzeit-Graph** — nicht Tutoring

---

## Die Meta-Tool-Architektur

```
┌─────────────────────────────────────────────────────────────┐
│                  KI-LERNTOOL (Second Brain)                 │
│                                                             │
│  [Vault]  [Validator]  [Knowledge Graph]  [Luecken-Radar]  │
└─────────────────────────────────────────────────────────────┘
        ↑           ↑            ↑            ↑         ↑
        │           │            │            │         │
   ┌────┴───┐  ┌────┴────┐  ┌────┴────┐  ┌───┴───┐  ┌──┴────┐
   │OneNote │  │NotebookLM│  │Knowunity│  │ChatGPT│  │ Anki  │
   │ (MS)   │  │ (Google) │  │  (DE)   │  │Claude │  │       │
   └────────┘  └──────────┘  └─────────┘  └───────┘  └───────┘
```

**Der Schueler nutzt weiter alle diese Tools.** Wir ziehen das Wissen raus, validieren, vernetzen, speichern — und geben es als vernetzten Graph zurueck.

---

## Integrations-Vektoren

| Tool | Wie integriert | Legal-Status | Prioritaet |
|---|---|---|---|
| **OneNote / MS Notes** | Export → Import (Standard-Format) | OK | Hoch — Schule zwingt oft dazu |
| **NotebookLM** | Export-Link einfuegen, wir parsen | OK | Hoch — viele Schueler nutzen es gratis |
| **Knowunity** | User kopiert Link/Screenshot → wir validieren + legen in Vault | **OK (user-getrieben, kein API-Scrape)** | Hoch — Community-Content nutzbar |
| **ChatGPT / Claude Conversations** | Copy-Paste-Paste-Import, spaeter Share-Link | OK | Mittel |
| **Anki** | Export .apkg, Import + Spaced-Repetition-Integration | OK | Mittel — spaeter |
| **Obsidian** | Markdown-Kompatibilitaet (bidirektional) | OK | Niedrig — Power-User-Fang |

**Kritisch:** Kein Scraping, kein Crawl. Alles **user-getrieben** (User kopiert Link/Datei). Damit ist Knowunity-Integration rechtlich sauber — es ist der Schueler, der seinen eigenen Account-Content bei uns ablegt.

---

## Der Code, den wir cracken muessen

### 1. Curriculum-Ground-Truth pro Bundesland + Klassenstufe + Fach
Dagegen validieren wir Notizen. Muss **vor** allem anderen stehen.

### 2. Semantischer Graph-Builder
Aus eingehenden Notizen/Links/Texten:
- Themen extrahieren
- Querverbindungen finden (Klasse 9 Photosynthese ↔ Klasse 7 Zellatmung)
- Auto-Linking via Embeddings

### 3. Validierungs-Engine
- „Ist das inhaltlich korrekt gegen Curriculum?"
- „Fehlt was Relevantes?"
- „Widerspricht sich was im Vault?"
- Confidence-Scores pro Aussage

### 4. Import-Pipelines
- OneNote-Format, Markdown, NotebookLM-Export, Screenshot+OCR, Knowunity-Link-Parser
- Jede Pipeline ist ein separates, testbares Modul

### 5. Luecken-Radar
Der eigentliche Pre-Test-Killer:
- „Du hast Bruchrechnung nie sauber notiert — aber die Vor-Klassenarbeit fragt's."
- „Klasse 7 Zellatmung fehlt im Vault — Klasse 9 Photosynthese baut drauf auf, du wirst das nochmal brauchen."

---

## Differenzierung gegen Knowunity — scharf formuliert

| Dimension | Knowunity | Wir |
|---|---|---|
| **Kategorie** | Social-Community-App | Second-Brain-OS |
| **Content-Herkunft** | Andere Schueler (peer-shared) | Alles was der Schueler selbst hat/nutzt |
| **Zeitachse** | Snapshot (lerne jetzt, fuer jetzt) | Persistent (baut sich ueber 9 Schuljahre auf) |
| **Validierung** | Keine (Crowd-Content, kann falsch sein) | Gegen Curriculum geprueft |
| **Feed/Scroll** | Ja | Nein (explizit) |
| **Lock-In** | Niedrig (Schueler wechseln Apps) | Hoch (Vault wird wertvoller mit jedem Eintrag) |
| **Zielnutzer** | Schueler die Community wollen | Schueler die ernsthaft lernen + Eltern die Social-Sog vermeiden |
| **Verhaeltnis zu Knowunity** | — | **Knowunity-Inhalte sind Input fuer unseren Vault** |

**Kill-Shot-Satz fuer Marketing:**
> „Knowunity ist das Nachschlagewerk. Wir sind dein Kopf."

Oder:
> „Andere Apps geben dir Content. Wir geben dir dein eigenes, vernetztes Wissen zurueck."

---

## Was das fuer den MVP bedeutet (neue Reihenfolge)

**Phase 0 — In Tochters Klasse (0–3 Monate):**
1. Vault-Kern (Markdown-basiert, Obsidian-kompatibel)
2. Curriculum-Ground-Truth fuer **ein** Fach + **eine** Klassenstufe (z.B. Bio Klasse 9 Bayern)
3. **Ein** Import-Vektor: OneNote-Export (weil die Schule das eh nutzt)
4. Validierungs-Engine gegen dieses eine Curriculum
5. Basis-Graph-Linking

**Phase 1 — Zweiter Import-Vektor (3–6 Monate):**
6. NotebookLM-Import **oder** Knowunity-Link-Parser (je nach Nutzung der Klasse)
7. Mehr Faecher + Klassenstufen

**Phase 2 — Luecken-Radar (6–9 Monate):**
8. Pre-Klassenarbeit-Vorbereitung aus Vault-Gaps

**Bewusst NICHT im MVP:**
- Eigener KI-Tutor-Chat (dafuer nutzt Schueler ChatGPT/Claude — wir verlinken nur)
- Content-Generierung (Quizzes, Karteikarten — Phase 2+)
- Social / Feed / Gamification

---

## Vorteile der Meta-Tool-Strategie

1. **Kein Feature-Race mit Knowunity.** Die bauen AI-Tutor, Hausaufgaben-Scanner, Audio-Explainer. Wir bauen **Vault + Graph + Validator**. Anderes Spiel.
2. **Schueler sagen nicht „noch eine App".** Sagen „die App die alles zusammenbringt".
3. **Eltern-Argument ist brutal stark:** „Kein Social-Feed, keine Scroll-Sucht. Nur Lern-Tool."
4. **Lock-In wird mit der Zeit brutal.** Nach 2 Jahren Vault-Aufbau wechselt kein Schueler mehr.
5. **Technisch realistisch.** Wir bauen nicht eigenes LLM, eigenen Tutor, eigene Community. Wir bauen Pipelines + Graph + Validator. Machbar.
6. **Legal einfacher.** Keine Aggregation fremder Tests, kein Crowd-Content, kein UrhG-Minenfeld. Alles was der Schueler bei uns hat, ist sein eigenes.

---

## Risiken der Meta-Tool-Strategie

1. **Erklaerungs-Problem.** „Was macht die App?" ist schwerer zu beantworten als „KI-Tutor". → Loesung: klares Demo-Video in Tochters Klasse.
2. **Mehr Kuration-Arbeit fuer Schueler.** Import bedeutet Klicken. → Loesung: radikal simple UX, Browser-Extension spaeter.
3. **Klasse 5–7 vielleicht zu jung.** Second-Brain-Denken ist abstrakt. → Loesung: Startet vielleicht bei Klasse 8–10 als Core.
4. **Import-Pipelines sind Wartungs-Ballast.** Wenn NotebookLM Format aendert, muessen wir nachziehen. → Akzeptables Risiko.

---

## Next Steps

1. [ ] AZ liest v2, entscheidet Go
2. [ ] Tochters Klasse: welche Tools werden tatsaechlich genutzt? (OneNote? Knowunity? NotebookLM?) → bestimmt **ersten Import-Vektor**
3. [ ] Curriculum-Ground-Truth: welches Fach + Klassenstufe zuerst? (Empfehlung: Bio oder Geschichte — viel strukturiertes Wissen, gut validierbar)
4. [ ] Legal-Fragen reduzieren sich drastisch — aber weiter klaeren:
   - Knowunity ToS: duerfen User eigene Inhalte exportieren? (vermutlich ja)
   - UrhG bleibt relevant fuer Test-Upload falls Phase 2 kommt
5. [ ] Name-Findung bleibt hinten — aber „Second Brain" / „Vault" / „Knowledge-OS" sind jetzt die Richtung, keine „Knack/Vice"-Namen

---

## Ein-Satz-Pitch

> **„Du nutzt weiter deine Schul-Notes, NotebookLM, ChatGPT und Knowunity. Wir bauen daraus dein vernetztes, geprueftes Gehirn — bis zum Abi."**
