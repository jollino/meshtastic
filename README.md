# Meshtastic, ma semplice
## di Jollino


### Hardware

Dispositivi provati personalmente:

- Heltec V3
- LilyGo T3S3 (in arrivo)
- Seeed Xiao nrf + SX1262 (in arrivo)

Antenne:

- Antennine di serie dell'Heltec V3 (~2 dBi?)
- Antennina CDEBYTE TX868-JZ-5 (2 dBi)
- Antenna a banana CDEBYTE TX868-JKD-20 (3 dBi)
- Antenna a molla CDEBYTE TX868-XPL-100 (3.5 dBi) (in arrivo)
- Antenna jpole ProDrone.pl (2.5 dBi)
- Antenna moxon ProDrone.pl (5.5 dBi) (in arrivo)

### Configurazione Meshtastic

```yaml
# start of Meshtastic configure yaml
channel_url: https://meshtastic.org/e/#CgsSAQEoATABOgIIEAozEiAVU20RKW-Nyl6vhOYzO3gG4v27yFaPiWtO8KyRCo6geRoHQWJydXp6bygBMAE6AggQCjASIBVTbREpb43KXq-E5jM7eAbi_bvIVo-Ja07wrJEKjqB5GgRUZXN0KAEwAToCCA4SEQgBEAQ4A0AHSAFQG2gByAYB
config:
  bluetooth:
    enabled: true
    fixedPin: 123456
  device:
    ledHeartbeatDisabled: true
    nodeInfoBroadcastSecs: 3600
    tzdef: CET-1CEST,M3.5.0/2:00:00,M10.4.0/3:00:00
  display:
    screenOnSecs: 30
  lora:
    configOkToMqtt: true
    hopLimit: 5
    modemPreset: MEDIUM_FAST
    region: EU_868
    sx126xRxBoostedGain: true
    txEnabled: true
    txPower: 27
    usePreset: true
  mqtt:
    encryptionEnabled: false
  network:
    enabledProtocols: 1
    ntpServer: meshtastic.pool.ntp.org
  position:
    broadcastSmartMinimumDistance: 100
    broadcastSmartMinimumIntervalSecs: 30
    gpsMode: ENABLED
    gpsUpdateInterval: 600
    positionBroadcastSecs: 3600
    positionBroadcastSmartEnabled: true
    positionFlags: 235
  power:
    lsSecs: 300
    minWakeSecs: 10
    sdsSecs: 4294967295
    waitBluetoothSecs: 60
  security:
    adminKey:
    - 'base64:'
    - 'base64:'
    - 'base64:'
    serialEnabled: true
module_config:
  mqtt:
    address: mqtt.meshtastic.org
    encryptionEnabled: true
    password: large4cats
    root: msh/EU_868
    username: meshdev
  telemetry:
    deviceUpdateInterval: 3600
    environmentUpdateInterval: 3600
```

La configurazione non include i campi `config.security.privateKey`, `config.security.publicKey`, `location`, `owner` e `owner_short`. Le chiavi vengono generate automaticamente, la posizione va configurata sul posto, e i callsign breve e lungo vanno scelti per identificarsi.

**Le chiavi vanno tenute da parte perché in caso di reset vanno reinserite, altrimenti gli altri nodi che ci hanno in cache non ci riconoscono e ci ignorano**

continua...