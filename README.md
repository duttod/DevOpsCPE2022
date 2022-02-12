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
docker tag tagname mon_image_local <br>
docker push duttod38/tp-devops-cpe:tagname

Je peux consulter les images sur mon Dockerhub
![img](https://github.com/duttod/DevOpsCPE2022/blob/main/img/docker_hub_image.png)
<h2>TP2 GIT ET GITHUB ACTIONS</h2>

1)mvn clean verify what is it supposed to do ?

Cette commande permet de clean le  build précédent et de charger tout les modules dépendant , elle effectue aussi les test déclarés dans l'application.
Elle utilise le fichier pom.xml du répértoire courant (dans son état actuel mais on peut lui spécifier un fichier) 

2) Unit tests ? Component test ?

Les tests unitaires sont une méthode de test qui vise à tester des composantes individuelles du codes et vérifier leurs fonctionnement (package,module,fonction ...)
Les tests d'intégrations sont utilisés pour tester les modules en tant que groupe lors du regroupement de plusieurs source par exemple.

Voici mon fichier .main.yml [github action file](https://github.com/duttod/DevOpsCPE2022/blob/main/.github/workflows/.main.yml)

Nous utilisons les variables SECRET de github pour ne pas leak nos ID.
Ce fichier contient aussi les commandes pour build et pousser les images docker sur le dockerhub.

Je peux vérifier que le déploiement à bien fonctionné dans la page action de github et en vérifiant que mes nouvelles images sont sur le hub.
![img](https://github.com/duttod/DevOpsCPE2022/blob/main/img/maven_github_action_succes.png)

<h3>QUALITY GATE</h3>

Nous avons choisis d'utiliser sonar afin de vérifier notre code, pour se faire nous avons passer en paramétre nos clés SONAR et l'organization dans laquel les vérifications doivent effectué.
<br>
La conf Sonar se trouve ici [sonar](https://github.com/duttod/DevOpsCPE2022/blob/main/.github/workflows/.main.yml)

Ma vérification Sonar à bien été exécuter cependant elle n'est pas validé car sonar détécte 2 vulnérabilités graves et il lui en faut 0 pour être accepté.
![img](https://github.com/duttod/DevOpsCPE2022/blob/main/img/Sonar.png)



<h2>ANSIBLE</h2>

Voici mon inventory ou j'y ai déclarer l'host fournit par Takima et comment le joindre :[inventory](https://github.com/duttod/DevOpsCPE2022/blob/main/ansible/inventories/setup.yml)

Afin de simplifier le déploiement avec ansible j'ai créer un role pour chacun des éléments que je veux déployer sur mon serveur avec une task associé.<br>
[database_conf](https://github.com/duttod/DevOpsCPE2022/blob/main/ansible/roles/database/tasks/main.yml)<br>
[httpd_conf](https://github.com/duttod/DevOpsCPE2022/blob/main/ansible/roles/httpd_proxy/tasks/main.yml)<br>
[app conf](https://github.com/duttod/DevOpsCPE2022/blob/main/ansible/roles/app/tasks/main.yml)<br>
[docker install](https://github.com/duttod/DevOpsCPE2022/blob/main/ansible/roles/docker/tasks/main.yml)<br>

