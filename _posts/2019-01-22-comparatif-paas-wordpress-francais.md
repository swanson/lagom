---
layout: post
title: OVH, Clever Cloud et Scalingo sont sur un bateau&#58; Le comparatif de l'offre PaaS française
categories:
- blog
---

Contexte: Dans la société où je travaille actuellement, nous avons une partie agence web avec des solutions basées sur du **Wordpress** (PHP) et **Symfony** (PHP)

Concernant notre offre Wordpress, la solution d'hosting aujourd'hui ne réponds plus à nos besoins pour de multiple raisons (sécurité, scalabilité, redondance). J'ai souhaité que l'on se tourne vers une offre PaaS (Platform as a Service) afin de résoudre les questions de:

- Maintenance
- Scalabilité
- Sécurité


Une question primordiale pour nous, c'est le coût. Avec notre parc de plus de **120+** Wordpress une solution PaaS coutera plus cher que notre solution mutualisé. Le côut rentrera donc dans les critères décisionnels au même titre que les performances.



⚠️ Disclaimer: Les tests ont été réalisés dans des conditions réelles. J'ai pris 2 Wordpress "représentatifs" déjà en production. Il ne s'agit pas de tests sur un wordpress sans plugins qui une page quasi vierge.

Les tests chez les différents PaaS ont été réalisés dans les même conditions: Même version du site (donc mêmes versions de wordpress + plugins).



## OVH et son offre Cloud Web

