# Tutorial n°1 : Installation d'un cluster ElasticSearch en Docker
***

Ce tutorial est réalisé sur une VM Linux Ubuntu Desktop avec 8 Gb de RAM, 4 vCPU et 128 Gb de disque dur.</br>
De préference, utilisez une IP fixe.</br>

Les logiciels suivant doivent être installés:</br>
- GIT
- Docker

Objectif de ce tuto:</br>
- Préparer une VM pour installer un cluster 3 noeuds ElasticSearch sous docker,
- Installer le cluster avec un Kibana en mode HTTPS,
- Démarrer et vérifier le cluster.

***
## Déroulé et liste des commandes</br>
Vérification du system:
```bash
cat /etc/os-release
```
```bash
free -h
```
```bash
lscpu
```
```bash
id
```
Le user utilisé doit appartenir aux groupes `root` et `docker`:
Sinon:
```bash
sudo usermod -aG root ${USER}
reboot
```
```bash
sudo usermod -aG docker ${USER}
reboot
```
Faire ensuite logout puis login et vérifier avec le commande `id`.</br>

Voici la liste des commandes utilisées lors du tuto:</br>
### Partie ajustement OS:
```bash
sudo sysctl -a | grep vm.max_map_count
```
Passez les commandes suivantes si le résultat est inférieur à 260.000 
```bash
sudo vi /etc/sysctl.conf
```
```bash
vm.max_map_count=262144
```
```bash
sudo reboot
```
### Pour l'installation:
```bash
sudo mkdir /data
```
```bash
sudo chmod 777 /data
```
```bash
cd /data
```
```bash
mkdir poc_elk
```
```bash
cd poc_elk
```
```bash
mkdir certs
```
```bash
curl -s -o .env https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.env
```
Modification du fichier .env pour définir le mot de passe des utilisateur `elastic` et `kibana_system` ainsi que la version de la stack ELK qui va être utilisée :</br>
ELASTIC_PASSWORD=[PASSWORD] par exemple `elastic@docker`</br>
KIBANA_PASSWORD=[PASSWORD] par exemple `kibana@docker`</br>
STACK_VERSION=[VERSION], au moment de ce tuto 9.3.0</br>

### Modification des résolutions de noms (DNS)

```bash
ip a | grep inet                 # copiez  l'IP du serveur
```
Modifiez le fichier de résolution de noms pour prendre en compte votre IP.
```bash
sudo vi /etc/hosts

[IP_Serveur]    es01 kibana
```

### Démarrage de la stack:
```bash
curl -s -o docker-compose.yml https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.docker-compose.yml
```
```bash
more docker-compose.yml
```
```bash
curl -s -o kibana.yml https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.kibana.yml
```
```bash
cat kibana.yml
```
```bash
docker compose up -d
```
```bash
docker compose ps -a
```

### Test de la stack:
Vérification de la création des certificats autosignés:</br>
```bash
ls -alh /data/poc_elk/certs
```
Affichage de l'état du premier noeud:</br>
```bash
curl https://es01:9200 --cacert /data/poc_elk/certs/ca/ca.crt -u "elastic":"elastic@docker"
```
Afficahge de l'état du Kibana:</br>
```bash
curl https://kibana:5601/status -I --cacert /data/poc_elk/certs/ca/ca.crt
```
> [!WARNING]
> Si vous n'arrivez à utiliser les certificats ajoutez le groupe `root` à votre utilisateur

### Lancement de l'interface Kibana
Dans un browser WEB:</br>
```bash
https://kibana:5601
```
