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
sudo usermod -aG root ${USER}
```
```
sudo usermod -aG docker ${USER}
```
***
## Déroulé et liste des commandes</br>
```
ip a | grep inet    # Pour retrouver l'IP de notre serveur
```
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
Partie Output, selectionnez la ligne "default" remplacer:</br>
- `http://localhost:9200` par  `https://es01:9200`
- Dans "Advanced YAML configuration" ajouter 
```
ssl.certificate_authorities: ["/certs/ca/ca.crt"]
```

Allez au menu "Agent " puis "Add Fleet Server":</br>
Selectionner "Advanced"</br>
**Point 1 - Create a policy for Fleet Server"**  
Changer le nom par "POC_Fleet"</br>
Ouvrir "Advanced options"</br>
Mettre le paramètre "Inactiviy timeout" à 600 </br>
=> 600s = 5mn car notre Fleet est un container qui sera automatiquement détruit après 5m d'inactivité.</br>

Puis `Create policy`</br>

\-- Attendre 5 mn --</br>
**Point 2 - Choose a deployment mode for security**  
Selectionner Production</br> 

**Point 3 - Add your Fleet Server host**  
Nom: `fleet_docker`</br>
URL: `https://fleet_docker:8220`</br>
Authentication:  </br>
Copiez les certificates:
- `Server SSL certificate authorities`: 
```
cat /data/poc_elk/certs/ca/ca.crt
```
- `Client SSL certificate`:
```
cat /data/poc_elk/certs/fleet_docker/fleet_docker.crt
```
- `Client SSL certificate key`:
```
cat /data/poc_elk/certs/fleet_docker/fleet_docker.key
```

**Point 4 - Generate a service token**  
Click on button  </br>
Garder le TOKEN à l'écran.</br>
***

Retour dans le terminal
```
cd ..
curl -o docker-compose.yml -s https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto02/002_fleet.docker-compose.yml
```
```
cat docker-compose.yml
```

Ouvrir le fichier docker-compose.yml et faire le remplacement suivant:</br>
`[REPLACE_BY_FLEET_TOKEN_CREATED_BY_KIBANA]` par le TOKEN de l'écran de configuration.</br>
***
Démarrez le Fleet Server:
```
docker compose up -d fleet_docker
```
***
Regardez la log du container:
```
docker compose logs fleet_docker -f
```
***
Test du Fleet Server:
```
curl --cacert certs/ca/ca.crt  https://fleet_docker:8220/api/status
```
***
Retour dans Kibana, le Fleet Server doit être en statut Updating puis Healthy.</br>
Vérifiez que la CPU et la mémoire ne sont pas en N/A.
***
## Activation de la surveillance de la stack
Ouvrir la policy "fleet_docker".
Ajouter l'integration `Elasticsearch`:</br>
- Nom: elasticsearch-elk_poc</br>
Dans la partie "Metrics (Stack Monitoring)":</br>
  - Settings
    - Hosts:
      ```
      https://es01:9200
      ```
    Advanced options:
      - Username: elastic
      - Password: elastic@docker
      - Scope: cluster
      - SSL Configuration: 
        ```
        ssl.certificate_authorities: ["/certs/ca/ca.crt"]
        ```
Ajouter l'integration `Kibana`:
- Nom: kibana-elk_poc
- Metrics (Stack Monitoring): 
  - Hosts:
    ```
    https://kibana:5601
    ```
  Advanced options:
    - Username: elastic
    - Password: elastic@docker
    - SSL Configuration: 
      ```
      certificate_authorities: ["/certs/ca/ca.crt"]
      ```
Revenir à l'écran principal et choisir "Monitor the stack".
