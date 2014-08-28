---
layout: post
title: MySQL vs MariaDB, réduire par 2 le temps de réponse d'une application
categories:
- blog
---

Cet billet s'inscrit dans le cadre d'une série d'article visant à expliquer les différentes étapes de la migration d'une infrastructure, et vous expliquer comment j'ai réussi à diviser par 2 le temps de réponse de l'application.

Dans la série:

* Part1. MySQL vs MariaDB
* Part2. [Apache2 vs Nginx]({% post_url 2014-08-28-migration-nginx %})


---

# MySQL vs MariaDB

L'application Symfony2 tournait sur du **MySQL 5.1** avec comme moteur **InnoDB**. Le choix de partir sur du MariaDB s'est imposé du fait de sa réputation côté performance (J'entends déjà les pro PostgreSQL gronder au loin), et du fait que <a href="http://www.zdnet.com/google-quietly-dumps-oracle-mysql-for-mariadb-7000020670/" target="_blank"> Google et Wikipedia ont dit adieu à MySQL</a>.

Pour information, **MariaDB** à été conçu comme un "drop-in replacement" de MySQL. Les binaires se nomment de la même manière. La transition MySQL -> MariaDB s'est fait sans aucun incident. J'utilise **XtraDB** qui est le drop-in replacement pour InnoDB, la aussi patché et optimisé dans tout les sens.

Pour information, voilà avec l'ancien serveur MySQL ce que l'on obtenais :

![Performance Before DB](/assets/images/online_mysql_counter.png)


## Le Hardware

Avant :

| Nom        | Reference                                      |
|------------|------------------------------------------------|
| CPU        | Intel Xeon L3426 @ 1,8Ghz                      |
| RAM        | 16 Go                                          |
| Hard Drive | 2x1,8To @ 3500rpm                              |
| Network    | 400Mbps                                        |

<br />

Après :

| Nom        | Reference                                      |
|------------|------------------------------------------------|
| CPU        | Intel Xeon E5-1620v2 4c/8t @ 3,8Ghz            |
| RAM        | 32 Go DDR3 ECC 1600MHz                         |
| Hard Drive | 3x 160Go SSD Intel DCS3500 SATA3 6Gbps (RAID 1)|
| Network    | 1 Gbps Public network + 1 Gbps Private network |

<br />

## Tuning MariaDB 10.0.x

Les serveurs web et de base de données communiquent entre eux sur le vRack, via une autre carte réseau, sur un réseau privé (coupé du net). Cela permet d'éviter de flood la carte réseau publique de call inutile, et d'à terme, d'isoler les serveurs de BDD du réseau public (NSA...).
OVH, par de multiples mise en garde, ont l'air de décourager cette pratique, qui pourtant est le gros avantage du **vRack**.

Cette petite appartée étant fermée, le but de l'article n'est pas de vous expliquer à quoi servent toutes ces optimisations que j'ai pu trouver [ici](http://www.tocker.ca/2013/09/17/what-to-tune-in-mysql-56-after-installation.html), ou [là](https://blog.mariadb.org/performance-evaluation-of-mariadb-10-1-and-mysql-5-7-4-labs-tplc/)

{% highlight mysql %}
[mysqld]
character-set-server=latin1
innodb_file_per_table=1
max_allowed_packet=64M
skip-external-locking
max-connect-errors=100000
max-connections=1000

# InnoDB settings
innodb_buffer_pool_size=15G
innodb_log_file_size=2G
innodb_io_capacity=2000

# Should be disabled on SSD
innodb_flush_neighbors=0

# Capacité totale IOPS de votre disque dur
innodb_io_capacity_max=6000
innodb_lru_scan_depth=2000

# Binary log/replication
log-bin = /var/log/mysql/mysql-bin.log
binlog-format=row
binlog_do_db=yprox
log-basename=master
server_id=1
expire_logs_days=15
transaction-isolation=READ-COMMITTED
innodb_autoinc_lock_mode=2
{% endhighlight %}

Pour connaitre le max IOPS (Input/Output Operation Per Second), rendez vous sur la fiche constructeur de votre SSD, il devrait être renseigné sur sa fiche. (Pensez quand même à retirer 10-15% des valeurs annoncés par les constructeurs).
Si vous êtes en disque dur à plateaux en RAID, des méthodes de calculs sont [disponibles ici](http://www.tocker.ca/2013/09/17/what-to-tune-in-mysql-56-after-installation.html).

## Le ménage des tables de logs

L'application possède des _tables de logs_ où sont consignés tous les changements opérés sur l'application. J'ai purgé ces tables, la base de donnée est passée de 7Go à 2Go.

Des problèmes de tables framgentées sont remontés avoir lancé un **mysql_tuner**. Un petit `mysqlcheck -o` qui va successivement faire un `CHECK TABLE`, `REPAIR TABLE`, `ANALYZE TABLE` et finalement un `OPTIMIZE TABLE` à résolu le problème.

## Test de montée de charge

Lorsque j'ai fais monter l'application à 300 requêtes par seconde, **MariaDB est monté jusqu'à 450 requêtes par secondes** (4500/10 secondes sur le graph ci dessous). J'ai eu quelques erreurs comme quoi plus de slots de connexion MySQL étaient disponible, ce qui m'à ammené à passer de `max-connections=500` à `max-connections=1000`.

Comme souvent en PHP, on est rapidement limité par PHP plutôt que par la base de données. Il semble que je ne sois pas arrivé au max de la capacité de MariaDB.

![Idée initiale](/assets/images/db_monte_charge.png)

## Résultat

Voila le résultat obtenu avant/après la migration, le dernier pic d'activité correspond à l'export de la BDD.

![Performance DB](/assets/images/db_perf.png)

Le temps de réponse de la base de donnée à été grandement améliorée, **on passe de 7ms à 0.3ms** soit un facteur **x23**

![Latence DB MariaDB](/assets/images/db_latency.png)