[Acceder au résumé si vous êtes pressés](#résumé-ovh-cloud-web-tldr)



OVH est le dernier arrivé dans le monde du PaaS avec son [offre Cloud Web](https://www.ovh.com/fr/hebergement-web/cloud-web.xml), ce n'est pas son domaine d'expertise, et ça s'en ressent vite, très vite.

Tout d'abord, première mauvaise surprise, il est impossible de commander une offre Cloud Web pour moins de 12 mois. Et si vous ne souhaitez pas commander un nom de domaine avec l'offre Cloud Web (car vous en avez déjà un), il est impossible de sauter cette étape lors de la commande de l'offre par l'interface OVH. Il est cepandant possible de bypass cet étape si vous commander l'offre par l'API d'OVH.

A priori, les équipes travailleraient à lever ces contraintes mais de source interne, ça ne sera pas traité avant de long mois (sans garanties). Mais soyons clairs, certains concurrents proposent de la **facturation à la minute**, donc débourser ~490€ TTC pour faire quelques tests sur du Cloud Web 3, ou même si on se projette sur notre parc de Wordpress de 120+ projets, cela reviendrait à un ticket d'entrée de ~**60,000€** sans garantie que nos clients restent un an chez nous.

Je suis rentré en contact avec un contact chez OVH pour lui faire remonter ces problèmes. On m'a proposé de m'offrir l'offre Cloud Web 3 pour 1 an, en échange de feedbacks. Merci pour cette proposition.

Dès que j'ai eu accès au produit, j'ai voulu mettre PHP7.3 ainsi que de créer mes variables d'environnements afin de pouvoir configurer le wordpress.

- PHP 7.3 n'est pas disponible, il est pourtant sorti le 6 Décembre, soit plus d'un mois lors de la création de cet article. 
- Pas de gestion de permissions (autre que pour le gestionnaire/contact technique) pour donner par exemple accès à la gestion des variables d'environnement pour un ensemble de personne d'une équipe.
- J'ai crée des variables d'environnements, certains ce sont crées en quelques secondes/minutes, et d'autres, rien après 15 minutes de "En cours de création". Je me suis mis à fouiller l'UI avant de découvrir en fait que la tâche de création de certaines variables étaient en erreur (sans plus de détail)
- Pas de gestion de "bulk insert" des variables d'environnements. J'ai du répéter les opérations de créations de variable 20 fois à cause de ce maque.

<br />
{% include image.html width="610" url="/assets/images/ovh-environment-variables-bug.png" description="La création de ces variables d'environnement sur OVH sont 'en cours' depuis 14 jours sans possibilité de les supprimer ou de les modifier" %}

<br />

#### Le support d'OVH

Sans possibilité de modifier ou de supprimer les variables "fautives", **je suis bloqué** dans ma configuration je me tourne donc vers le support.

- Le 8 janvier 2019, ouverture du ticket au support pour signaler que je ne peux pas utiliser le produit à cause de mon problème de variables d'environnement
- Le 21 janvier 2019, soit **13 JOURS après** , je reçois la première réponse du support qui peut être résumé à "Désolé pour la prise en charge tardive du ticket, des administrateurs sont en train de vérifier votre demande"




#### Résumé OVH Cloud Web (TLDR)

| 👎 Inconvéniants                                              | 👍 Avantages                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Engagement annuel<br />Pas de paiement mensuel<br />Impossible de commander sans domaine associée <br />par l'interface web<br />Documentation très pauvre et incomplète<br />Pas de déploiement par Git<br />Pas de deploiement automatique via application Github/Gitlab<br />Pas de scaling horizontal, vertical<br />Pas [d'auto-scaling](https://en.wikipedia.org/wiki/Autoscaling)<br />Le support d'OVH : à proscrire <br />Pas de gestion de permissions | Backup automatisés<br />Intégration avec let's encrypt<br />Réseau OVH<br /><br />C'est à peu près tout... 🤷‍♂️😱 |

<br />

⛔️  **VERDICT: ELIMINÉ** ⛔️

Difficile d'emettre un jugement définitif quand à leurs offres tant que l'on ressent qu'elle à besoin de maturer encore afin de répondre au besoin du marché. Honnêtement, j'étais frustré et déçu sur le fait de ne pas avoir pu benchmarker la performance de leur offre Cloud Web 3.

Mes apprioris sur le support se sont malheureusement confirmés, de ce fait, je ne peux conseiller à personne cette offre dans le cadre professionnel, où au moindre soucis, vous attendez des semaines la réponse du support (à moins de les harceler sur Twitter), et ce même si c'est critique pour le service.

Quand à une utilisation personnelle, qui est capable de sortir ~490€ d'un coup pour heberger son "side project" personnel pour une offre PaaS ? 🙊



## Clever Cloud

[Acceder au résumé si vous êtes pressés](#résumé-clever-cloud-tldr)



Clever Cloud est aussi un PaaS français qui remontait souvent dans ma timeline Twitter dès que des twittos demandaient chez qui heberger. C'est donc avec enthousiasme que j'ai voulu tester leur PaaS.

Sur le papier, l'offre est sexy:  (auto) scaling vertical, horizontal

#### La base de données

Au moment de prendre le service, première mauvaise surprise: La base de données est payante au mois complet, et il n'est pas possible de la faire scaler ni de manière manuelle, ni de manière automatique; (Il est néanmoins possible de commander une nouvelle BDD, d'exporter les data de l'ancienne, et de migrer vers la nouvelle... mais bon... 🥶)

Je m'attarde sur la base de données, puisque chez Clever Cloud (comme d'autres PaaS), la base de données est sujette à une limitation de connexions simultanées.

Cette limite est très basse pour le prix: petite comparaison pour du MySQL. Cela compromet la promesse de pouvoir faire scaler l'application: en cas d'un fort pic de charge sur un ecommerce (donc avec des besoins de lecture/ecriture base de données) par exemple, si l'on part du postulat que 1 connexion = 2 visiteurs simultanés, cela nous limitera le nombre simultané (👉 attention, calcul avec méthode du doigt mouillé) à 150 visiteurs par seconde et ce, peut importe si l'on place 10 scaler/container frontaux, ou 500)

|                | Clever Cloud    | Scalingo                             |
| -------------- | --------------- | ------------------------------------ |
| Connexion max  | 75              | 62                                   |
| Taille max BDD | 10Gb            | 5Gb                                  |
| Mémoire        | 1Gb             | 1Gb                                  |
| Type           | Dédié           | Dédié (Software + RAM)               |
| CPU            | 1 vCPU          | Partagé                              |
| Prix           | 45€/mois <br /> | 14,4€/mois<br />(découpé par minute) |

<br />

Autre point d'alerte, chez Clever Cloud, on fera tout ce qui est possible pour vous orienter vers du PostgreSQL et ce de manière très assumée, puisqu'à ressource strictement équivalente, les containers (appellés add-on chez eux) de base de données sont **plus cher** que du PostgreSQL. 

On parle quand même d'une **différence de 180€ par an**.

Ils justifient cette différence par le fait qu'une instance MySQL coûte plus cher à gérer que du PostgreSQL et l'équipe technique chez eux préfèrent maintenir du PostgreSQL.

Autant pour une application Symfony, ça me fait ni chaud, ni froid (ou presque...) de mettre un PostgreSQL, autant pour du Wordpress, nous n'avons pas le choix sur la base de donnée compatible avec le CMS. Donc au final, c'est l'utilisateur qui paye le coût supplémentaire. 💸.

A priori, ils seraient en train de retravailler le pricing des add-on de base de données.



#### Paramétrage et build de l'application



Comme toute application hebergée sur un PaaS, on passe par la case "variables d'environnement". Chez Clever Cloud, c'est peu pratique. Lors de mes tests, il y à quelques semaines, il n'y avait pas de gestion de l'édition/ajout des variables en masse. Quand on doit saisir 20 variables : quelle perte de temps !  Heureusement, ça a été récemment corrigé par l'ajout d'un bulk edit/add.

Il n'est pas possible de faire reférence à une autre variable (ex: DATABASE_URL=$MYSQL_ADDON_URI), et ça, c'est dommage.

J'ai voulu déployer ensuite mon Wordpress. Je n'ai malheureusement pas pu puisqu'une dépandance ([wp-cli](https://github.com/wp-cli/wp-cli)) nécessitait la présence de l'extension php `ext-readline` qui n'était pas disponible sur Clever Cloud.

Il a fallu faire une demande au support, et manque de peau, c'est tombé un vendredi, donc pas de mise en prod possible avant mon départ en vacances. 

<u>Note à part de cet article</u>: il faut arrêter avec cette politique absurde, je suis tout à fait d'accord avec le Twet ci dessous: chez nous le vendredi est un jour comme les autres pour les déploiements parce qu'on a fait en sorte que ça soit le cas (tests, automatisation, reviews...). 


<blockquote class="twitter-tweet" data-lang="fr"><p lang="en" dir="ltr">&quot;No deploys on friday&quot; is cancer philosophy that enforces fear to deploy and slow down the dev cycle. Stop repeating this crap as if it&#39;s cool, and improve processes and tests, for god&#39;s sake.</p>&mdash; SergiGP 🎗 (@SergiGP) <a href="https://twitter.com/SergiGP/status/1075417087714181120?ref_src=twsrc%5Etfw">19 décembre 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

A mon retour de vacances, toujours pas possible de build, suite à un couac humain, "la feature est activée mais pas l'extension". Bref, ça arrive.



#### Performance chez Clever Cloud

Ce que je retiendrais, si l'on compare Scalingo à Clever Cloud: 
⚠️ **il faut débourser 5 à 9 fois plus cher pour avoir des performances "équivalentes"**, si on est gentil sur un Wordpress ⚠️

Exemple: J'envoie 10 clients par seconde pendant 2 minute sur la page d'un produit (Wordpress avec un woocommerce).

Pour **28,8€**/mois chez <u>Scalingo</u>, j'obtiens **930ms** de moyenne de temps de réponse
Pour **275,40€**/mois chez <u>Clever Cloud</u>, j'obtiens **977ms** (c'est la somme minimum à débourser pour avoir des performances semblables).

