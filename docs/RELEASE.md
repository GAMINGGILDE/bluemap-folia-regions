# Release erstellen

Dieses Projekt baut die Plugin-JAR automatisch, sobald ein GitHub Release veröffentlicht wird. Der Workflow [`.github/workflows/release.yml`](../.github/workflows/release.yml) checkt den Release-Tag aus, baut das Plugin mit Java 25 und lädt die fertige JAR als Asset in dasselbe GitHub Release hoch.

## Versionierung

- Release-Tags verwenden das Format `vX.Y.Z`, beispielsweise `v2.0.2`.
- Release Candidates verwenden beispielsweise `v2.1.0-rc.1`.
- Die Plugin-Version und der JAR-Dateiname werden beim Release-Build aus dem Tag abgeleitet.
- Aus `v2.0.2` wird intern `2.0.2`.
- Das Release-Asset heißt entsprechend `BlueMap-Folia-Regions-2.0.2.jar`.
- `plugin.version` in `gradle.properties` ist die Standardversion für lokale Builds ohne `releaseVersion`.

## Vor dem Release testen

Die folgenden Beispiele bauen Version `2.0.2` für Folia 26.3 Build 26 aus `main`. Der Release-Workflow baut den getaggten Commit.

Windows PowerShell:

```powershell
git checkout main
git pull --ff-only
& .\gradlew.bat clean build '-PreleaseVersion=2.0.2'
```

Linux/macOS:

```bash
git checkout main
git pull --ff-only
./gradlew clean build -PreleaseVersion=2.0.2
```

Die lokale Test-JAR liegt anschließend unter:

```text
build/libs/BlueMap-Folia-Regions-2.0.2.jar
```

## Release per GitHub CLI

Beispiel für Version `2.0.2`:

```powershell
git checkout main
git pull --ff-only
& .\gradlew.bat clean build '-PreleaseVersion=2.0.2'
git tag -a v2.0.2 -m "Release v2.0.2"
git push origin v2.0.2
gh release create v2.0.2 --title "BlueMap Folia Regions v2.0.2" --notes "Release v2.0.2"
```

Nach `gh release create` startet GitHub Actions automatisch den Workflow `Release`. Sobald der Workflow abgeschlossen ist, enthält das GitHub Release die Datei `BlueMap-Folia-Regions-2.0.2.jar`.

## Release über die GitHub-Weboberfläche

1. Lokal sicherstellen, dass `main` aktuell ist.
2. Lokal mit `.\gradlew.bat clean build "-PreleaseVersion=X.Y.Z"` testen.
3. Den annotierten Tag erstellen: `git tag -a vX.Y.Z -m "Release vX.Y.Z"`.
4. Den Tag pushen: `git push origin vX.Y.Z`.
5. Auf GitHub im Repository den Bereich `Releases` öffnen.
6. `Draft a new release` auswählen.
7. Den zuvor gepushten Tag `vX.Y.Z` auswählen.
8. Titel und Release Notes eintragen.
9. `Publish release` auswählen.
10. Warten, bis der Workflow `Release` abgeschlossen ist.

## Workflow überwachen

Mit GitHub CLI:

```powershell
$runId = gh run list --workflow release.yml --limit 1 --json databaseId --jq '.[0].databaseId'
gh run watch $runId
```

Alternativ kann der Workflow auf GitHub unter `Actions` → `Release` geöffnet werden.

## Release-Asset erneut bauen

Falls die JAR für einen bestehenden Release erneut gebaut und hochgeladen werden muss:

```powershell
gh workflow run release.yml -f tag=v2.0.2
```

Der manuell gestartete Workflow checkt den angegebenen Tag aus und überschreibt das gleichnamige Release-Asset.

## Tag vor dem Veröffentlichen prüfen

```powershell
git show --no-patch --decorate v2.0.2
git tag --points-at HEAD
```

Ein noch nicht gepushter, fehlerhafter Tag kann lokal entfernt werden:

```powershell
git tag -d v2.0.2
```

Ein bereits veröffentlichtes Release-Tag sollte nicht verschoben werden. Korrekturen werden stattdessen als neue Patch-Version, beispielsweise `v2.0.3`, veröffentlicht.
