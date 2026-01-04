# Tutorial n°4 : Activation de la supervision d'une stack ElasticSearch
***
## Préambule
Ce tutoriel suit le tuto 1: [03- Installation d'Elastic-Agent](https://youtu.be/TWMNc9QjoJw)

***
Ce tutorial est réalisé sur une VM Linux Xubuntu 25.10.</br>

Les logiciels suivant doivent être installés:
- GIT
- Docker

## Activation de la surveillance de la stack
Ouvrir la policy "POC_Fleet".
Ajouter l'integration `Elasticsearch`:</br>
- Nom: `elasticsearch-elk_poc`</br>
Dans la partie "Metrics (Stack Monitoring)":</br>
  - Settings
    - Hosts:
      ```
      https://es01:9200
      ```
    Advanced options:
      - Username:
           ```
           elastic
           ```
      - Password:
           ```
           elastic@docker`
           ```
      - Scope: `cluster`
      - SSL Configuration: 
           ```
           certificate_authorities: ["/certs/ca/ca.crt"]
           ```
Ajouter l'integration `Kibana`:
- Nom:
     ```
     kibana-elk_poc
     ```
- Metrics (Stack Monitoring): 
  - Hosts:
       ```
       https://kibana:5601
       ```
  Advanced options:
    - Username:
         ```
         elastic
         ```
    - Password:
         ```
         elastic@docker
         ```
    - SSL Configuration: 
         ```
         certificate_authorities: ["/certs/ca/ca.crt"]
         ```
Revenir à l'écran principal et choisir "Monitor the stack".
