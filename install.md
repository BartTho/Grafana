# Prometheus
## Installatie
- [ ] Voer het volgende in om Prometheus-gebruikersaccounts aan te maken die als servicegebruikersaccounts worden gebruikt voor beveiligings- en
beheerdoeleinden. Deze accounts worden niet gebruikt om in te loggen op het systeem.
- [ ] Prometheus-mappen aanmaken
- [ ] Prometheus downloaden en installeren

```
sudo useradd --no-create-home --shell /bin/false prome
sudo useradd --no-create-home --shell /bin/false node_exporter
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```    
Zodra aan al deze vereisten is voldaan, is uw systeem klaar om Prometheus te installeren.
Hieronder vindt u de procedure voor het downloaden en installeren van Prometheus.
https://prometheus.io/download/
- [ ] Download de nieuwste stabiele versie van Prometheus met de opdracht wget.
- [ ] Pak het Prometheus-archief uit
- [ ] Kopieer de binaire bestanden uit de uitgepakte map naar de map /usr/local/bin en wijzig de eigenaar.
- [ ] Kopieer de binaire bestanden "prometheus" en "promtool" naar /usr/local/bin kopiëren.
- [ ] Wijzig vervolgens het eigendom van de bestanden door de onderstaande opdrachten in te voeren.
      
```
wget https://github.com/prometheus/prometheus/releases/download/v2.0.0/prometheus-2.0.0.linux-amd64.tar.gz
tar xvf prometheus-2.0.0.linux-amd64.tar.gz
sudo cp prometheus-2.0.0.linux-amd64/prometheus /usr/local/bin/
sudo cp prometheus-2.0.0.linux-amd64/promtool /usr/local/bin/
sudo chown prome:prome /usr/local/bin/prometheus
sudo chown prome:prome /usr/local/bin/promtool
sudo chown prome:prome /var/lib/prometheus
```

- [ ] Nadat je de binaire bestanden hebt gekopieerd, kopieert je de vereiste bibliotheken naar de map /etc/prometheus.
- [ ] Gebruik vervolgens de volgende opdrachten om het eigendom van de bestanden te wijzigen.

```
sudo cp -r prometheus-2.0.0.linux-amd64/consoles /etc/prometheus
sudo cp -r prometheus-2.0.0.linux-amd64/console_libraries /etc/prometheus
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
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
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
