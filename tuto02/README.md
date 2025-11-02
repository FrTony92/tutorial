# Tutorial n°2 : Création d'un Fleet Server sous Docker
***
## Préambule
Ce tutoriel suit le tuto 1: [01- Installation Stack Elasticsearch Docker](https://youtu.be/TWMNc9QjoJw)

***
Ce tutorial est réalisé sur une VM Linux MINT 22.2 avec 16 Gb de RAM et 100 Gb de disque dur.</br>

Les logiciels suivant doivent être installés:
- GIT
- Docker

Le user utilisé doit appartenir aux groupes `root` et `docker`:
```
sudo usermod -aG root [MON_USER]
sudo usermod -aG docker [MON_USER]
```
***
## Liste des commandes utilisées lors du tuto:</br>
```
cd /data/poc_elk
sudo chown -R opc /data/poc_elk
docker compose ps
cd certs
curl -o fleet_docker.yml -s https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto02/002_fleet.fleet_docker.yml
ls -alh
docker exec -it poc-es01-1 bash
bin/elasticsearch-certutil cert --silent --pem -out config/certs/fleet_docker.zip --in config/certs/fleet_docker.yml --ca-cert config/certs/ca/ca.crt --ca-key config/certs/ca/ca.key
exit
ls -alh
unzip fleet_docker.zip
cd ..
curl -o docker-compose.yml -s https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto02/002_fleet.docker-compose.yml
cat docker-compose.yml
```
