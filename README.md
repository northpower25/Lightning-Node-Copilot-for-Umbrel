# Lightning Node Copilot for Umbrel (LNNC)

Visuelle Decision Engine fuer Lightning-Peering auf Umbrel.

## Produktvision

Lightning Node Copilot for Umbrel (LNNC) kombiniert:

1. Peer-Auswahl und Channel-Aufbau fuer neue Lightning-Nodes.
2. Grafische Topologie-Simulation fuer "Was waere wenn"-Entscheidungen.
3. Laufende Optimierung auf Basis realer Node-Metriken.

Ziel ist nicht nur "welcher Peer ist gut", sondern "welcher Peer passt zu meiner Node, meinem Kapital und meinen Flussmustern".

## Produktgrenzen (wichtig)

LNNC ist in den ersten Versionen ein reines Analyse- und Empfehlungssystem (Advisory Only):

- Kein automatisches Oeffnen oder Schliessen von Channels.
- Kein automatisches Rebalancing.
- Keine automatischen Gebuehrenaenderungen.

Alle operativen Aktionen bleiben manuell beim Node-Betreiber.
Rebalancing und Fee-Management bleiben vorerst in LNDG.

## Zielgruppen

1. Einsteiger: wollen klare, erklaerbare erste Channel-Schritte statt Blindflug.
2. Fortgeschrittene Routing-Nodes: wollen Redundanz senken, Fluesse verbessern und Rebalancing-Kosten reduzieren.

## Kernmodule

## 1) Node Onboarding Wizard

Gefuehrter Einstieg mit Profil und Schritt-fuer-Schritt-Plan.

Eingaben:

- Node-Ziel: zahlen, empfangen, routen, hybrid.
- Risikoprofil: konservativ, ausgewogen, aggressiv.
- Budget und bevorzugte Kanalgroessen.
- Gewuenschte Management-Intensitaet.

Ausgabe:

- Priorisierte Peer-Liste.
- Vorschlag fuer Kanalgroessen.
- Reihenfolge in Phasen: Fundament, Diversifizierung, Spezialisierung.
- Begruendung pro Empfehlung (Nutzen, Risiko, Redundanz, Timing).

## 2) Graph Simulator

Interaktiver Netzwerk-Graph mit:

- Eigener Node im Zentrum.
- Bestehende Peers als Ring 1.
- Kandidaten als "Ghost Nodes".
- Ampelbewertung fuer Mehrwert: gruen, gelb, rot.

Simulationsmetriken (Heuristik):

- Common-Peer-Overlap-Score
- Unique-Reach-Score
- Topology-Diversity-Score
- Routing-Adjacency-Fit
- Marginal-Benefit-Score

## 3) Bootstrap Strategy Engine

Erzeugt einen konkreten Aktionsplan statt nur Rankings.

Phase 1: Grundkonnektivitaet

- Fokus auf stabile Gegenstellen und robuste Erstnutzung.

Phase 2: Diversifizierung

- Fokus auf geringe Ueberlappung und neue Netzwerkbereiche.

Phase 3: Spezialisierung

- Fokus auf passgenaue Peers entsprechend Node-Zielen und ersten Nutzungsdaten.

## 4) Adaptive Optimization Engine

Nutzt laufend Betriebsdaten, um Empfehlungen zu aktualisieren.

Beispielsignale:

- Zahlungsverlauf und Hops
- Forwards
- Kanalperformance
- Rebalance-Historie
- HTLC-Fehler

Ergebnis:

- Neue Peer-Empfehlungen mit hohem Fit.
- Erkennung fehlender Direktpeers.
- Hinweise auf Redundanz, Kostenfallen und unproduktive Channels.

Wichtig fuer V1-V2:

- Die Engine gibt nur Handlungsempfehlungen mit Begruendung aus.
- Es gibt keine direkte Ausfuehrungsfunktion fuer Open/Close/Resize.
- Rebalancing- und Fee-Vorschlaege werden als Hinweis ausgegeben, die Umsetzung erfolgt in LNDG.

## Umbrel- und Integrationsrahmen

LNNC wird als Umbrel-App im Standardlayout gebaut:

- `umbrel-app.yml`
- `docker-compose.yml`
- optional `exports.sh` und `hooks/`
- UI hinter `app_proxy`

Moegliche Integrationen:

