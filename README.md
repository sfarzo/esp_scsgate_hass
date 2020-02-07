# esp_scsgate_hass
Questa breve guida dovrebbe aiutare chi vuole integrare la scheda WIFI  [ESP_SCSGATE](http://guidopic.altervista.org/alter/esp_scsgate.html)  con il software [Home-assistant](https://home-assistant.io/)

Per integrare questa scheda con Home-assistant ho usato il software [scsgate](https://github.com/terminet85/scsgate)  di **@termine85** . Questa versione estende il software originale scritto per la scheda scsgate con connessione seriale e aggiunge il supporto al modulo wifi. Qui la versione [originale](https://github.com/flavio/scsgate) .

## Requisiti
Gli unici requisti sono: 
* una scheda [ESP_SCSGATE](http://guidopic.altervista.org/alter/esp_scsgate.html) installata e funzionante 
* Home-assistant installato e funzionante

La mia versione di Home-assistant installata al momento della scrittura di questa guida è: **0.74.1** . E' installata su un Raspberry Pi, in virtual-env, come descritto in questa [guida](https://www.home-assistant.io/docs/installation/raspberry-pi/). Le istruzioni che seguono quindi, andranno adattate ad eventuali modalità di installazione differenti.

## Installazione
1) Prima di tutto stoppiamo l'istanza di Home-assistant in esecuzione:
``` 
$ sudo systemctl stop home-assistant@homeassistant 
``` 

2) Attiviamo il virtual-env:
``` 
$ sudo -u homeassistant -H -s
$ source /srv/homeassistant/bin/activate 
$ cd /home/homeassistant
 ```
 
3) Scarichiamo scsgate con le modifiche per la versione ESP_SCSGATE:
 ``` 
$ wget https://github.com/terminet85/scsgate/archive/new/hardware.zip
$ unzip hardware.zip
 ```
 Tutto il sofwtare da installare dovrebbe trovarsi nella dir: *scsgate-new-hardware/* .
 
4) Installiamo le librerie necessarie e scsgate:
 ``` 
 $ pip install -r scsgate-new-hardware/requirements.txt
 $ pip install scsgate-new-hardware/
 ```
 
5a) (per versioni <=0.87.1) E' necessario sostituire un file all'interno di Home-assistant. Il file si chiama *scsgate.py* e nella mia installazione si trova nel path: */srv/homeassistant/lib/python3.5/site-packages/homeassistant/components/*
 ``` 
 $ cd /home/homeassistant 
 $ wget https://raw.githubusercontent.com/terminet85/home-assistant/d4c2813a2b4c82ae56b4ecbf6f154ddd56fcf79c/homeassistant/components/scsgate.py
 $ cd /srv/homeassistant/lib/python3.5/site-packages/homeassistant/components/
 $ mv scsgate.py scsgate.py.org
 $ cp /home/homeassistant/scsgate.py /srv/homeassistant/lib/python3.5/site-packages/homeassistant/components/
 ```
 5b) (per versioni >=0.88.0) E' necessario sostituire un file all'interno di Home-assistant. Il file si chiama *__init__.py* e nella mia installazione si trova nel path: */srv/homeassistant/lib/python3.5/site-packages/homeassistant/components/scsgate/__init__.py*
 ``` 
 $ cd /home/homeassistant 
 $ wget https://github.com/vinciop/home-assistant/blob/dev/homeassistant/components/scsgate/__init__.py
 $ cd /srv/homeassistant/lib/python3.5/site-packages/homeassistant/components/scsgate/
 $ mv __init__.py __init__.py.org
 $ cp /home/homeassistant/__init__.py /srv/homeassistant/lib/python3.5/site-packages/homeassistant/components/scsgate/
 ```
 6) Riavviamo Home-assistant:
```
$ sudo systemctl start home-assistant@homeassistant
``` 

## Configurazione
A questo punto tutto dovrebbe essere pronto per configurare Home-assistant per integrarsi con la scheda [ESP_SCSGATE](http://guidopic.altervista.org/alter/esp_scsgate.html)

La prima cosa da fare è trovare gli id degli attuatori BTICINO che gestiscono luci e tapparelle. Nella versione originale del software *scsgate* è presente uno script python (scs-monitor) che dovrebbe generare in automatico parte della configurazione, tuttavia funziona solo per la versione con connessione seriale. 

Per il modulo WIFI ho recuparto gli ID usando il software di test  [knxscsgate_v5.0.zip](http://guidopic.altervista.org/alter/software-pc-demo-per-knxgate-e-scagate.html) , come indicato in questa [guida](http://guidopic.altervista.org/esp_scsgate/espscsgate.pdf).

Questo è un esempio di configurazione da mettere nel file configuration.yaml. 
Nei campi *device* e *port* bisogna indicare l'ip e la porta UDP del device [ESP_SCSGATE](http://guidopic.altervista.org/alter/esp_scsgate.html)

```
scsgate:
  device: 192.168.88.24
  port: 52056

cover:
  - platform: scsgate
    devices:
      sala_finestra:
              name: Sala Finestra
              scs_id: 22
      sala_porta:
              name: Sala Porta
              scs_id: 21
      cucina_finestra:
              name: Cucina Finestra
              scs_id: 15
```

## Note Finali
Mi sono accorto che, aggiornando Home-assistant alla versione successiva, viene ripristinata la versione originale del file */srv/homeassistant/lib/python3.5/site-packages/homeassistant/components/scsgate.py* . 
Bisonga quindi ripetere il *punto 5.* di questa guida, dopo ogni aggiornamento.

## Ringraziamenti
La scheda che ha prodotto [Guido](http://guidopic.altervista.org/alter/chisiamo.html) ha un rapporto qualità/prezzo incredibile. Mi ha permesso di risolvere in maniera rapida e ordinata una necessità familiare non banale. Inoltre Guido è molto disponibile nel dare supporto.

Grazie alle modifiche che [Emanuele](https://github.com/terminet85) ha apportato al software originale, è possibie integrare questa scheda con Home-assistant. Spero che possano diventare definitive in una prossima release, rendendo inutile questa guida. Grazie anche per il supporto via email.
