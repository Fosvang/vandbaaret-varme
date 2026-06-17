# Brugervejledning til Centralvarme

Centralvarme er en Home Assistant-loesning til overvaagning af vandbaaren gulvvarme og radiatorzoner. Appen viser temperaturer, aktuatorstatus, komfortstatus, alarmer og historik for op til 8 zoner.

Loesningen styrer ikke setpunkter direkte. Den overvager, om rummene holder de valgte temperaturgraenser, og om gulvvarmeaktuatorer eller radiatorventiler faktisk er aktive.

## Hvad appen kan

- Vise aktuel temperatur for zone 1-8.
- Vise om hver zones aktuator/TRV er aaben eller aktiv.
- Vise om zoner er `OK`, `For kold`, `For varm` eller har `Sensorfejl`.
- Give samlet status for anlaegget.
- Give alarm ved sensorfejl eller rumtemperatur uden for graenser.
- Sende Home Assistant-notifikationer og valgfri SMS.
- Vise temperaturudvikling for 24 timer.
- Vise statistik for 7 dage med middel, minimum og maksimum.
- Vise aktuatorhistorik for 7 dage.

## Filer

```text
home-assistant/
|-- centralvarme_package.yaml
|-- centralvarme_dashboard.yaml
`-- configuration.yaml
```

`centralvarme_package.yaml` opretter helpers, scripts, template-sensorer, binary sensors og automationer.

`centralvarme_dashboard.yaml` er Lovelace-dashboardet med fanerne `Overblik`, `Statistik` og `Opsaetning`.

`configuration.yaml` viser de linjer, der skal ind i Home Assistant `configuration.yaml`.

## Installation

1. Aktiver packages i Home Assistant, hvis det ikke allerede er gjort.
2. Kopier `centralvarme_package.yaml` til din `packages/` mappe.
3. Kopier `centralvarme_dashboard.yaml` til den dashboard-sti, der er angivet i `home-assistant/configuration.yaml`.
4. Tilfoej dashboard-konfigurationen fra `home-assistant/configuration.yaml` til din Home Assistant `configuration.yaml`.
5. Genstart Home Assistant.
6. Aabn dashboardet `Centralvarme`.
7. Gaa til fanen `Opsaetning` og udfyld zonefelterne.

## Configuration snippet

Standardopsaetningen forventer:

```yaml
homeassistant:
  packages: !include_dir_named packages

lovelace:
  dashboards:
    centralvarme-dashboard:
      mode: yaml
      title: Centralvarme
      icon: mdi:home-thermometer
      show_in_sidebar: true
      filename: lovelace/dashboards/centralvarme_dashboard.yaml