- LND-Daten ueber Umbrel-Integrationsvariablen.
- LNDG als zusaetzlicher Datenpartner (z. B. Metriken, API-Ressourcen, Betriebsindikatoren).

## Technischer Vorschlag (MVP-freundlich)

## Backend

- Python + FastAPI fuer Collector, Scoring, Recommendation API.
- SQLite fuer schnellen Start, spaeter PostgreSQL.
- Periodische Jobs fuer Snapshots und Re-Scoring.

## Frontend

- React (oder Vue) mit Fokus auf Erklaerbarkeit.
- Graph-Visualisierung fuer Topologie und Simulation.
- Wizard-Flow fuer neue Nutzer.

## GUI-Sprache und Internationalisierung (i18n/l10n)

Der aktuelle Konzeptstand war hier nicht ausreichend konkret. Die GUI-Mehrsprachigkeit wird daher wie folgt festgelegt.

Zielbild:

- V1 unterstuetzt Deutsch (`de`) und Englisch (`en`).
- Architektur ist von Beginn an auf weitere Sprachen erweiterbar.
- Fallback-Sprache ist Englisch, falls ein Key in einer Sprache fehlt.

Scope fuer V1:

- Alle statischen UI-Texte sind uebersetzbar (Navigation, Buttons, Wizard, Labels, Hilfetexte, Fehlermeldungen).
- Empfehlungstexte werden als lokalisierbare Templates gebaut (keine frei codierten Satzfragmente im UI).
- Datums-, Zahlen- und Prozentformate sind locale-sensitiv.
- Einheitliche Darstellung von BTC/sats bleibt fachlich konsistent, nur textliche Umfelder werden lokalisiert.

Nicht-Ziel fuer V1:

- Keine automatische Uebersetzung von externen Freitextquellen.
- Keine vollautomatische Sprachdetektion als harte Vorgabe (optional spaeter).

Technische Leitplanken:

- String-Management nur ueber Translation Keys (keine Hardcoded UI-Texte in Components).
- Namespace-Struktur pro Bereich, z. B. `common`, `wizard`, `simulator`, `recommendations`, `errors`.
- ICU-kompatible Platzhalter fuer dynamische Texte (z. B. Counts, Prozentwerte, Konditionen).
- Versionierte Locale-Dateien und Pflichtpruefung bei Build/CI auf fehlende Keys.

UX-Leitplanken:

- Sprachumschalter in den App-Einstellungen.
- Klare Anzeige der aktiven Sprache.
- Fallback-Hinweis im Debug/Support-Bereich, wenn uebersetzte Keys fehlen.
- Formulierungen fuer Advisory-Hinweise in beiden Sprachen gleich streng und eindeutig.

Qualitaetskriterien fuer V1:

- 100 Prozent lokalisierbare sichtbare UI-Texte in `de` und `en`.
- Keine fehlenden Translation Keys in Produktions-Builds.
- Snapshot-Tests fuer mindestens die Kernansichten in beiden Sprachen.
- Review der kritischen Empfehlungs- und Risikotexte durch fachliche Gegenlese.

## Engine-Design

Start mit heuristischen Scores (erklaerbar, debugbar), spaeter adaptive Gewichtung.

Beispiel Starter-Score:

```
StarterScore =
	0.35 * ConnectivityGain
+ 0.25 * DiversificationScore
+ 0.15 * StabilityProxy
+ 0.15 * CapitalFit
- 0.10 * OverlapPenalty
```

Beispiel Operational-Score:

```
OperationalScore =
	0.30 * FlowAffinity
+ 0.25 * RebalanceSavingsPotential
+ 0.20 * TopologyGain
+ 0.15 * SimilarityToProfitablePeers
- 0.10 * RedundancyRisk
```

## Produkt-Roadmap

## V1 (MVP)

- Starter Wizard
- Peer-Overlap- und Diversity-Analyse
- Kandidatenvergleich
- Empfehlungen mit Begruendung
- Advisory-Only Modus: keine automatischen Node-Aktionen

## V2

- Vertiefte operative Datenintegration
- Peer-Fingerprinting
- Gap Detection (fehlende Direktpeers)
- Lernende Priorisierung
- Verbesserte Explainability inkl. Confidence und Datenabdeckung

## V3

