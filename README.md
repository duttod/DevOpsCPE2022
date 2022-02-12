# DevOpsCPE2022

<h2>TP1 DOCKER</h2>

<h3>POSTGRES</h3>
Voici les commandes utilisés afin de produire la base de données a travers un docker (build et lancement):


Build: sudo docker build -t driss/postgres . <br>
Lancement:sudo docker run -p 7979:5432 --network tp1_network --name tp1_db driss/postgres

1)Why should we run the container with a flag -e to give the environment variables ?

--> Nous préférons utiliser -e pour éviter d'écrire les identifiants dans la base de données pour mieux les protéger.

Pour obtenir de la données persistente:

docker run -p 7979:5432 -v data:/var/lib/postgresql/data  --name tp1_db driss/postgres


Afin de tester ma base de données j'ai choisis d'utiliser le client console psql de linux .
psql -U usr -d db -p 7979

2)Why do we need a volume to be attached to our postgres container ?

Nous utilisons un volume pour éviter à la structure et les données de la base d'être détruite à chaque redémarrage du container ou a sa suppression.

Voici le dockerfile pour la DB : [Dockerfile](https://github.com/duttod/DevOpsCPE2022/blob/main/tp1_db_docker/Dockerfile)

<h3>BACKEND API</h3>

Voici les commandes utilisés afin de produire l'api backend a travers un docker (build et lancement):

Build: sudo docker build -t driss/back_api . <br>
Lancement: sudo docker run -p 8080:8080 --network tp1_network  --name tp1_back_api driss/back_api

Il est important de mettre l'API dans le même network que la base de données pour y accéder.
Avant de lancer le container de l'API je suis aller modifier le fichier application.yml et j'ai remplacé les paramétres par celui de ma base de données.

Je peux vérifier que mon API fonctionne en consultant les étudiants http://localhost:8080/departments/IRC/students.

Voici le dockerfile pour l'API : [Dockerfile](https://github.com/duttod/DevOpsCPE2022/blob/main/tp1_backend_api/Dockerfile)

<h3>HTTP SERVER</h3>

J'ai choisis une distribution alpine pour httpd qui est plus légére que la version de base.

Voici les commandes utilisés afin de produire le serveur web a travers un docker (build et lancement):
<br>
Build: sudo docker build -t driss/tp1_apache .<br>
Lancement: sudo docker run --name tp1_web_server --network tp1_network -p 80:80 driss/tp1_apache .

J'ai modifié le fichier httpd.conf en rajoutant le chemin de mon API afin que apache soit configuré comme reverse proxy pour la desservir.
On utilise un proxy pour éviter au client de directement accéder a l'API backend.



Voici le dockerfile pour le server web: [Dockerfile](https://github.com/duttod/DevOpsCPE2022/blob/main/tp1_http_server/Dockerfile)

J'y ai ajouter le déplacement de ma conf httpd à travers docker.

<h3>DOCKER COMPOSE</h3>

Docker compose est un manager qui permet de build et déployer plusieurs image docker à la fois.


Voici le fichier docker-compose.yml: [Dockerfile](https://github.com/duttod/DevOpsCPE2022/blob/main/docker-compose.yml)

Il contient une référence au trois docker précédent (database,API,web_server).

Pour publier une image il faut utiliser les commandes suivantes.

docker login <br>
docker tag <tagname> <image> <br>
docker push <image>