```

Hvis dashboardfilen placeres et andet sted, skal `filename` tilpasses.

## Dashboardets faner

### Overblik

Fanen `Overblik` bruges til daglig drift.

Den viser:

- Samlet status.
- Alarmstatus.
- Drift score.
- Valgt anlaegstype.
- Zone 1-8 med status, temperatur og aktuatorstatus.
- Temperaturudvikling for de seneste 24 timer.
- Alarmknapper til kvittering, pause og reset.

### Statistik

Fanen `Statistik` bruges til at forstaa udviklingen over tid.

Den viser:

- Aktuel drift score og status.
- Temperaturstatistik for zone 1-8 over 7 dage.
- Aktuatorhistorik for zone 1-8 over 7 dage.

### Opsaetning

Fanen `Opsaetning` bruges ved installation og vedligeholdelse.

Her indtastes:

- Temperatur-sensor entity for hver zone.
- Aktuator/TRV entity for hver zone.
- Min og maks rumtemperatur.
- Alarm-gentagelsesinterval.
- Driftvalg og anlaegstype.
- Notifikationer og SMS.

## Zoneopsaetning

Hver zone har to felter:

- `input_text.vv_zone_X_temp_sensor`
- `input_text.vv_zone_X_aktuator_entity`

Udskift `X` med zonenummeret 1-8.

Eksempler paa temperatursensorer:

```text
sensor.stue_temperatur
sensor.shelly_blu_ht_stue_temperature
sensor.bad_temperature
```

Eksempler paa aktuatorer:

```text
switch.stue_gulvvarme
switch.bad_termoaktuator
climate.stue_radiator
climate.sovevaerelse_trv
```

Aktuatorstatus regnes som aaben/aktiv naar entity-status er `on`, `open`, `heat`, `heating`, eller naar en `climate.*` entity har attributten `hvac_action: heating`.

## Temperaturgraenser

`Min rumtemperatur`

- Helper: `input_number.vv_zone_temp_min`
- Standard: 18 C
- Bruges til at vurdere om en zone er `For kold`.

`Maks rumtemperatur`

- Helper: `input_number.vv_zone_temp_max`
- Standard: 24 C
- Bruges til at vurdere om en zone er `For varm`.

`Gentag alarm hvert`

- Helper: `input_number.vv_alarm_repeat_min`
- Standard: 30 minutter
- Angiver hvor tit en aktiv, ikke-kvitteret alarm gentages.

## Driftvalg

`Overvaagning aktiv`

- Naar feltet er slaaet til, kan appen oprette alarmer.
- Naar feltet er slaaet fra, bliver fejl stadig vist, men alarmstyring er ikke aktiv.

`Anlaegstype`

- Muligheder: `Gulvvarme`, `Radiatorer`, `Blandet`.
- Bruges som administrativ markering af installationstypen.

`Driftvalg`

- Muligheder: `Normal`, `Spare`, `Ferie`, `Frostsikring`, `Service`.
- `Service` deaktiverer automatisk alarmoprettelse.

## Notifikationer og SMS

`Notifikation service` staar som standard til:

```text
persistent_notification.create
```

Den kan aendres til en Home Assistant notify-service, f.eks.:

```text
notify.mobile_app_min_telefon
```

SMS er valgfrit. Standardmodellen bruger GatewayAPI via `rest_command.vv_gatewayapi_send_sms`. Hvis SMS aktiveres, skal `gatewayapi_authorization_header` findes i `secrets.yaml`.

Flere SMS-modtagere kan adskilles med komma, semikolon eller linjeskift.

## Automatiske entities

Appen opretter temperatursensorer:

```text
sensor.vv_zone_1_temperatur
...
sensor.vv_zone_8_temperatur
```

Appen opretter zonestatusser:

```text
sensor.vv_zone_1_status
...
sensor.vv_zone_8_status
```

Appen opretter aktuatorstatusser:

```text
binary_sensor.vv_zone_1_aktuator_aaben
...
binary_sensor.vv_zone_8_aktuator_aaben
```

Samlede system-entities:

- `sensor.vv_status`
- `sensor.vv_alarm_arsag`
- `sensor.vv_anbefaling`
- `sensor.vv_drift_score`
- `binary_sensor.vv_alarm_aktiv`
- `binary_sensor.vv_sensorfejl`
- `binary_sensor.vv_zone_temperatur_alarm`
- `binary_sensor.vv_zone_aktuator_aktiv`

## Alarmer

Appen opretter alarm, naar en fejl har vaeret aktiv i 2 minutter.

Alarm kan oprettes ved:

- Sensorfejl.
- En zone er for kold.
- En zone er for varm.

Alarm nulstilles automatisk, naar fejlene har vaeret vaek i 5 minutter.

Alarm gentages efter det interval, der er valgt i `Gentag alarm hvert`, hvis:

- Overvaagning er aktiv.
- Alarm er aktiv.
- Alarm ikke er kvitteret.
- Alarm ikke er paa pause.

## Knapper

`Kvitter`

- Kalder `script.vv_alarm_kvitter`.
- Slaar `input_boolean.vv_alarm_ack` til.
- Bruges naar brugeren har set alarmen.

`Pause 2t`

- Kalder `script.vv_alarm_pause_2h`.
- Slaar `input_boolean.vv_alarm_muted` til.
- Starter `timer.vv_alarm_mute_2h`.

`Reset`

- Kalder `script.vv_alarm_reset`.
- Slaar alarm, kvittering og pause fra.
- Stopper pausetimeren.

`Test alarm`

- Kalder `script.vv_alarm_test`.
- Sender en testbesked via den valgte notifikationsopsaetning.

`Test SMS`

- Kalder `script.vv_sms_test`.
- Sender en testbesked via SMS, hvis SMS er aktiv og korrekt opsat.

## Anbefalet opsaetning

1. Udfyld zone 1 og zone 2 foerst.
2. Kontroller at temperaturerne vises korrekt i `Overblik`.
3. Kontroller at aktuatorstatus skifter naar der kaldes paa varme.
4. Udfyld zone 3-8 efter behov.
5. Juster min og maks rumtemperatur.
6. Aktiver notifikationer.
7. Aktiver SMS, hvis det er noedvendigt.

## Fejlfinding

Hvis en zone viser `Sensorfejl`:

- Kontroller at entity ID er stavet korrekt.
- Kontroller at sensoren findes i Home Assistant.
- Kontroller at sensoren leverer en numerisk temperatur.
- Kontroller at ubrugte zonefelter enten udfyldes med gyldige entities eller fjernes/tilpasses i pakken.

Hvis aktuator altid viser `off`:

- Kontroller at aktuatorfeltet peger paa korrekt `switch.*`, `binary_sensor.*` eller `climate.*`.
- Kontroller om TRV'en bruger attributten `hvac_action`.
- Kontroller om Shelly-switch faktisk skifter til `on`.

Hvis alarm ikke sendes:

- Kontroller at `Overvaagning aktiv` er slaaet til.
- Kontroller at `Driftvalg` ikke staar paa `Service`.
- Kontroller at `Notifikation aktiv` er slaaet til.
- Kontroller at `Notifikation service` er korrekt.

Hvis SMS ikke sendes:

- Kontroller at `SMS aktiv` er slaaet til.
- Kontroller at `SMS status` viser `Klar`.
- Kontroller at `SMS modtagere` er udfyldt.
- Kontroller GatewayAPI secret og rest_command i `configuration.yaml`.

## Begraensninger og forbedringsforslag

- Appen styrer ikke setpunkter direkte; den overvaager temperatur og aktuatorstatus.
- Statistik kraever at Home Assistant recorder gemmer historik for sensorerne.
- Alle 8 zoner forventes som udgangspunkt at have gyldige temperatur-entities. En god naeste forbedring er at tilfoeje `input_boolean.vv_zone_X_aktiv`, saa ubrugte zoner kan deaktiveres uden at give sensorfejl.
