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
  --web.listen-address=:9200

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
in dit geval, poort 9200. http://[IP-DevOps-Server]:9200
