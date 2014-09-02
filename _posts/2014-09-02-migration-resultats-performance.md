---
layout: post
title: L'impact de la migration - performances doublées
categories:
- blog
---

Cet billet, au titre racolleur, s'inscrit dans le cadre d'une série d'article visant à expliquer comment j'ai réussi à diviser par 2 le temps de réponse de notre application Symfony2 sans toucher au côté applicatif.

Dans la série:

* Part1. [MySQL vs MariaDB]({% post_url 2014-08-27-migration-mysql-mariadb %})
* Part2. [Apache2 vs Nginx]({% post_url 2014-09-01-migration-apache2-nginx %})
* Part3. L'impact de la migration sur les performances

---

## L'infrastructure

Voilà au final à quoi ressemble une partie de la nouvelle infrastructure _(Varnish est OFF pour l'instant)_ :

![Infrastructure](/assets/images/infra_applicative.png)


## Les performances en cas d'usage réel, ça donne quoi ?

Je monitore l'application principalement avec NewRelic et Graphite. Voilà les metrics qu'ils ont pu relever:


![Migration infrastructure performance applicative Symfony2 NewRelic](/assets/images/migration_perf_glob.png)

![Migration infrastructure performance applicative Symfony2 Grafana](/assets/images/grafana_response_time.png)


Temps de réponse moyen en usage réel:

|        Avant  | Après               |
|---------------|---------------------|
| 1sec ~ 1.3sec | 300ms ~ 400ms       |

<br />
**Soit un temps de réponse divisé par deux**, pas mal non ?

## Conclusion

Voilà, mon retour d'experience est fini. L'application n'est restée inaccessible que 26 minutes, le temps de migrer la base de données.

Je suis conscient qu'il aurait été plus interessant de migrer brique par brique, pour terminer sur la migration
hardware, afin de savoir quel composant apportait quel gain, mais j'avais une deadline serée pour migrer toute l'infra.

Je ne vous ai présenté que l'application Symfony2, mais au total, **8 serveurs** ont du être installés et les projets migrés.
Et je tiens d'ailleurs à remercier #Ansible sans qui j'aurais passer 2 fois plus de temps sur la migration (tiens, faudra que je fasse un REX sur Ansible...)

Au final, **ce gain de 50% à été réalisé grace** :

 * Au changement de réseau et de hardware (merci les SSD)
 * Au basculement de Apache / MySQL vers Nginx / MariaDB

La partie load balancing n'intervient pas dans ce gain, puisque lors de mes essais avec un seul backend, cela n'a pas impacté les temps de réponses.
Elle intervient uniquement pour la scalabilité.
