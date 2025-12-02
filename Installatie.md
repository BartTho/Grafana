# Node Exporter

Wanneer je net begint met Prometheus om je linux resources te monitoren is het goed om eerst de basics op te zetten. 
De stappen hieronder helpen je om deze basics op te zetten. 
Er wordt van een specifieke versie uitgegaan voor het gemak, maar je kan natuurlijk de laatst beschikbare 
versie gebruiken om bij te blijven.

## Installetie
### Downloaden en uitpakken

Volg de volgende stappen om Node Exporter te installeren:
- [ ] Haal de laatste versie op;
- [ ] Pak 'm uit.
- [ ] Maak een nieuwe groep aan voor de aan te maken gebruiker;
- [ ] Voeg een nieuwe gebruiker toe, ken deze toe aan de zojuist gemaakte groep. Het aanmaken van een userdirectory is niet nodig voor deze gebruiker;
- [ ] Verplaats de gedownloade binary naar de juiste directory en geef het de juiste rechten

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.9.1.linux-amd64.tar.gz
sudo groupadd -f node_exporter
sudo useradd -g node_exporter --no-create-home --shell /bin/false node_exporter
cd node_exporter-1.9.1.linux-amd64/
sudo mv node_exporter /usr/bin/
sudo chown node_exporter:node_exporter /usr/bin/node_exporter
```
### Service aanmaken en starten

- [ ] Maak vervolgens een nieuw bestand aan voor de service.
- [ ] Geef het een inhoud
- [ ] Start de service
```
sudo nano /usr/lib/systemd/system/node_exporter.service
```
Geef het de volgende inhoud:

```
[Unit]
Description=Node Exporter
Documentation=https://prometheus.io/docs/guides/node-exporter/
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
ExecStart=/usr/bin/node_exporter \
  --web.listen-address=:9100

[Install]
WantedBy=multi-user.target
```

Geef het de juiste rechten en start vervolgens de service, check of hij werkt en zorg dat hij automatisch start bij een eventuele reboot.
```
sudo chmod 664 /usr/lib/systemd/system/node_exporter.service
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl status node_exporter
sudo systemctl enable node_exporter.service
```
Wanneer de service actief is kan je controleren of hij toegankelijk is door de url van de host te checken op, 
in dit geval, poort 9100. http://[IP-DevOps-Server]:9100


# Prometheus
## Installetie
Zodra aan al deze vereisten is voldaan, is uw systeem klaar om Prometheus te installeren.
Hieronder vindt u de procedure voor het downloaden en installeren van Prometheus.
https://prometheus.io/download/

### Gebruiker aanmaken en mappen aanmaken
- [ ] Voer het volgende in om Prometheus-gebruikersaccounts aan te maken die als servicegebruikersaccounts worden gebruikt voor beveiligings- en beheerdoeleinden. Deze accounts worden niet gebruikt om in te loggen op het systeem.
- [ ] Prometheus-mappen aanmaken

```
sudo useradd --no-create-home --shell /bin/false prome
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```    

### Downloaden en uitpakken
- [ ] Haal de laatste versie op: https://prometheus.io/download/
- [ ] Pak 'm uit.
- [ ] Maak een nieuwe groep aan voor de aan te maken gebruiker;
- [ ] Voeg een nieuwe gebruiker toe, ken deze toe aan de zojuist gemaakte groep. Het aanmaken van een userdirectory is niet nodig voor deze gebruiker;
- [ ] Verplaats de gedownloade binary naar de juiste directory en geef het de juiste rechten

```
wget https://github.com/prometheus/prometheus/releases/download/v3.5.0/prometheus-3.5.0.linux-amd64.tar.gz
tar xvf prometheus-3.5.0.linux-amd64.tar.gz
sudo cp prometheus-3.5.0.linux-amd64/prometheus /usr/local/bin/
sudo cp prometheus-3.5.0.linux-amd64/promtool /usr/local/bin/
sudo chown prome:prome /usr/local/bin/prometheus
sudo chown prome:prome /usr/local/bin/promtool
sudo chown prome:prome /var/lib/prometheus
```

- [ ] Nadat je de binaire bestanden hebt gekopieerd, kopieert je de vereiste bibliotheken naar de map /etc/prometheus.
- [ ] Gebruik vervolgens de volgende opdrachten om het eigendom van de bestanden te wijzigen.

```
sudo cp -r prometheus-3.5.0.linux-amd64/consoles /etc/prometheus
sudo cp -r prometheus-3.5.0.linux-amd64/console_libraries /etc/prometheus
sudo chown -R prome:prome /etc/prometheus/consoles
sudo chown -R prome:prome /etc/prometheus/console_libraries
``` 
## Prometheus Configuration
- [ ] In deze sectie maken we het configuratiebestand prometheus.yml aan in de map /etc/prometheus die we in de vorige stappen hebben aangemaakt. Voer de volgende opdracht uit in Terminal om het bestand prometheus.yml te bewerken:
``` 
sudo nano /etc/prometheus/prometheus.yml
```

- [ ] Kopieer en plak vervolgens de volgende regels in prometheus.yml:
```
global:
  scrape_interval: 10s

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "node-exporter"
    static_configs:
      - targets: ["node-exporter:9100"]
```
Druk op Ctrl+o om op te slaan en op Ctrl+x om het bestand te sluiten.
- [ ] Nu gaan we een nieuw bestand voor de systemd-service aanmaken. Voer hiervoor de volgende opdracht in de terminal uit:
```
sudo nano /etc/systemd/system/prometheus.service
```
- [ ] Kopieer en plak vervolgens de volgende regels in prometheus.service:
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prome
Group=prome
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```
Druk op Ctrl+o om het bestand op te slaan en op Ctrl+x om het bestand te sluiten.
- [ ] Herlaadt u systemd met de volgende opdracht:
- [ ] Start de Prometheus-service
- [ ] Schakel de Prometheus-service in bij het opstarten van het systeem
- [ ] Bekijk de servicestatus
```
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus
sudo systemctl status prometheus
```
De volgende schermafbeelding laat zien dat de Prometheus-service actief is.
Open de Prometheus-webinterface
Probeer vervolgens de Prometheus-webinterface te openen. Open een webbrowser en ga naar het volgende adres:

http://ip-address:9090

# Links
https://www.thedutchlab.com/inzichten/grafana-alloy-quickstart
[https://www.thedutchlab.com/inzichten/grafana-alloy-quickstart](https://grafana.com/docs/grafana-cloud/send-data/metrics/metrics-prometheus/prometheus-config-examples/noagent_linuxnode/)
https://www.thedutchlab.com/inzichten
https://uptrace.dev/tools/prometheus-for-docker

