---
layout: post
title: Partie IV - Les PaaS Français sont sur un bateau&#58; Platform.sh reste au port
categories:
- blog
---


Ce billet, s'inscrit dans une série d'articles visant à faire un retour d'experience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ Wordpress.

Dans la série:

{% include sommaire-paas.html %}

---


## Platform.sh

[Acceder au résumé si vous êtes pressés](#résumé-platformsh-tldr)


Pour nos projets Symfony sur mesure, nous sommes chez [Platform.sh](https://platform.sh/) (~7 projets).

C'est donc sans mal que l'ai éliminé dans le contexte d'une offre d'hebergement de plus de 120+ projets wordpress car le pricing n'est pas du tout adapté à notre contexte.

Principalement à cause du pricing: **40€ pour 0.8GB de mémoire**, sachant qu'elle est partagé entre les container applicatif; Donc 0.8GB divisé entre le container web, et le container de la base de données, ça fait pas vraiment rêver....

{% include image.html width="610" url="/assets/images/platformsh-memory.png" description="Répartition de la mémoire sur les plans Platform.sh. Pour un service avec du PHP et une base de données, le plan S ne suffira pas." %}

<br />

Le plan supérieur est à 100€/mois. Et là on fait x10 sur nos dépenses, sachant que dans notre contexte, la fonctionnalité pour utiliser différents environnements ne nous interessait pas.

La gestion des variables d'environnements, comment dire... c'est compliqué: on pense que l'onglet variable sur la branche en question suffirait ? En fait non, car elles ne sont accessibles qu'au runtime et pas au build.

Pour rajouter des variables au build, il faut aller cliquer sur une zone de 20x20 px pas du tout mis en avant et rajouter les variables. On se retrouve souvent alors a rajouter les variables dans la configuration du projet, puis à les dupliquer dans la configuration de master afin de bénéficier de l'héritage sur les autres branches.

Notez qu'il n'y à pas d'édition de variables d'environement en mode "Bulk Edit/Add".

Des efforts sur l'UI et l'UX/DX sont à prévoir car l'interface est souvent génératrice de frustrations (et lente).

Les incidents sont fréquents (avis personnel) sur Platform.sh, du type "ah tiens, mes build sont bloqués", "ah tiens, l'application n'est plus auto-deployé, du coup mes patch que je pensais être passé en prod y'a 7 jours ne sont jamais arrivés". Heureusement, la prod n'est jamais tombée sur nos projets, mais c'est souvent usant ces problèmes à répétitions. 

En exemple, de Décembre à Janvier, il y à eu 10 incidents, dont certains majeurs, repertoriés sur leur [statuspage](https://status.platform.sh/history) (hors maintenance)


#### Résumé Platform.sh (TLDR)


| 👎 Inconvéniants                                              | 👍 Avantages                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Cher<br />Pas de scalabilité automatique <br />Interface très lente et pas sexy<br />Gestion des variables d'environnements<br />Incidents a repetitions (hors production)<br />Hook de déploiement qu'il faut <br />régulièrement supprimer et rajouter pour <br />que le projet se déploie automatiquement<br />Aucun outil de monitoring depuis l'interface pour aller créer des règles d'alertes (ex: Poster un message dans Slack si la RAM >90%)<br />On ne sait pas combien de ressource on consomme | On peux déployer des branches <br />comme environnements pour faire <br />des démo ou de la préprod facilement.<br />Encore aucun crash de la prod. |

<br />

⛔️  **VERDICT: ELIMINÉ** ⛔️

Sans benchmark, tant que la projection d'une migration chez eux bloquait au niveau tarifs.

## Lire la partie VI

* Part6. [Comparatif PaaS: Les performances, Clever Cloud VS Scalingo]({% post_url 2019-01-23-clevercloud-vs-scalingo %})
<br />