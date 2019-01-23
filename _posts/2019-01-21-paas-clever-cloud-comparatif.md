---
layout: post
title: Partie III - Les PaaS Français sont sur un bateau&#58; Clever Cloud prends l'eau
categories:
- blog
---


Ce billet s'inscrit dans une série d'articles visant à faire un retour d'experience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ Wordpress.

Dans la série :

{% include sommaire-paas.html %}

---


## Clever Cloud

[Acceder au résumé si vous êtes pressés](#résumé-clever-cloud-tldr)



Clever Cloud est aussi un PaaS français qui aparait souvent dans ma timeline Twitter dès que des twittos demandaient où héberger leurs projets. C'est avec enthousiasme que j'ai voulu tester leurs PaaS.

Sur le papier, l'offre est sexy:  (auto) scaling vertical, horizontal;

#### La base de données

Au moment de prendre le service, première mauvaise surprise: La base de données est payante au mois complet, et il n'est pas possible de la faire scaler ni de manière manuelle, ni de manière automatique; (Il est néanmoins possible de commander une nouvelle BDD, d'exporter les data de l'ancienne, et de migrer vers la nouvelle... mais bon... 🥶)

Je m'attarde sur la base de données, puisque chez Clever Cloud (comme d'autres PaaS), la base de données est sujette à une limitation de connexions simultanées. Cette limite est (trop) basse pour le prix. 

Cela compromet la promesse de pouvoir faire scaler l'application : en cas d'un pic de charge sur un site ecommerce, la base de données sera l'élément bloquant de l'infrastructure. Par exemple, si l'on part du postulat que 1 connexion = 2 visiteurs simultanés (👉 attention, calcul avec méthode du doigt mouillé) à 150 visiteurs par seconde et ce, peut importe si l'on place 10 scaler/container frontaux, ou 500)

|                | Clever Cloud    | Scalingo                             |
| -------------- | --------------- | ------------------------------------ |
| Connexion max  | 75              | 62                                   |
| Taille max BDD | 10Gb            | 5Gb                                  |
| Mémoire        | 1Gb             | 1Gb                                  |
| Type           | Dédié           | Dédié (Software + RAM)               |
| CPU            | 1 vCPU          | Partagé                              |
| Prix           | 45€/mois <br /> | 14,4€/mois<br />(découpé par minute) |

<br />

Autre point d'alerte, chez Clever Cloud, ils feront beaucoup d'efforts pour vous orienter vers du PostgreSQL et ce de manière très assumée, puisqu'à ressource strictement équivalente, les containers de base de données sont **plus cher** que du MySQL. 

On parle quand même d'une **différence de 180€ par an**.

Ils justifient cette différence par le fait qu'une instance MySQL coûte plus cher à gérer que du PostgreSQL et l'équipe technique chez eux préfèrent maintenir du PostgreSQL.

Autant pour une application Symfony, ça me fait ni chaud, ni froid (ou presque...) de mettre un PostgreSQL, autant pour du Wordpress, nous n'avons pas le choix sur la base de donnée compatible avec le CMS. Donc au final, c'est l'utilisateur qui paye le coût supplémentaire. 💸.

A priori, ils seraient en train de retravailler le pricing des scaler de base de données.



#### Paramétrage de l'application sur Clever Cloud



Comme toute application hebergée sur un PaaS, on passe par la case "variables d'environnement". Chez Clever Cloud, c'est peu pratique. Lors de mes tests, il y à quelques semaines, il n'y avait pas de gestion de l'édition/ajout des variables en masse. Quand on doit saisir 20 variables : quelle perte de temps !  Heureusement, ça a été corrigé récemment par l'ajout d'un bulk edit/add.

Il n'est pas possible de faire reférence à une autre variable (ex: `DATABASE_URL=$MYSQL_ADDON_URI`), et ça, c'est dommage.

J'ai voulu déployer ensuite mon Wordpress. Je n'ai malheureusement pas pu puisqu'une dépandance ([wp-cli](https://github.com/wp-cli/wp-cli)) nécessitait la présence de l'extension PHP `ext-readline` qui n'était pas disponible sur Clever Cloud.

Il a fallu faire une demande au support, et manque de peau, c'est tombé un vendredi, donc pas de mise en prod possible avant mon départ en vacances. 

<u>NB (non déstinée envers Clever Cloud)</u>: il faut arrêter avec cette politique absurde, je suis tout à fait d'accord avec le Twet ci dessous: chez nous le vendredi est un jour comme les autres pour les déploiements parce qu'on a fait en sorte que ça soit le cas (tests, automatisation, reviews...). 


