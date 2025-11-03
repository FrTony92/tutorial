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
## Déroulé et liste des commandes</br>
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
```
Retour dans l'interface graphique.
Ouverture de l'interface Fleet Server
Burger menu (icone avec  3 lines) => Management => Fleet
Partie Output, selectionner la ligne "default" remplacer:
- `http://localhost:9200` par  `https://es01:9200`
- Dans "Advanced YAML configuration" ajouter `ssl.certificate_authorities: ["/certs/ca/ca.crt"]`

Allez au menu "Agent " puis "Add Fleet Server":  
Selectionner "Advanced"  
**Point 1 - Create a policy for Fleet Server"**  
Changer le nom par "POC_Fleet"  
\-- Attendre 5 mn --  
**Point 2 - Choose a deployment mode for security**  
Selectionner Production  
**Point 3 - Add your Fleet Server host**  
Nom: fleet_docker  
URL: https://127.0.0.1:8220  
Authentication:  
Copiez le certificate et la clé du répertoire `certs`  
**Point 4 - Generate a service token**  
Click on button  
Garder l eTOKEN à l'écran.
***
Retour en mode commande
```
curl -o docker-compose.yml -s https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto02/002_fleet.docker-compose.yml
cat docker-compose.yml
```

Ouvrir le fichier docker-compose.yml et fair ele remplacement suivant:
`[REPLACE_BY_FLEET_TOKEN_CREATED_BY_KIBANA]` par le TOKEN de l'écran de configuration.
***
Démarrer the fleet:
```
docker-compose up -d fleet_docker
```
***
Regardez la log du container:
```
docker compose logs fleet_docker -f
```
***
Test du Fleet Server:
```
curl --cacert certs/ca/ca.crt  https://127.0.0.1:8220/api/status
```
***
Retour dans Kibana, le Fleet Server doit être en statut Updating puis Healthy.  
Vérifiez que la CPU et la mémoire ne sont pas en N/A.