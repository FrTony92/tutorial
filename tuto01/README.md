# Tutorial n°1 : Installation d'une stack Docker
***

Ce tutorial est réalisé sur une VM Linux Ubuntu Desktop 24.04 avec 8 Gb de RAM, 6 vCPU et 128 Gb de disque dur.</br>
De préference, utilisez une IP fixe.</br>

logiciels intallés:
- Docker

Le user utilisé doit appartenir aux groupes `root` et `docker`:
```
sudo usermod -aG root ${USER}
```
```
sudo usermod -aG docker ${USER}
```

Voici la liste des commandes utilisées lors du tuto:</br>
### Partie ajustement OS:
```
sudo sysctl -a | grep vm.max_map_count
```
Passez les commandes suivantes si le résultat est inférieur à 200000 
```
sudo vi /etc/sysctl.conf
```
```
vm.max_map_count=262144
```
```
sudo reboot
```
### Pour l'installation:
```
sudo mkdir /data
```
```
sudo chmod 777 /data
```
```
cd /data
```
```
mkdir poc_elk
```
```
cd poc_elk
```
```
mkdir certs
```
```
curl -s -o .env https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.env
```
Modification du fichier .env pour définir le mot de passe des utilisateur `elastic` et `kibana_system` ainsi que la version de la stack ELK qui va être utilisée :</br>
ELASTIC_PASSWORD=[PASSWORD]</br>
KIBANA_PASSWORD=[PASSWORD]</br>
STACK_VERSION=[VERSION]</br>

### Modification des résolutions de noms (DNS)

```
ip a | grep inet                 # copiez  l'IP du serveur
```
Modifiez le fichier de résolution de noms pour prendre en compte votre IP.
```
sudo vi /etc/hosts

[IP_Serveur]    es01 kibana
```

### Démarrage de la stack:
```
curl -s -o docker-compose.yml https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.docker-compose.yml
```
```
more docker-compose.yml
```
```
curl -s -o kibana.yml https://raw.githubusercontent.com/FrTony92/tutorial/main/tuto01/001_stack.kibana.yml
```
```
cat kibana.yml
```
```
docker compose up -d
```
```
docker compose ps -a
```

### Test de la stack:
```
curl https://es01:9200 --cacert certs/ca/ca.crt -u "elastic":"elastic@docker"
```
```
curl https://kibana:5601/status -I --cacert certs/ca/ca.crt
```
> [!WARNING]
> Si vous n'arrivez à utiliser les certificats ajoutez le groupe `root` a votre utilisateur