<blockquote class="twitter-tweet" data-lang="fr"><p lang="en" dir="ltr">&quot;No deploys on friday&quot; is cancer philosophy that enforces fear to deploy and slow down the dev cycle. Stop repeating this crap as if it&#39;s cool, and improve processes and tests, for god&#39;s sake.</p>&mdash; SergiGP 🎗 (@SergiGP) <a href="https://twitter.com/SergiGP/status/1075417087714181120?ref_src=twsrc%5Etfw">19 décembre 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

A mon retour de vacances, toujours pas possible de build, suite à un couac, "la feature est activée mais pas l'extension". Bref, ça arrive quelque heures plus tard: l'application peut enfin être build.



#### Build de l'application sur Clever Cloud

Alors, comment on build chez eux ?

Comme le projet possède un `composer.json`, le PaaS va récupérer les dépandances de manière automatique. Seulement, nous "buildons" le thème depuis une tâche `yarn build`. Il faut donc que l'on puisse lancer un `yarn install` suivi d'un `yarn build`.

Et la c'est pas hyper pratique puisqu'il n'est pas capable de dire "Ok, je vois un `yarn.lock` donc je vais lancer aussi un `yarn install && yarn build`". Donc on est obligé de passer par un **Build Hook.**

On lui défini une variable pour lui dire d'utiliser `yarn` comme package manager, et puis on va lui dire quelles commandes lancer et a quel moment du build, exemple:  `CC_POST_BUILD_HOOK="yarn install && yarn build"`



#### Performance chez Clever Cloud

C'est là ou tout s'écroule, si l'on compare Scalingo à Clever Cloud, **il faut débourser 5 à 9 fois plus cher pour avoir des performances "équivalentes"** sur un Wordpress ⚠️

Exemple: J'envoie 10 clients par seconde pendant 2 minute sur la page d'un produit (Wordpress avec un woocommerce).

Pour **28,8€**/mois chez <u>Scalingo</u>, j'obtiens **930ms** de moyenne de temps de réponse
Pour **275,40€**/mois chez <u>Clever Cloud</u>, j'obtiens **977ms**. C'est la somme <u>minimum</u> à débourser pour avoir des performances semblables 💸.

Alors avant d'écrire cet article, je me suis rapproché du support afin d'avoir leur avis, pour éviter d'écrire un article qui serait basé sur un problème de configuration ou d'optimisation de performances de mon côté.

Voilà leur réponse:

> Wordpress est très consommateur de base de données et ne lésine pas sur les requêtes SQL. Nous utilisons des reverse proxy devant les bases de données qui permettent de les déplacer si nécessaire sans downtime pour l'utilisateur. **Le problème avec ce setup c'est que Wordpress met plus de temps à contacter la base de données** et donc à faire ses requêtes (on parle ici d'une milliseconde de latence ajoutée mais c'est suffisant pour donner le résultat que vous voyez).
>
>  C'est un problème connu de notre côté. Si je donne un accès direct de votre base de données à votre application, nous passons aux alentours de 600ms de chargement de la page (toujours avec 2M, certes, <http://bit.ly/2Cp9fTK> Je me suis permit de créer un compte à moi et de lancer les tests sur le domaine). Dans ces 600ms, environ 550ms sont des échanges avec la base de données.
>
> **Nous travaillons à une solution** pour éviter ces reverse proxy pour les add-ons **mais ça ne risque pas d'arriver avant 2020 je pense**.
>
> Vous avez aussi la possibilité de mettre un varnish devant votre application comme expliqué ici afin de cacher les pages statiques. 



LIEN VERS METHODOLOGIE ET RESULTATS DES TESTS





#### Résumé Clever Cloud (TLDR)

| 👎 Inconvénients                                              | 👍 Avantages                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <Ratio prix/performances sur du wordpress catastrophique<br />Gestion du pricing des add-on de base de données<br />Pas de références à d'autres variables d'environnement <br />Pas de support de Github Server pour <br />déploiement auto <br />Interface parfois peu intuitive<br /><br />Gestions des statistiques (en BETA)<br /><br />Pas de gestion du `pm.max_children` depuis<br /> une variable d'environnement "out of the box" | Backup automatisés<br />Intégration avec let's encrypt<br />Auto-scaling vertical et/ou horizontal<br />Support réactif sur l'aspect communication<br /><br />Interface sexy & réactive<br />Gestion des logs claires |

<br />

⛔️  **VERDICT: ELIMINÉ** ⛔️

Les performances obtenues lors des tests justifie l'élimination de ce candidat. Il n'est pas possible de retenir une solution qui coûte 5 à 9 fois plus cher qu'un de ses conccurrents. 

Rappel: Je n'ai benchmarké qu'une application Wordpress chez eux, en aucun cas je peux dire les performances (très mauvaises) que j'ai obtenues seront les mêmes pour d'autres applications (PHP, Symfony, ou autre languages...) 

## Lire la partie IV

* Part4. [Comparatif PaaS: Scalingo, on reste à flot]({% post_url 2019-01-22-paas-scalingo-comparatif %})

<br />
