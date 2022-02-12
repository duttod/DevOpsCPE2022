# DevOpsCPE2022

<h2>TP1 DOCKER</h2>

<h3>POSTGRES</h3>
Voici les commandes utilisés afin de produire la base de données a travers un docker (build et lancement):

Build: sudo docker build -t driss/postgres .

Lancement:sudo docker run -p 7979:5432 --name tp1_db driss/postgres

1)Why should we run the container with a flag -e to give the environment variables ?

--> Nous préférons utiliser -e pour éviter d'écrire les identifiants dans la base de données pour mieux les protéger.

Pour obtenir de la données persistente:

docker run -p 7979:5432 -v data:/var/lib/postgresql/data  --name tp1_db driss/postgres


Afin de tester ma base de données j'ai choisis d'utiliser le client console psql de linux .
psql -U usr -d db -p 7979

2)Why do we need a volume to be attached to our postgres container ?

Nous utilisons un volume pour éviter à la structure et les données de la base d'être détruite à chaque redémarrage du container.

Voici le dockerfile pour la DB : [Dockerfile](https://github.com/duttod/DevOpsCPE2022/blob/main/tp1_db_docker/Dockerfile)
