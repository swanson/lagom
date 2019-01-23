---
layout: post
title: Partie II - Les PaaS Français sont sur un bateau&#58; OVH tombe à l'eau
categories:
- blog
---

Ce billet s'inscrit dans une série d'articles visant à faire un retour d'experience sur les différents PaaS français dans une problématique d'hébergement d'un parc de 120+ Wordpress.

Dans la série :

{% include sommaire-paas.html %}


## OVH et son offre Cloud Web

[Acceder au résumé si vous êtes pressés](#résumé-ovh-cloud-web-tldr)



OVH est le dernier arrivé dans le monde du PaaS avec son [offre Cloud Web](https://www.ovh.com/fr/hebergement-web/cloud-web.xml), ce n'est pas son domaine d'expertise, et ça se ressent vite, très vite.

Tout d'abord, première mauvaise surprise, il est impossible de commander une offre Cloud Web pour moins de 12 mois. 

Si vous ne souhaitez pas commander un nom de domaine avec l'offre Cloud Web (car vous en avez déjà un), il est impossible de sauter cette étape lors de la commande de l'offre par l'interface OVH. Il est cependant possible de contourner cette étape si vous commandez l'offre par l'API d'OVH.

A priori, les équipes travailleraient à lever ces contraintes mais de source interne, ça ne sera pas traité avant de longs mois (sans garanties). Mais soyons clairs, certains concurrents proposent de la **facturation à la minute**, donc débourser ~490€ TTC pour faire quelques tests sur du Cloud Web 3, ou même si l'on se projette sur notre parc de Wordpress de 120+ projets, cela reviendrait à un ticket d'entrée de ~**60,000€** sans garantie que nos clients restent un an chez nous.

Je suis rentré en contact avec une personne de chez OVH pour lui faire remonter ces problèmes. On m'a proposé de m'offrir l'offre Cloud Web 3 pour 1 an, en échange de feedbacks sur le produit. Merci pour cette proposition.

Dès que j'ai eu accès au produit, j'ai voulu mettre PHP 7.3 et créer mes variables d'environnement afin de pouvoir configurer le wordpress et faire mes tests de performances. Mais je me suis heurté a quelques problèmes, dont un qui m'a empêché toute utilisation du produit :

- PHP 7.3 n'est pas disponible, il est pourtant sorti le 6 Décembre, soit depuis plus d'un mois lors de la création de cet article
- Pas de gestion de permissions (autre que pour le gestionnaire/contact technique) pour donner par exemple accès à la gestion des variables d'environnement pour un ensemble de personne d'une équipe
- J'ai créé des variables d'environnement, certaines ce sont créées en quelques secondes/minutes, et d'autres, rien après 15 minutes de "En cours de création". Je me suis mis à fouiller l'UI avant de découvrir que la tâche de création de certaines variables était en erreur (sans plus de détails)
- Pas de gestion de "bulk insert" des variables d'environnement. J'ai du répéter les opérations de créations de variable 20 fois à cause de ce manque

<br />
{% include image.html width="610" url="/assets/images/ovh-environment-variables-bug.png" description="La création de ces variables d'environnement sur OVH sont 'en cours' depuis 14 jours sans possibilité de les supprimer ou de les modifier" %}

<br />

#### Le support d'OVH

Sans possibilité de modifier ou de supprimer les variables "fautives", **je suis bloqué** dans ma configuration, je me tourne donc vers le support.

- Le 8 janvier 2019, ouverture du ticket au support pour signaler que je ne peux pas utiliser le produit à cause de mon problème de variables d'environnement
- Le 21 janvier 2019, soit **13 JOURS après**, je reçois la première réponse du support qui peut être résumé à : "Nous sommes désolés pour la prise en charge tardive du ticket, des administrateurs sont en train de vérifier votre demande."




#### Résumé OVH Cloud Web (TLDR)

| 👎 Inconvénients                                              | 👍 Avantages                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| - Engagement annuel<br />- Pas de paiement mensuel<br />- Impossible de commander sans domaine associé <br />par l'interface web<br />Documentation très pauvre et incomplète<br />Pas de déploiement par Git<br />Pas de déploiement automatique via app Github/Gitlab<br />Pas de scaling horizontal, vertical<br />Pas [d'auto-scaling](https://en.wikipedia.org/wiki/Autoscaling)<br />Le support d'OVH : à proscrire <br />Pas de gestion de permissions | Backup automatisés<br />Intégration avec let's encrypt<br />Réseau OVH<br /><br />C'est à peu près tout... 🤷‍♂️😱 |

<br />

⛔️  **VERDICT: ELIMINÉ** ⛔️

Difficile d'émettre un jugement définitif quant aux offres PaaS d'OVH tant que l'on ressent qu'elles ont besoin de maturer encore afin de répondre au besoin du marché. Honnêtement, j'étais frustré et déçu sur le fait de ne pas avoir pu benchmarker la performance de leur offre Cloud Web 3.

Mes aprioris sur le support se sont malheureusement confirmés, de ce fait, je ne peux conseiller à personne cette offre dans le cadre professionnel, où au moindre soucis, vous attendez des semaines la réponse du support (à moins de les harceler sur Twitter), et ce, même si c'est critique pour le service.

Quand à une utilisation personnelle, qui est capable de sortir ~490€ d'un coup pour heberger son "side project" personnel pour une offre PaaS ? 🙊

  
## Lire la partie IV

  * Part3. [Comparatif PaaS: Clever Cloud, le bateau prends l'eau]({% post_url 2019-01-21-paas-clever-cloud-comparatif %})

<br />








