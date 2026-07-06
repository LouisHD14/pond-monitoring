# Teich-Monitoring

Monitoring-Dashboard für die Wasserwerte eines Gartenteichs: Temperatur, pH-Wert,
gelöster Sauerstoff, Wasserhärte (GH), Karbonathärte (KH), Nitrat und Nitrit.
Aufgebaut analog zum bestehenden Solar-Monitoring-Setup: Frontend und Backend
sind getrennt deploybar.

## Status: Phase 1 — Design-Prototyp mit Demo-Daten

Diese Phase enthält **ausschließlich das Frontend als statische Seite** mit
plausiblen, clientseitig generierten Beispieldaten. Es gibt kein Backend, keine
Datenbank und keinen API-Abruf — die Seite kann direkt lokal im Browser geöffnet
werden.

### Ausprobieren

```
frontend/index.html im Browser öffnen — mehr ist nicht nötig.
```

Alternativ (z. B. fürs spätere Nginx-Muster):

```bash
cd frontend && python3 -m http.server 8080
# → http://localhost:8080
```

### Was der Prototyp zeigt

- **Live-Kacheln** je Messgröße: große Zahl + Einheit + Icon, Änderung gegenüber
  Vortag, 24-h-Sparkline und Grenzwert-Status (Normalbereich / Warnung)
- **Verlaufscharts** je Messgröße mit Zeitraum-Umschalter:
  - **7 Tage** — 1 Datenpunkt pro Stunde (168 Punkte)
  - **31 Tage** — 1 Datenpunkt pro Tag, jeweils der 12:00-Uhr-Wert
- Grenzwertlinien im Chart (z. B. Sauerstoff-Minimum), Fadenkreuz-Tooltip
  (Maus + Tastatur), Datentabelle je Chart, helles + dunkles Farbschema
  (Teich-/Wasserpalette), responsiv für Desktop und Mobile
- Keine externen Abhängigkeiten (kein CDN) — Charts sind handgebautes SVG,
  die Seite funktioniert komplett offline

### Demo-Daten

Die Fake-Daten werden deterministisch (geseedet) generiert und simulieren einen
realistischen Verlauf: Tagesgang mit Nachmittags-Maximum plus langsamer,
mehrtägiger Drift (Wetterlage) — keine strukturlosen Zufallswerte.
Die Reihen liegen **bereits im Format der späteren Zielstruktur** vor
(`live`, `d7` stündlich, `d31` täglich 12:00 Uhr), sodass in Phase 2 nur die
Datenquelle getauscht wird, nicht die Chart-Logik.

### Neue Messgröße ergänzen

Messgrößen sind konfigurationsgetrieben: In `frontend/index.html` einen Eintrag
zum Array `SENSORS` hinzufügen (Label, Einheit, Farbe, Grenzwerte, Icon) —
Kachel, Charts, Tooltip und Tabelle entstehen automatisch. So wurden bereits
Wasserhärte, Karbonathärte, Nitrat und Nitrit ergänzt; vorgesehen z. B. auch
für Redoxpotential oder Wasserstand.

## Spätere Phasen (nicht Teil dieses Stands)

| Phase | Inhalt |
|---|---|
| 2 | ESP32 liest Sensoren (Temperatur, pH, O₂) und sendet per **MQTT** |
| 2 | Backend/Persistenz: **InfluxDB** (ggf. Node-RED) für Speicherung & Aggregation |
| 2 | Frontend von Demo-Daten auf API-/Datenbank-Anbindung umstellen |
| 3+ | Optional: Alarmierung, weitere Sensoren, Steuerungsfunktionen |

**Deployment-Ziel:** Linux (Raspberry Pi/VPS), Frontend als statische Dateien
hinter **Nginx** (Reverse Proxy), Backend als eigener Service — gleiches Muster
wie das Solar-Monitoring.

## Struktur

```
pond-monitoring/
├── frontend/
│   └── index.html   # komplette Seite (HTML + CSS + JS, self-contained)
└── backend/         # folgt in Phase 2 (MQTT → InfluxDB → API)
```
