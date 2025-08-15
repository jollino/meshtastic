# Appunti su Meshtastic
## by Jollino, ultimo aggiornamento 2025-08-15


### Hardware

#### Dispositivi provati personalmente:

- Heltec V3
- LilyGo T3S3 (in arrivo)
- Seeed Xiao nrf + SX1262 (in arrivo)

#### Antenne:

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

Salvare in un file e caricarlo su un nodo fresco con `meshtastic --config file.yaml`.

La configurazione _non_ include i campi `config.security.privateKey`, `config.security.publicKey`, `location`, `owner` e `owner_short`. Inoltre l'accesso diretto al server pubblico MQTT è disattivato, per concentrarci sulla rete via radio.

Le chiavi vengono generate automaticamente, la posizione va configurata sul posto, e i callsign breve e lungo vanno scelti per identificarsi. **Vanno tenute da parte perché in caso di reset vanno reinserite, altrimenti gli altri nodi che ci hanno in cache non ci riconoscono e ci ignorano!** Il modo più semplice per fare un backup è usare `meshtastic --export-config`.

### Come testare la copertura

La scoperta degli altri nodi avviene quando i nodi trasmettono il proprio pacchetto nodeInfo, e con la configurazione di sopra avviene ogni ora. Alcuni nodi fissi possono trasmetterlo ogni tre ore. L'ideale sarebbe quindi lasciare il nodo fermo per qualche ora e vedere se e cosa si riceve.

In realtà si può fare subito un test semplicemente inviando un messaggio al canale primario, che di norma — ma non sempre — è configurato sugli altri nodi. Se si riceve un ack, significa che almeno qualcuno ci ha ricevuto; se non si riceve niente ("Max retransmission reached"), non ci ha ricevuto nessuno.

Gli ack ricevuti, o gli altri messaggi di telemetria, _non_ sono sufficienti a identificare completamente un nodo. Per quello è necessario aspettare il pacchetto nodeInfo.

Quando un nodo compare nella lista, è possibile usare la funzione _Traceroute_ per verificare la copertura effettiva: il nodo rimane salvato nella lista per un tempo configurabile nell'app, quindi possiamo andare in un altro posto e provare un traceroute. La risposta del trace si interpreta così:

- Se riceviamo una risposta e abbiamo un link diretto, possiamo vedere la potenza con cui ci ha ricevuto il nodo in questione e la potenza con cui noi abbiamo ricevuto lui.
- Se riceviamo una risposta e ci sono nodi intermedi, possiamo vedere la potenza con cui ogni nodo ha ricevuto il precedente, in entrambe le direzioni.
- Se non riceviamo risposta, le possibilità sono tre:
	- Il nodo non ci riceve, quindi non saprà mai che l'abbiamo interpellato
	- Il nodo riceve noi ma noi non riceviamo lui, quindi non sapremo mai che ha provato a rispondere
	- Noi non vediamo il nodo, e il nodo non vede noi: buio totale
	
La qualità del segnale (SNR) nei trace è segnalata in dB, su una scala che va da -12 dB (pessimo) a +12 dB (ottimo).

Ogni nodeInfo aggiorna anche la potenza (RSSI) in dB, oltre alla qualità del link (RSSI) in dB, che va da -125 dB (pessimo) a -30 dB (ottimo).

**RSSI e SNR misurano cose diverse!**

| RSSI | SNR | Segnale |
|-|-|-|
| Alto | Alto | Forte e chiaro |
| Alto | Bsaso | Forte ma sporco |
| Basso | Alto | Debole ma chiaro |
| Basso | Basso | Debole ma sporco |

Principalmente guardiamo l'SNR, perché Meshtastic (e in generale LoRa) è proprio progettato per funzionare con potenze basse e link deboli.