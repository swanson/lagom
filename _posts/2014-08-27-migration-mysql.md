---
layout: post
title: Comment réduire par 2 les temps de réponse, MySQL VS MariaDB 
categories:
- blog
---

Cet billet s'inscrit dans le cadre d'une série d'article visant à expliquer les différentes étapes de la migration d'une infrastructure, et vous expliquer comment j'ai réussi à diviser par 2 le temps de réponse de l'application.

---

# MYSQL vs MariaDB

L'application tournait sur du **MySQL 5.1** avec comme moteur **InnoDB**. Le choix de partir sur du MariaDB s'est imposé du fait de sa réputation côté performance (J'entends déjà les pro PostgreSQL gronder au loin), et du fait que <a href="http://www.zdnet.com/google-quietly-dumps-oracle-mysql-for-mariadb-7000020670/" target="_blank"> Facebook et Google ont dit adieu à MySQL</a>.

Pour information, MariaDB à été conçu comme un "drop in replacement" de MySQL. Les binaires se nomment de la même manière. La transition MySQL -> MariaDB s'est fait sans aucun incident. J'utilise **XtraDB** qui est le drop-in replacement pour InnoDB, la aussi patché et optimisé dans tout les sens.

## Le Hardware

Avant : 



Après : 

| CPU        | Intel Xeon E5-1620v2 4c/8t @ 3,8Ghz            |
|------------|------------------------------------------------|
| RAM        | 32 Go DDR3ECC 1600MHz                          |
| Hard Drive | 3x 160Go SSD Intel DC S3500 SATA3 6Gbps        |
| Network    | 1 Gbps Public network + 1 Gbps Private network |


## Tuning MariaDB 10.0.x


Pour connaitre le max IOPS (Input/Output Operation Per Second), rendez vous sur la fiche constructeur de votre SSD, il devrait être renseigné sur sa fiche. (Pensez quand même à retirer 10-15% des valeurs annoncés par les constructeurs).
Si vous êtes en disque dur à plateaux en Raid, des méthodes de calculs sont disponibles ici.

Cette petite appartée étant fermée, le but de l'article n'est pas de vous expliquer à quoi servent toutes ces optimisations, j'en serai incapable, malgré des recherches poussées sur le tuning MariaDB. 




## Test de montée de charge

Lorsque j'ai fais monter l'application à 300 requêtes par seconde, MariaDB est monté jusqu'à 500 requêtes par secondes. J'ai eu quelques erreurs comme quoi plus de slots de connexion MySQL étaient disponible, ce qui m'à ammené à passer de `variable_conf = 500` à `variable_conf = 1000`.


## Résultat


Côté montée de charge, comme souvent en PHP, on est rapidement limité par PHP plutôt que par la base de données. 
