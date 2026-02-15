# Tutorial n°2 : Création d'un Fleet Server sous Docker
***
## Préambule
Ce tutoriel suit le tuto 1: [01- Installation Stack Elasticsearch Docker](https://youtu.be/TWMNc9QjoJw)

***
Ce tutorial est réalisé sur une VM Linux Ubuntu Desktop 25.10 avec 8 Gb de RAM, 4 vCPU et 128 Gb de disque dur.</br>
De préference, utilisez une IP fixe.</br>

Les logiciels suivant doivent être installés:</br>
- GIT
- Docker

Objectif de ce tuto:</br>
- Création des certificats pour la mise en place d'un Fleet Server,
- Création d'une Policy Fleet,
- Création d'un Fleet Server en docker utilisant cette Policy,
- Démarrage du Fleet server sous docker,
- Tests et vérifications.

***
## Déroulé et liste des commandes</br>
```
sudo vi /etc/hosts      # Ajouter fleet_docker
```
```
cd /data/poc_elk
```
```
sudo chown -R ${USER} /data/poc_elk
```
```
docker compose ps
```
```
cd certs
```
```
ls -alh
```
```
curl -o fleet_docker.yml -s https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto02/002_fleet.fleet_docker.yml
```
```
cat /etc/hosts
```
```
vi fleet_docker.yml             # Ajouter l'IP du serveur
```
```
docker exec -it poc-es01-1 bash
```
```
bin/elasticsearch-certutil cert --silent --pem -out config/certs/fleet_docker.zip --in config/certs/fleet_docker.yml --ca-cert config/certs/ca/ca.crt --ca-key config/certs/ca/ca.key
```
```
exit
```
```
ls -alh
```
```
unzip fleet_docker.zip
```
```
cd ..
```
Retour dans l'interface graphique.</br>
Ouverture de l'interface Fleet Server</br>
Burger menu (icone avec  3 lignes) => Management => Fleet</br>
Onglet `Settings`</br>
Partie Output, selectionnez la ligne "default" remplacer:</br>
- `http://localhost:9200` par  `https://es01:9200`
Ouvrir `Authentication`:  </br>
`Server SSL certificate authorities` mettre le contenu de : 
```
cat /data/poc_elk/certs/ca/ca.crt
```

Allez au menu "Agent " puis "Add Fleet Server":</br>
Selectionner "Advanced"</br>
**Point 1 - Create a policy for Fleet Server"**  
Changer le nom par "POC_Fleet"</br>
Ouvrir "Advanced options"</br>
Mettre le paramètre "Inactiviy timeout" à 600.</br>
Mettre le paramètre "Inactive agent unenrollment timeout" à 700.</br>
=> 600s = 5mn car ce Fleet est un container qui sera automatiquement détruit après 5m d'inactivité.</br>

Puis `Create policy`</br>

\-- Attendre 5 mn --</br>
**Point 2 - Choose a deployment mode for security**  
Selectionner Production</br> 

**Point 3 - Add your Fleet Server host**  
Nom: `fleet_docker`</br>
URL: `https://fleet_docker:8220`</br>
Authentication:  </br>
Copiez les certificates:
- `Fleet Server > Elastic Agents certificate authorities`: 
```
cat /data/poc_elk/certs/ca/ca.crt
```
- `Fleet Server > SSL server certificate`:
```
cat /data/poc_elk/certs/fleet_docker/fleet_docker.crt
```
- `Fleet Server > SSL server private key`:
```
cat /data/poc_elk/certs/fleet_docker/fleet_docker.key
```
Choisir pour `Client auth`, `Required`</br>
Terminez par `Add host`</br>

**Point 4 - Generate a service token**  
Click on button  </br>
Garder le TOKEN à l'écran.</br>
***

Retour dans le terminal
```
curl -o docker-compose.yml -s https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto02/002_fleet.docker-compose.yml
```
```
vi docker-compose.yml
```
Ouvrir le fichier docker-compose.yml et faire le remplacement suivant:</br>
`[REPLACE_BY_FLEET_TOKEN_CREATED_BY_KIBANA]` par le TOKEN de l'écran de configuration.</br>
***
Démarrez le Fleet Server:
```
docker compose up -d fleet_docker
```
***
Test du Fleet Server:
```
curl --cacert /data/poc_elk/certs/ca/ca.crt  https://fleet_docker:8220/api/status
```
***
Retour dans Kibana, le Fleet Server doit être en statut Updating puis Healthy.</br>
Vérifiez que la CPU et la mémoire ne sont pas en N/A.

> [!WARNING]
> En version 9.2.x, un restart de l'interface Kibana se fait lors de l'intégration du Fleet.
***
