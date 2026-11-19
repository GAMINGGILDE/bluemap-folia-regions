# Folia 26.3 mit BlueMap prüfen

## Testumgebung

Version 2.0.2 wurde mit Temurin 25.0.5+8, Gradle 9.4.1 und dem Dev-Bundle `26.3.build.26-stable` gebaut. Der lokale Testserver verwendete Folia `26.3-26-eca40f9` und `bluemap-5.23-paper.jar`. Die Prüfsumme der Server-JAR entsprach dem veröffentlichten Build. [Folia Build 26](https://fill.papermc.io/v3/projects/folia/versions/26.3/builds/26) · [BlueMap 5.23](https://github.com/BlueMap-Minecraft/BlueMap/releases/tag/v5.23)

## Durchgeführt

- Der saubere Gradle-Build führte alle 66 Tests erfolgreich aus. Der Signaturtest verwendet die echte Klasse `RegionScheduleHandle` des Bundles für alle fünf Zeitfenster. Ein Negativtest prüft die Ablehnung eines gleichnamigen Aufrufs mit falschem Rückgabetyp.
- Der Bytecode der gebauten JAR und die Methoden des Servers verwenden für alle fünf Aufrufe den Deskriptor `(J)Lca/spottedleaf/common/time/TickData$TickReportData;`. Die Messwerttests decken die Umrechnung von Nanosekunden in Millisekunden, die langsamsten 1 % und 5 %, fehlende Reports, null gesammelte Ticks und eine Einzelmessung ab.
- Auf dem Server wurden die von BlueMap ausgegebenen Marker in Oberwelt, Nether und Ende geprüft. Für jedes der fünf Fenster enthielten sie Regionsumrisse, Farben, TPS, MSPT, Auslastung, Tickspitzen und steigende Zeitstempel. Die Zuordnung wurde mit unterschiedlichen, gezielt geladenen Chunk-Koordinaten je Dimension geprüft.
- Beim Wechsel zwischen den Oberwelt-Chunks `1024, 1024` und `0, 0` erschien die neue Region. Nach dem Entladen verschwand der alte Marker. Die gezielt geladenen Regionen in Nether und Ende behielten ihre Marker-IDs.
- `/bmfr reload`, `/bmfr refresh` und `/bmfr status` funktionierten mit allen fünf Fenstern. Nach `/bluemap reload` aktualisierten sich die Marker aller drei Maps wieder automatisch. Der Status meldete drei geplante Maps. Im Testlauf trat kein Report-Linkagefehler auf.

Die Prüfung las BlueMaps serialisierte Markerdaten. Die visuelle Kontrolle im Browser sowie ein Dauerlauf mit vollständig gefüllter 15-Minuten-Historie stehen aus.

## Auf einem Testserver wiederholen

1. Einen separaten Serverordner mit Folia Build 26, Java 25 und den beiden Plugin-JARs anlegen. Den Server starten, BlueMap für die drei Standarddimensionen einrichten und warten, bis BlueMap geladen ist. Für eine Prüfung über Dateien in BlueMaps `plugin.conf` vorübergehend `write-markers-interval: 1` setzen und `/bluemap reload` ausführen.
2. In der Testserver-Konsole je Dimension einen Chunk laden. Die Befehle verwenden Blockkoordinaten.

   ```text
   execute in minecraft:overworld run forceload add 0 0
   execute in minecraft:the_nether run forceload add 1600 1600
   execute in minecraft:the_end run forceload add 3200 3200
   ```

3. In `plugins/BlueMap-Folia-Regions/config.yml` unter `visualization.report-window` nacheinander `5s`, `15s`, `1m`, `5m` und `15m` setzen. Jeweils `/bmfr reload`, `/bmfr refresh` und nach dem Update `/bmfr status` ausführen. Der Status soll das gewählte Fenster, drei geplante Maps und aktuelle Zeitstempel anzeigen.
4. Im Overlay oder in `bluemap/web/maps/<map>/live/markers.json` das Set `folia-regions` prüfen. Die Details sollen Welt, gewähltes Fenster und Messwerte enthalten. Das Feld „Stand“ muss bei der nächsten Aktualisierung fortschreiten. Die Testchunks liegen bei `0, 0`, `100, 100` und `200, 200`.
5. Mit `execute in minecraft:overworld run forceload add 16384 16384` eine zweite, entfernte Region laden. Nach Erscheinen ihres Markers mit `execute in minecraft:overworld run forceload remove 0 0` den alten Chunk freigeben. Sobald Folia ihn entlädt, soll sein Marker verschwinden. Die Marker für die gezielt geladenen Nether- und Ende-Chunks müssen ihren Dimensionen zugeordnet bleiben.
6. `/bluemap reload` ausführen. Nach der Lademeldung erneut Status, manuelle Aktualisierung und den nächsten automatischen Zeitstempel prüfen. Es soll wieder genau eine geplante Aktualisierung je Map geben. Das Serverlog auf `NoSuchMethodError` und fehlgeschlagene Marker-Aktualisierungen prüfen.
7. Auf dem Testserver die drei verbliebenen Tickets mit `forceload remove` an den jeweiligen Koordinaten in ihrer Dimension freigeben. Das gewünschte Report-Fenster und BlueMaps Schreibintervall wieder einstellen.
