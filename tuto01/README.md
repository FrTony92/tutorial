# Tutorial n°1 : Installation d'une stack Docker
***

Ce tutorial est réalisé sur une VM Linux MINT 22.2 avec 16 Gb de RAM et 100 Gb de disque dur.</br>

Les logiciels suivant doivent être installés:
- GIT
- Docker

Le user utilisé doit appartenir au groupe `root`.

Voici la liste des commandes utilisées lors du tuto:</br>
### Pour la partie ajustement OS:
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
curl -s -o .env https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.env
```
Modification du fichier .env :</br>
ELASTIC_PASSWORD=[PASSWORD]</br>
KIBANA_PASSWORD=[PASSWORD]</br>
STACK_VERSION=[VERSION]</br>

### Démarrage de la stack:
```
curl -s -o docker-compose.yml https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.docker-compose.yml
more docker-compose.yml
curl -s -o kibana.yml https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.kibana.yml
cat kibana.yml
docker compose up -d
docker compose ps -a
```

### Test de la stack:
```
curl https://127.0.0.1:9200 --cacert certs/ca/ca.crt -u "elastic":"elastic@docker"
curl https://127.0.0.1:5601/status -I --cacert certs/ca/ca.crt
```
> [!WARNING]
> Si vous n'arrivez à utiliser les certificats ajoutez le groupe `root` a votre utilisateur
