# BlueMap Folia Regions

Zeigt Folia-Tick-Regionen als übersichtliches Marker-Overlay in **BlueMap** an.

Version **2.0.2** ist für **Folia 26.3 Build 26** (`eca40f9`) vorgesehen.

<img width="809" height="502" alt="Folia-Regionen in BlueMap" src="https://github.com/user-attachments/assets/3153a9f2-dbf4-4259-bbf9-d4fbbef9c754" />

## Features

- Exakte Regionsumrisse einschließlich konkaver Flächen, Löcher und getrennter Teilflächen
- Regionsdaten wie Zentrum, Sektionen, Chunks, Fläche, Entitäten und Spieler
- TPS, MSPT, Tickspitzen und Auslastung für verschiedene Zeitfenster
- Konfigurierbare Heatmap nach Auslastung, MSPT oder TPS
- Kurzzeit-Trends, Warnzustandsdauer und kompakte Ursachenanalyse
- Belastungskontext für hohe Entitätsdichte und ungewöhnlich große Regionen
- Kompakte, für Desktop und Mobilgeräte geeignete Detailansicht
- Laufzeitstatus, sofortige Aktualisierung und Reload per Befehl

## Voraussetzungen

- **Folia 26.3 Build 26** mit **Java 25**
- **BlueMap** für Paper/Folia (getestet mit **5.23**, Build-Abhängigkeit BlueMapAPI **2.7.8**)

Für einen lokalen Build werden zusätzlich **JDK 25** und der mitgelieferte Gradle-Wrapper benötigt.

## Installation

1. BlueMap installieren und einmal starten, damit die Maps angelegt werden.
2. Den Server stoppen. Bei einem Update die bisherige BlueMap-Folia-Regions-JAR aus `plugins/` entfernen und die vorhandene `config.yml` behalten.
3. `BlueMap-Folia-Regions-2.0.2.jar` in `plugins/` legen und den Server starten.
4. Nach dem Laden von BlueMap mit `/bmfr refresh` aktualisieren. Mit `/bmfr status` Version, geplante Maps und den letzten Aktualisierungszeitpunkt je Map prüfen.
5. In BlueMap unter „Marker“ das Overlay „Folia Tick-Regionen“ aktivieren und in einer geladenen Region den Zeitstempel „Stand“ über zwei Aktualisierungen vergleichen.

## Konfiguration

Beim ersten Start wird `plugins/BlueMap-Folia-Regions/config.yml` erstellt. Die wichtigsten Einstellungen:

```yaml
update-interval-seconds: 5

visualization:
  mode: utilization       # static, utilization, mspt oder tps
  report-window: 15s      # 5s, 15s, 1m, 5m oder 15m

trends:
  enabled: true

load-context:
  enabled: true

marker-set:
  default-hidden: true
```

Die vollständige Beschreibung aller Einstellungen, Schwellenwerte, Farben und Formatplatzhalter befindet sich in der [Konfigurationsreferenz](docs/CONFIGURATION.md).

## Befehle

| Befehl | Beschreibung |
| --- | --- |
| `/bmfr reload` | Konfiguration neu laden |
| `/bmfr refresh` | Marker aller BlueMap-Maps sofort neu berechnen |
| `/bmfr status` | Verbindungs-, Regions-, Marker- und Laufzeitstatus anzeigen |

Die Berechtigungen heißen entsprechend `bluemapfoliaregions.reload`, `bluemapfoliaregions.refresh` und `bluemapfoliaregions.status`. Sie sind standardmäßig Operatoren vorbehalten.

## Dokumentation

- [Konfiguration und Formatplatzhalter](docs/CONFIGURATION.md)
- [Integrationstest für Folia 26.3](docs/INTEGRATION.md)
- [Release mit Git-Tags erstellen](docs/RELEASE.md)

## Build

```bash
./gradlew clean build
```
