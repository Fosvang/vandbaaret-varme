# Vandbaaret Varme

Home Assistant-loesning til overvaagning af vandbaaren gulvvarme og radiatorzoner.

Loesningen giver et samlet dashboard for temperaturer, aktuatorstatus, komfortalarmer, historik, notifikationer og valgfri SMS. Den er bygget til op til 8 zoner, hvor hver zone kan pege paa egne Home Assistant entities for rumtemperatur og aktuator/TRV.

## Funktioner

- Overblik over zone 1-8.
- Automatisk statussensor pr. zone: `OK`, `For kold`, `For varm` eller `Sensorfejl`.
- Aktuatorstatus for `switch.*`, `binary_sensor.*` og `climate.*` entities.
- Samlet driftstatus og drift score.
- Alarm ved sensorfejl eller temperatur uden for valgte graenser.
- Kvittering, pause og reset af alarm.
- Home Assistant-notifikationer og valgfri SMS via GatewayAPI.
- Temperaturhistorik og statistikgrafer.

## Understoettet udstyr

Gulvvarme pr. basiszone:

- 240/24V 2A omformer
- Shelly BLU H&T Display ZB
- Shelly 1PM Gen4
- Shelly Plus Add-on
- DS18B20 temperatur sensor
- Termoaktuator Neotherm 24V
- RS PRO polykarbonat/ABS kapsling

Gulvvarme ekstra zoner:

- Shelly BLU H&T Display ZB
- Shelly 1PM Gen4
- Termoaktuator Neotherm 24V

Radiatorzoner:

- Shelly BLU H&T Display ZB
- Shelly BLU TRV radiatorstyring
- Gateway

## Filer

```text
VandbaaretVarme/
|-- README.md
|-- docs/
|   `-- Brugervejledning.md
`-- home-assistant/
    |-- configuration_snippet.yaml
    |-- centralvarme_dashboard.yaml
    `-- centralvarme_package.yaml
```

## Installation

1. Kopier `home-assistant/centralvarme_package.yaml` til Home Assistant `packages/`.
2. Kopier `home-assistant/centralvarme_dashboard.yaml` til `lovelace/dashboards/centralvarme_dashboard.yaml`, eller tilpas stien i `configuration_snippet.yaml`.
3. Tilfoej indholdet fra `home-assistant/configuration_snippet.yaml` til din `configuration.yaml`.
4. Genstart Home Assistant.
5. Aabn dashboardet `Vandbaaret Varme`.
6. Udfyld sensor- og aktuatorfelter i fanen `Opsaetning`.

## Typiske entity IDs

- Rumtemperatur: `sensor.stue_temperatur`
- Gulvvarmeaktuator/Shelly 1PM: `switch.stue_gulvvarme`
- Radiator TRV/climate: `climate.stue_radiator`

Hvis en zone bruger radiator/TRV, saet aktuatorfeltet til TRV'ens `climate.*` entity. Hvis en zone bruger gulvvarme, saet aktuatorfeltet til Shelly-switchens `switch.*` entity.

## Dokumentation

Den fulde brugervejledning findes i [docs/Brugervejledning.md](docs/Brugervejledning.md).

## Validering

Filerne er testet med YAML-parser og krydsreferencekontrol mellem dashboard og package. Home Assistant-specifikke tags som `!include_dir_named` skal valideres endeligt med Home Assistants egen konfigurationskontrol.
