# Tutorial n°3 : Installation d'Elastic-Agent
***
## Préambule
Ce tutoriel suit le tuto : [02- Création d'un Fleet Server sous Docker](https://youtu.be/TWMNc9QjoJw)

***
Ce tutorial est réalisé avec 3 VMs:</br>
- VM linux Xubuntu 25.10,
- VM Oracle Linux R10,
- VM Windows 11.

Les logiciels suivant doivent être installés:
- GIT
- Docker

Objectifs de ce tuto:</br>
- Enregistrer le certificat auto-signé ElasticSearch sur un système Debian ou RedHat,
- Installer un elastic-agent sur la VM où est installé le cluster ElasticSearch en docker,
- Démarrer un elastic-agent en mode docker.

***
## Déroulé et liste des commandes</br>
### Vérification des prérequis

```
cat /etc/hosts
```

Il doit y avoir une entrée avec l'IP du serveur et les noms suivant: `es01 kibana fleet_docker`.</br>

Vous devez avoir un répertoire `/data`.</br>
Vous devez avoir un répertoire `/data/poc_elk` et `/data/poc_elk/certs` avec les certificats autosignés créés par l'installation initiale du cluster.</br>
Le cluster Elasticsearch doit être démarré.</br>

### Ajout du certificat d'autorité dans le trousseau du serveur

Pour un serveur Ubuntu (type .deb) :
```
sudo trust list | grep label | grep Elastic
sudo mkdir /usr/local/share/ca-certificates/extra
sudo cp /data/poc_elk/certs/ca/ca.crt /usr/local/share/ca-certificates/extra/
sudo update-ca-certificates
sudo trust list | grep label | grep Elastic
```

Pour un serveur Redhat (type .rpm) :
```
sudo trust list | grep label | grep Elastic
sudo cp /data/poc_elk/certs/ca/ca.crt /etc/pki/ca-trust/source/anchors/
update-ca-trust
sudo trust list | grep label | grep Elastic
```

### Ajout d'une politique agent

Se connecter à Kibana: https://kibana:5601</br>
Burger menu: </br>
`Managament => Fleet`
Onglet : `Agent policies`.</br>
Bouton : `Create agent policy`.</br>

Name : `Agent_Policy`</br>
Bouton : `Create agent policy`.</br>

Une fois la nouvelle policy créée, la sélectionner.</br>
Choisir le menu `Add agent`.</br>
Passez au point 3 => `Install Elastic Agent on your host`</br>
Dans les types d'OS proposé, choisir `Linux_x86_64` si votre serveur est de type Ubuntu (debian) ou Redhat.</br>
Notez les commandes fournies et, notamment, le Token d'enrollement sur la ligne d'installation.</br>

### Installation d'un elastic-agent sur la VM poc_elk

Ouvrir une fenêtre et passer les commandes suivantes:
```
cd /data
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-[VERSION]-linux-x86_64.tar.gz 
tar xzvf elastic-agent-[VERSION]-linux-x86_64.tar.gz
cd elastic-agent-[VERSION]-linux-x86_64
sudo ./elastic-agent install --url=https://fleet_docker:8220 --enrollment-token=[TOKEN_DONNE_DANS_LA_POLICY]
```

Une fois l'installation terminée, vous devez voir la policy changer et indiquer qu'un agent a été enrollé.</br>

Vous pouvez supprimer les sources de l'agent.</br>
```
cd ..
rm -rf /data/elastic-agent-*
```

Dans la policy `Agent_Policy` à la place de `Add agent` vous devez avoir `1 agent`.</br>

### Installation d'un elastic-agent en mode docker

Si vous avez suivi les premiers tutos, executez les commandes suivantes:</br>
```
cd /data/poc_elk
```
```
curl -o docker-compose.yml -s https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto03/003_docker-compose.yml
```

Ouvrir le fichier, cherchez er remplacer `[ENROLLEMENT_TOKEN]` par l'enrollement token de la policy `Agent_Policy`.</br>
Lancer l'agent docker:</br>
```
docker compose up -d agent_docker
```


