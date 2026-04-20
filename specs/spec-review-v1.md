# KI-Lerntool fuer Schueler — Kritische Spec-Review

**Datum:** 2026-04-20
**Reviewer:** Claude (Opus 4.7, 1M)
**Status Spec:** Ideenphase — Arbeitstitel verworfen (KNACK/VICE raus)
**Ausgangsdokument:** Product Spec v1 von AZ, 14 Abschnitte

---

## TL;DR

Die Idee ist **tragfaehig**, der Wedge liegt aber woanders als die Spec denkt.

- **Echter USP:** Saeule 3 (Personal Knowledge Graph ueber Jahre). Das hat **keiner** sauber.
- **Bootstrap-Engine:** Saeule 2 (Notizen-Validierung). Funktioniert ab Tag 1 mit einem Schueler.
- **Rechtliches Minenfeld:** Saeule 1 (Test-Upload). Nicht nur DSGVO — **Urheberrecht an Testfragen + Jugendschutz**.
- **Ernster Konkurrent:** Knowunity. Nicht „alte Schule ohne KI", sondern **30 Mio User, 45 Mio EUR Funding, KI-Tutor „SchoolGPT", jeder dritte Schueler in DE**. Aber: Social-Community-App — nicht Tool fuer privaten Vault. Differenzierung moeglich, aber Positionierung muss scharf sein.

**Empfehlung:** Reihenfolge drehen auf Saeule 2 → 3 → 1. Legal vor Code. Name spaeter.

---

## 1. Was die Spec richtig macht

- **Drei-Saeulen-Modell ist klar.** Nicht „eine App die alles kann", sondern drei diskrete Faehigkeiten. Das ist gutes Produkt-Denken.
- **„Von Schuelern fuer Schueler" (keine Lehrer/Eltern/Schulen)** ist eine starke Positionierungs-Entscheidung. Authentisch, TikTok-kompatibel, keine B2B-Sales-Komplexitaet.
- **Red Line zu Noten und Namen ist moralisch richtig.** Auch juristisch wichtig (Jugendschutz, DSGVO Art. 9 besondere Kategorien bei Leistungsdaten).
- **Tochters Klasse als Beta** = perfektes Product-Founder-Fit. Taegliche Feedback-Schleife, echte User, kein Moralkompass-Problem.
- **Ehrlich benannte Risiken (Abschnitt 11).** DSGVO-Unklarheit, Chicken-and-Egg, Execution-Bandbreite — nichts verschwiegen.
- **Curriculum-Mapping pro Bundesland als spaeterer Schritt** ist realistisch — nicht „wir bauen Day 1 alles".

---

## 2. Was kritisch ist (muss gefixt werden, bevor Code)

### 2.1 Urheberrecht ist heisser als DSGVO (Abschnitt 7 erweitern)

Die Spec behandelt DSGVO als Hauptproblem. **Das Urheberrecht (UrhG) ist mindestens genauso kritisch:**

- Lehrer haben UrhG an selbst erstellten Aufgaben (§ 2 UrhG, Schriftwerke).
- Aggregation von 30 Klassenarbeiten im Modell = **Vervielfaeltigung** (§ 16 UrhG). Anonymisierung heilt das nicht — Urheberrecht schuetzt das Werk, nicht den Personenbezug.
- Wenn Schueler X's Klassenarbeit zu Schueler Y zurueckfliesst (auch paraphrasiert), ist das **oeffentliche Zugaenglichmachung** (§ 19a UrhG).
- **Schranke fuer Wissenschaft/Unterricht (§ 60a UrhG)** greift nicht, weil ihr kommerziell seid.
- **AI-Training-Ausnahme (§ 44b UrhG, Text-und-Data-Mining)** greift moeglicherweise — aber nur wenn Rechteinhaber keinen Opt-out erklaert haben. Schulen/Lehrer werden das tun.

