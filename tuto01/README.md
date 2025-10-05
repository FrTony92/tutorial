# Tutorial n°1 : Installation d'une stack Docker
***

Ce tutorial est réalisé sur une VM Linux MINT 22 avec 8 Gb de RAM et 100 Gb de disque dur.</br>

Les logiciels suivant doivent être installé:
- GIT
- Docker

Voici la liste des commandes utilisées lors du tuto:</br>
### Pour la partie Ajustement OS:
```
sudo vi /etc/sysctl.conf
vm.max_map_count=262144
sudo reboot
```
Après reboot:
```
sudo sysctl -a | grep vm.max_map_count
```
### Pour l'installation:
```
sudo mkdir /data
sudo chmod 777 /data
cd /data
mkdir poc_elk
cd poc_elk
mkdir certs
curl -s -o .env https://raw.githubusercontent.com/FrTony92/tutorial/main/001_stack.env
```
Modification du fichier .env :
ELASTIC_PASSWORD=[PASSWORD]
KIBANA_PASSWORD=[PASSWORD]
STACK_VERSION=[VERSION]

### Dmarrage de la stack:
```
curl -s -o docker-compose.yml https://raw.githubusercontent.com/FrTony92/tutorial/main/001_stack.docker-compose.yml
curl -s -o kibana.yml https://raw.githubusercontent.com/FrTony92/tutorial/main/001_stack.kibana.yml
ls -alh
docker compose up -d
docker compose ps -a
```

### Test de la stack:
```
curl https://127.0.0.1:9200 --cacert certs/ca/ca.crt -u "elastic":"elastic@docker"
curl https://127.0.0.1:5601/status -I --cacert certs/ca/ca.crt
```