- Wirkungsmessung umgesetzter Empfehlungen
- Advisory fuer Open/Close/Resize
- Erweiterte "What-if"-Szenarien
- Optionaler Export von Aktions-Playbooks (nur als Checkliste, keine Auto-Ausfuehrung)

## Implementierungs-Readiness

Dieser Abschnitt definiert, was vor Entwicklungsstart geklaert und festgelegt wird.

## A) Findings mit Vorschlaegen

1. Security und Berechtigungen sind noch zu allgemein.

Vorschlag:

- Least-Privilege-Zugriff auf LND/LNDG definieren (minimale Read-Rechte fuer Analyse).

Kurze Begruendung:

- Minimiert Risiko und verhindert unbeabsichtigte Schreiboperationen.

2. Advisory-vs-Automation war bisher nicht strikt genug formuliert.

Vorschlag:

- Produktweite Policy "Advisory Only" in UI, API und Doku verankern.

Kurze Begruendung:

- Verhindert Missverstaendnisse und reduziert Betriebsrisiken fuer Nutzerkapital.

3. Es fehlen messbare MVP-Ziele.

Vorschlag:

- KPI-Set fuer V1 definieren (z. B. Akzeptanzrate von Empfehlungen, wahrgenommene Verstaendlichkeit, Overlap-Reduktion in simulierten Plaenen).

Kurze Begruendung:

- Erlaubt objektive Bewertung, ob das Produkt echten Nutzen liefert.

4. Datenmodell und Historisierung sind nicht ausgearbeitet.

Vorschlag:

- Snapshot-Modell festlegen (Inputdaten, Score-Version, erzeugte Empfehlungen, spaetere Outcomes).

Kurze Begruendung:

- Macht Empfehlungen nachvollziehbar und spaetere Verbesserungen messbar.

5. Ausfall- und Degradationslogik fehlt.

Vorschlag:

- Fallback definieren: Wenn LNDG-Daten fehlen, nur Topologie-basierte Analyse mit klarer Kennzeichnung.

Kurze Begruendung:

- App bleibt nutzbar und kommuniziert transparent reduzierte Aussagekraft.

6. Explainability hat noch kein festes Format.

Vorschlag:

- Einheitliche Recommendation Card mit: Warum empfohlen, warum nicht Alternative X, Risiko, Confidence, Datenalter.

Kurze Begruendung:

- Verbessert Trust und erleichtert manuelle Entscheidungen.

7. Teststrategie ist offen.

Vorschlag:

- Replay- und Regressionstests fuer Scoring und Ranking-Reihenfolge definieren.

Kurze Begruendung:

- Verhindert stille Qualitaetsverluste bei spaeteren Aenderungen.

8. Unsicherheit wird im UI noch nicht aktiv gefuehrt.

Vorschlag:

- Jede Empfehlung mit Confidence-Level und Datenabdeckung visualisieren.

Kurze Begruendung:

- Verhindert falsche Sicherheit und setzt Erwartungen realistisch.

9. Mehrsprachigkeit der GUI war nicht spezifiziert.

Vorschlag:

- i18n-Standard fuer V1 definieren (`de` und `en`, Key-basierte Uebersetzungen, Fallback-Regeln, CI-Key-Checks).

Kurze Begruendung:

- Verhindert spaete teure UI-Refactors und sichert konsistente Nutzerfuehrung in beiden Sprachen.

## B) Offene Punkte mit Entscheidungsvorschlag

1. Muss LNDG in V1 bereits Pflicht sein?

Entscheidungsvorschlag:

- Nein, LNDG optional in V1, Pflicht erst ab V2-Funktionen.

Kurze Begruendung:

- Senkt Integrationsaufwand und beschleunigt MVP-Validierung.

2. Welche Persona priorisieren wir fuer die ersten 8 Wochen?

Entscheidungsvorschlag:

- Fokus auf Einsteiger + "early routing" Hybrid-Profile.

Kurze Begruendung:

- Hoher Nutzen durch Erklaerbarkeit und klarer Wizard-Flow.

3. Welche Aktionen bleiben strikt manuell?

Entscheidungsvorschlag:

- Open/Close/Resize, Rebalance und Fee-Aenderungen bleiben vollstaendig manuell.

Kurze Begruendung:

- Entspricht Advisory-Only Scope und reduziert Fehlbedienungsrisiko.

4. Welche Mindestdaten braucht Operational Scoring?