**Konsequenz:**
- Der kollektive Lern-Loop („System lernt aus allen Uploads") muss rechtlich **wasserdicht** sein, bevor das Feature live geht.
- Alternative: **Nur eigene Uploads des Schuelers fliessen in seinen eigenen Vault.** Kollektives Lernen erst nach Klaerung. Das rettet Saeule 3 + 2, schiebt Saeule 1 in Phase 2.

### 2.2 Re-Identifikation durch Metadaten (Abschnitt 6)

„Gymnasium, Klasse, Fach, Thema" als Metadaten klingt harmlos — ist aber in **Kleinstaedten k-Anonymitaet = 1**:

- „Gymnasium Bergisch Gladbach, 9b, Bio, Baeume" → es gibt genau einen Bio-Kurs der 9b dort.
- Anonymisierung faellt, sobald man mit Aussenwissen kombiniert.

**Fix:**
- Aggregation auf **Schultyp + Bundesland + Klassenstufe + Thema**. Keine einzelne Schule, keine Klassenbezeichnung.
- Mindest-Schwelle: **mindestens N=10 Uploads** bevor ein Thema-Cluster fuer andere sichtbar wird.

### 2.3 Monetarisierungs-Framing kollidiert mit Positionierung (Abschnitt 3 + 9)

- Zielgruppe: Schueler Klasse 5–13 = **minderjaehrig**.
- Freemium-Premium (Abschnitt 9, Phase 2) braucht zahlende Kunden.
- **Minderjaehrige koennen keine gueltigen Kaufvertraege abschliessen** (§ 107 BGB, beschraenkte Geschaeftsfaehigkeit).
- Der „Taschengeld-Paragraph" (§ 110 BGB) reicht bei Abo-Modellen **nicht** — Dauerschuldverhaeltnisse brauchen Eltern-Zustimmung.

**Fix:** Positionierung nicht aendern („Schueler-First-Produkt"), aber Bezahl-Framing ergaenzen:
- „Tool fuer Schueler, Abo-Vertrag mit Eltern." Eltern bekommen **Vertrag**, sehen **Preis**, aber **nicht den Vault-Inhalt**.
- Elternsicht auf Zahlungen, Abo-Status — **nicht** auf Lern-Inhalte. Klare Privacy-Grenze.

### 2.4 Name-Anonymisierung ist eigene Engineering-Komponente (Abschnitt 5, 6)

Die Spec behandelt „Name vor Upload entfernen" als Feature-Detail. Tatsaechlich:

- OCR auf handschriftlichen Kopfzeilen: Fehlerquote realistisch 2–5%.
- Falls Name durchrutscht → **Datenleck eines Minderjaehrigen**. Haftungs-Kategorie.
- „Preview vor Upload" ist gut — aber Kinder klicken durch.

**Fix:**
- Eigene MVP-Komponente mit hoher Test-Coverage.
- **Serverseitig zweite Pruefung** (nicht nur clientseitig vertrauen).
- **On-device-OCR** wo moeglich, sodass Rohbild mit Name den Server gar nicht erst erreicht.
- Konservative Default-Policy: Im Zweifel **oberste 15% der Seite** wegschneiden, auch wenn kein Name erkannt.

### 2.5 Knowunity fehlt in der Problem-Analyse (Abschnitt 2, 10)

Die Spec nennt Anton, Quizlet, Studyfetch als generische Lerntools. **Knowunity fehlt — obwohl es der direkte Konkurrent ist.** Das ist ein Blindspot. Siehe Abschnitt 4 unten fuer Details.

---

## 3. Reihenfolge der Saeulen: umdrehen

Die Spec priorisiert Saeule 1 (Test-Upload) als erstes MVP-Feature (Abschnitt 5 und 14.4). Ich halte das fuer **falsch**:

| Saeule | Chicken-and-Egg? | Rechtlich? | USP-Staerke |
|---|---|---|---|
| **1. Test-Upload** | Ja — braucht Masse, damit Aggregation Wert liefert | Heiss (UrhG + DSGVO + Jugendschutz) | Mittel (Knowunity macht aehnlich) |
| **2. Notizen-Validierung** | Nein — funktioniert ab User 1 | Low (Schueler validiert eigenen Content) | Hoch (hat keiner sauber) |
| **3. Wissens-Wiki / Vault** | Nein — baut sich mit jedem Upload auf | Low (privater Vault des Schuelers) | **Sehr hoch (der echte Wedge)** |

**Vorschlag:**
1. **Phase 0 (MVP):** Saeule 2 + Saeule 3. Schueler laedt Notizen hoch, System validiert + baut Vault auf. Kein Test-Upload, keine Aggregation.
2. **Phase 1 (nach Legal-Klaerung):** Saeule 1 als Opt-in-Feature. Default aus.
3. **Phase 2 (nach 1000+ Usern):** Kollektives Lernen aus aggregierten Test-Patterns.

---

## 4. Knowunity — Deep Dive

**Kurz-Antwort:** Die sind krass. Keine alte Schule ohne KI — moderne, gut finanzierte KI-Lernplattform. Aber: Social-Community-App, kein privates Tool. Da liegt die Luecke.

### Was Knowunity 2026 ist

- **30 Mio User, 15 Laender.** Jeder dritte Schueler in DE nutzt die App.
- **Funding:** 45 Mio EUR total, 27 Mio EUR Series B in 2025 (XAnge, Project A, Redalpine, Educapital, Verena Pausder, Booking.com-Gruender).
- **KI-Tutor „SchoolGPT"** — trainiert auf 3 Mio peer-erstellten Materialien, fine-tuned auf Curricula pro Land.
- **Features:** Hausaufgaben-Scanner, AI-Quizzes, Karteikarten, Audio-Explainer, Lernzettel-Upload, Community-Feed.
- **Monetarisierung:** Freemium + Premium-Abo + B2B-Employer-Branding (Vodafone, Gothaer, Porsche).
- **Positionierung:** „Peer-Learning-Community mit KI-Assistent."

### Wo die sich von unserer Idee unterscheiden

| Dimension | Knowunity | KI-Lerntool (unsere Idee) |
|---|---|---|
| **Modell** | Social-Community, Content geteilt | Privater Vault, Content bleibt beim Schueler |
| **Lernzettel-Herkunft** | Von anderen Schuelern | Von mir selbst |
| **Knowledge-Graph** | Keiner — Snapshot-basiert | Persistent ueber Schuljahre, auto-verlinkt |
| **Validierung** | Keine — Content kann falsch sein | Notizen werden gegen Curriculum geprueft |
| **Datenschutz-Claim** | Standard-DSGVO | Radikal: keine Noten, keine Namen, eigener Vault |
| **Gamification/Feed** | Kern-Feature | Explizit kein Feature (Spec Abschnitt 5) |
| **Monetarisierung** | Freemium + B2B | Freemium fuer Eltern |

### Wo Knowunity gewinnt (und wir es akzeptieren muessen)

- **Marketing-Budget + Netzwerk-Effekt.** 30 Mio User = „die App die jeder hat". Das holen wir nie ein.
- **Breite Feature-Palette.** Die haben schon Hausaufgaben-Scanner, Karteikarten, Audio — wenn wir uns auf Breite einlassen, verlieren wir.
- **Brand Recognition in Schuelerkreisen.** TikTok-dominant.

### Wo wir gewinnen koennen (der realistische Wedge)

1. **Der eigene Vault ueber Jahre.** Knowunity ist Snapshot. Unser Tool ist **Knowledge-OS**. Das ist ein kategorisch anderes Produkt.
2. **Notizen-Validierung gegen Curriculum.** Knowunity-Content ist crowd-sourced und kann falsch sein. Wir pruefen aktiv.
3. **Datenschutz-Radikalitaet.** Keine Noten, keine Namen, kein Social-Feed. In Elternaugen: **ueberzeugender** als Knowunity.
4. **Kein Social-Feed = kein Scroll-Sog.** Eltern die das Handy ihrer Kinder weniger sehen wollen — **Knowunity ist eine Social-App**, wir sind ein Tool.

### Wahrheit

Wenn wir versuchen „Knowunity 2.0" zu bauen, verlieren wir. Wenn wir „Obsidian fuer Schueler mit KI" bauen, sind wir in einer anderen Kategorie — und da ist Knowunity nicht.

---

## 5. Rechts-Gespraech: Was muss abgeklopft werden

Die Spec (Abschnitt 7) listet 5 Fragen. Ich ergaenze auf 9:

1. ~~DSGVO Aggregation anonymisierter Tests~~ (aus Spec)
2. ~~Re-Identifikation durch Schule+Klasse+Thema~~ (aus Spec)
3. ~~Einwilligung Eltern bei Minderjaehrigen~~ (aus Spec)
4. ~~Widerspruch Schulen gegen Aggregation~~ (aus Spec)
5. ~~Urheberrecht Testfragen~~ (aus Spec — aber unterschaetzt, siehe 2.1)
6. **Neu:** UrhG § 44b (TDM-Ausnahme) — reicht das oder brauchen wir Einzel-Lizenzen?
7. **Neu:** Abo-Vertraege mit Minderjaehrigen (§ 107 BGB) — Eltern-Vertrag-Modell tragfaehig?
8. **Neu:** JuSchG / Jugendmedienschutz — braucht die App Altersverifikation?
9. **Neu:** Pflicht zur Abuse-Meldung — falls Schueler via Notizen Suizidgedanken etc. teilen: Welche Pflichten?

**Empfehlung:** Anwalt muss **EdTech-Erfahrung** haben, nicht nur generischer DSGVO-Anwalt. Kandidaten recherchieren.

---

## 6. Konkrete Aenderungs-Vorschlaege zur Spec

| Spec-Abschnitt | Aenderung |
|---|---|
| 2 Problem | Knowunity ergaenzen mit ehrlicher Abgrenzung (Snapshot vs. Vault) |
| 3 Zielgruppe | „Nicht-Ziel Eltern" → „Eltern sind Vertragspartner, nicht Zielgruppe" |
| 5 MVP Features | Reihenfolge drehen: 3 → 4 → Notizen-Validierung als Nr.1, Test-Upload nach hinten |
| 6 Privacy | Metadaten-Aggregation auf Bundesland+Schultyp+Klassenstufe, nicht Einzelschule |
| 7 Legal | Urheberrecht als eigenen Hauptpunkt, nicht Unterpunkt. 9 Fragen statt 5. |
| 8 Architektur | On-device OCR fuer Name-Strip, serverseitige Zweit-Pruefung |
| 9 GTM | Phase 1 darf kollektiv lernen nur **nach** Legal-Klaerung |
| 10 Markt | Knowunity mit ehrlichen Zahlen (30 Mio, 45 Mio EUR Funding) und Abgrenzung |
| 11 Risiken | „Knowunity zieht hinterher" als explizites Risiko |
| 13 Offene Fragen | „Elternvertrag-Modell" als neue Frage |
| 14 Next Steps | Reihenfolge: Legal-Gespraech VOR Design Saeule 1 |

---

## 7. Ehrliche Empfehlung

**Bauen? Ja — aber anders als die Spec sagt.**

1. **Name-Findung ruhig spaeter.** Bike-Shed. Jetzt nicht Thema.
2. **Legal VOR Code.** Nicht parallel. UrhG kann Saeule 1 killen — dann spart ihr das Design.
3. **Reihenfolge Saeule 2 → 3 → 1.** Saeule 2 als Einstiegs-MVP in Tochters Klasse.
4. **Knowunity-Abgrenzung klar formulieren** in jeder Kommunikation nach aussen. Nicht versuchen, sie zu kopieren.
5. **Elternvertrag-Modell** sauber durchdenken, bevor Premium live geht.
6. **GrabOnline-Starter-Kit** als Fundament ist sinnvoll — RAG + Supabase + MCP ist 80% der Tech. Das spart 6 Monate.

**Risiko, das ich sehe und AZ auch bewusst sein muss:** Parallel zu GrabOnline diese Qualitaet zu halten ist eine Execution-Wette. Die Spec nennt das als Risiko 11.5 — ich unterstreiche es.

---

## 8. Was als naechstes sinnvoll ist

- [ ] AZ entscheidet: Umbau der Spec nach diesen Punkten? Oder erst Gespraech mit Wifey auf Ist-Stand?
- [ ] Legal-Anwalt-Suche: EdTech-erfahrene DSGVO + UrhG Kanzlei in DE recherchieren
- [ ] Knowunity-Feature-Teardown: eine Stunde App nutzen, ehrliche Notizen zu Feature-Gaps
- [ ] Skizze „Notizen-Validierungs-Loop" als erstes technisches Experiment auf GrabOnline-Starter-Kit
- [ ] Name-Brainstorming auf eine konkrete Phase in 4–6 Wochen schieben (nach Spec-Finalisierung)

---

*Review-Ende. Kein Bullshit, keine Hoeflichkeitsphrasen. AZ fragt nach ehrlich — das ist ehrlich.*