Alors avant d'écrire cet article, je me suis rapproché du support afin d'avoir leur avis, afin que j'évite d'écrire un article qui serait basé sur un énorme problème de configuration ou de tweaking de mon côté.

Après investigation de leur côté, j'obtiens une réponse de leur support:

>  Le problème se situe bien là ou je le pensais. Wordpress est très consommateur de base de données et ne lésine pas sur les requêtes SQL. Nous utilisons des reverse proxy devant les bases de données qui permettent de les déplacer si nécessaire sans downtime pour l'utilisateur. **Le problème avec ce setup c'est que Wordpress met plus de temps à contacter la base de données** et donc à faire ses requêtes (on parle ici d'une milliseconde de latence ajoutée mais c'est suffisant pour donner le résultat que vous voyez).
>
>  C'est un problème connu de notre côté. Si je donne un accès direct de votre base de données à votre application, nous passons aux alentours de 600ms de chargement de la page (toujours avec 2M, certes, <http://bit.ly/2Cp9fTK> Je me suis permit de créer un compte à moi et de lancer les tests sur le domaine). Dans ces 600ms, environ 550ms sont des échanges avec la base de données.
>
> **Nous travaillons à une solution** pour éviter ces reverse proxy pour les add-ons **mais ça ne risque pas d'arriver avant 2020 je pense**.
>
> Vous avez aussi la possibilité de mettre un varnish devant votre application comme expliqué ici https://www.clever-cloud.com/doc/tools/varnish/ afin de cacher les pages statiques. 



LIEN VERS METHODOLOGIE ET RESULTATS DES TESTS





#### Résumé Clever Cloud (TLDR)

| 👎 Inconvéniants                                              | 👍 Avantages                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Ratio prix/performance sur du wordpress catastrophique<br />Gestion du pricing des add-on de base de données<br />Gestion des variables d'environnements peu UX/DX <br />Pas de support de Github Server pour <br />déploiement auto <br />Interface parfois peu intuitive<br /><br />Réactivité du support sur des problématiques techniques.<br />Gestions des statistiques (en BETA)<br /> | Backup automatisés<br />Intégration avec let's encrypt<br />Auto-scaling vertical et/ou horizontal<br />Support réactif sur l'aspect communication<br /><br />C'est à peu près tout... 🤷‍♂️😱<br /> |



⛔️  **VERDICT: ELIMINÉ** ⛔️

Les performances obtenues lors de mes tests divers et répétés justifie l'élimination de ce candidat. Il est inconcevable de retenir un candidat qui coûte 5 à 9 fois plus cher qu'un de ses conccurrents 

 Je n'ai benchmarké qu'une application Wordpress chez eux, en aucun cas je m'avancerai en disant que les performances (très mauvaises) que j'ai obtenues seront les mêmes pour d'autres applications (PHP, Symfony, ou autre...) 

Notre parc clientelle paye un prix très bas pour leur hebergement mutualisé chez nous. Partir avec Clever Cloud voudrait dire une augmentation 