Entscheidungsvorschlag:

- Aktivierung erst ab definierter Datenbasis (z. B. Mindestanzahl Tage und Events), sonst nur Starter/Topology-Modus.

Kurze Begruendung:

- Verhindert verrauschte Empfehlungen bei zu wenig Signal.

5. Wie messen wir V1-Erfolg ohne Auto-Ausfuehrung?

Entscheidungsvorschlag:

- Erfolg ueber qualitative und indirekte Metriken messen: Nutzerfeedback, Empfehlungsklarheit, angenommene manuelle Aktionen, spaetere Vergleichssicht.

Kurze Begruendung:

- Passt zum Advisory-Ansatz und liefert trotzdem belastbares Lernsignal.

6. Wie gehen wir mit Datenausfaellen um?

Entscheidungsvorschlag:

- Harte Degrade-Stufen definieren (vollstaendig, eingeschraenkt, nur Topologie) und im UI klar anzeigen.

Kurze Begruendung:

- Sichert Betriebskontinuitaet und vermeidet falsche Interpretationen.

7. Welche Exporte brauchen Nutzer in V1?

Entscheidungsvorschlag:

- Export von Empfehlungslisten als manuelle Checkliste (CSV/JSON/Markdown), keine API-Execution.

Kurze Begruendung:

- Erleichtert Umsetzung durch Nutzer und haelt Scope risikoarm.

8. Welche Governance fuer Score-Aenderungen gilt?

Entscheidungsvorschlag:

- Versionierte Score-Configs mit Changelog und Vergleichsansicht alter vs. neuer Empfehlungen.

Kurze Begruendung:

- Macht Modellentwicklung kontrollierbar und auditierbar.

9. Wie werden dynamische Empfehlungstexte mehrsprachig erzeugt?

Entscheidungsvorschlag:

- Recommendation Templates mit strukturierten Platzhaltern und regelbasierten Textbausteinen je Sprache.

Kurze Begruendung:

- Ermoeglicht erklaerbare, konsistente Texte ohne manuelle Doppelpflege pro Empfehlung.

10. Welche Sprache ist Default bei Erststart?

Entscheidungsvorschlag:

- Default: Browser/Umgebungs-Locale, Fallback auf Englisch; Nutzer kann jederzeit manuell umstellen.

Kurze Begruendung:

- Verbessert Erstnutzer-Erlebnis und bleibt robust bei unklarer Umgebung.

11. Wie skalieren wir auf weitere Sprachen ab V2/V3?

Entscheidungsvorschlag:

- Neue Sprachen nur nach Abdeckungskriterien freischalten (kritische Flows 100 Prozent, Rest mindestens definierter Schwellenwert).

Kurze Begruendung:

- Verhindert halb uebersetzte UX in sicherheitsrelevanten Entscheidungspfaden.

## C) Mindestanforderungen vor Umsetzungsstart

1. Advisory-Only Policy ist in Doku, API-Contracts und UI-Texten fixiert.
2. Sicherheitsmodell (Read-only soweit moeglich) ist dokumentiert.
3. KPI-Set fuer V1 ist festgelegt.
4. Datenmodell fuer Snapshots und Recommendation-Historie steht.
5. Degradationslogik fuer fehlende Datenquellen ist implementierbar beschrieben.
6. Testplan fuer Scoring/Ranking-Regression ist definiert.
7. i18n-Setup fuer `de`/`en` inkl. Key-Check, Fallback-Regeln und Locale-Tests ist definiert.

## Repo-Strukturvorschlag

```
lnnc/
	umbrel-app.yml
	docker-compose.yml
	exports.sh
	hooks/
		pre-start
		post-update
	backend/
		api/
		collectors/
		scoring/
		graph/
		recommendations/
		jobs/
	frontend/
		app/
		components/
		views/
	data/
	gallery/
```

## Naechste sinnvolle Schritte

1. Umbrel-App-Scaffold anlegen (`umbrel-app.yml`, `docker-compose.yml`, Proxy).
2. Backend-Grundgeruest mit FastAPI + SQLite aufsetzen.
3. Erste Collector-Endpunkte fuer lokale Node-Metriken bauen.
4. Heuristische Scoring-Engine fuer Starter-Mode implementieren.
5. Frontend mit Wizard + Topology-Ansicht als klickbaren MVP erstellen.
