---
layout: post
title: Le bilan de notre migration
categories:
- blog
---

Lorsque j'ai rejoins Y-Proximité, j'ai ressenti après avoir collecté de nombreux signaux décrit dans cette suite de billets, le besoin de lancer un processus de migration de notre application "CMS" orienté réseau, multi-tenant (1BDD = X clients) & marque blanche. Il héberge aujourd'hui environ 2,000 sites/landing page.

On peut lire de à certains endroits qu'[une refonte c'est le mal](http://www.joelonsoftware.com/articles/fog0000000069.html). J'ai décidé de raconter comment s'est passé notre migration (moins abbrasive sur le plan politique qu'une refonte) qui s'est déroulée sur 2 ans. Voici mon cahier de route de la migration vers "Yprox v2" qui couvre plusieurs sujets.

* I. [Migration: Les raisons]({% post_url 2016-07-16-les-raisons-dune-migration %})
* II. [Migration: Le processus de migration continue]({% post_url 2016-07-17-migration-continue %})
* III. Migration: Le bilan

# Les gains de cette migration

![12700 commits](/assets/images/yprox_github_stats.png)


## Une "meilleure" structure, introduction de microservices.
_Cliquez pour une version dépliée de l'architecture_

[![Architecture yprox v2 version simplifiée](/assets/images/yprox_architecture_v2.png)](https://www.evernote.com/l/ARGX1eBvtjFLCKMrVYdtkgAmmnsbCa8ow_Q)

Je tiens à préciser qu'il s'agit d'une migration, le code/logique n'a pas été réécrit "from scratch" puisque le coeur à bénéficié d'une **base solide**.

Cepandant, beaucoup de modules ont bénéficiés de gros refactoring et d'une restructuration profonde.
Au final, voilà le tableau comparatif avant/après du nombre de lignes de code (acronyme LOC), tel que rapporté par l'utilitaire Cloc.

| Language  | V1      | V2     |
|-----------|---------|--------|
| PHP       | 138,000 | 75,000 |
| JS        | 312,000 | 20,000 |
| HTML+TWIG | 182,000 | 25,000 |

<br />
Cela s'explique par:

* Passage des librairies JS avec NPM, et publication avec Gulp
* Réfactorisation des modules métier en PHP
* Suppression de code mort
* Supression de fonctionnalités très couteuses à maintenir par rapport à son utilisation (ex: module e-commerce "maison")

Certaines tâches métiers ont étés déportés dans d'autres repository. Nous avons pour certaines tâches un système de "queue" et de "worker" (en Silex qui tourne avec du Docker) chez Iron.io qui viennent performer certaines tâches en dehors de notre infrastructure. 

![architecture globale v2](/assets/images/yprox-architecture-globale.png)

## Tests fonctionnels et unitaire

Nous avons augmenté le taux de couverture des tests **unitaires**, **de classe** (60 specs, 368 exemples PHPSpec) et **fonctionnels** (449 scénarios Behat).

Entre nous, sortir des taux de couverutre, c'est bidon si les tests sont mals conçus, mais ça permet d'avoir une base afin de se "rassurer". Nous sommes quand même loin de ce que peux faire [Sylius](https://github.com/Sylius/Sylius) côté tests.  
Afin de nous épauler, Travis a été boosté pour obtenir 5 build parallèles.

## Un design plus "dans l'ère du temps"

_Cliquez pour pouvoir agrandir_
[![Screenshot yproximite v2](/assets/images/yprox_bo_dashboard_v2.png)](https://www.evernote.com/l/ARGMA336ksxKfJn0dV8X6XpsHU3MA3mqks4)
[![Screenshot yproximite v2 - Listing](/assets/images/yprox_bo_list_v2.png)](https://www.evernote.com/l/ARGJLToKexxKN7jjsZmNnxEHzZwZy7d2-mQ)

Certes, c'est un template d'admin qui a été acheté sur un site de template, mais il fait le travail. Notre graphiste & équipe d'intégration, très occupés (eux aussi) n'ont presque pas été sollicités, parfois à regret.

## Migration = période de jachère ? Pas forcémment.

Bien qu'ayant freiné certains besoins des utilisateurs internes, au court de cette migration plus de **20 nouvelles fonctionnalités** ont vu le jour:

- De nouveaux modules métiers
- Des réécritures de modules en Vue.js avec des nouvelles fonctionnalités.
- Une API Rest consommée par des partenaires
- Le support d'HTTP2 pour le backoffice

Attention, le fait de continuer a faire évoluer le produit pendant une migration (+bugfixs), à un **coût conséquent sur le temps de migration**. Il aura fallu tenir un rythme parfois soutenu pendant 2 ans (début 05/2014) avec environ 3 à 4 (selon périodes) ressources à temps plein.

## La résistence des utilisateurs: attention à la contamination.

Nous avons "perdus" l'adhésion de certains utilisateurs internes en 2 ans qui se demandent "pourquoi cette migration ?", "est-elle vraiment nécessaire d'un point de vue de l'utilisateur ?"
La résistance au changement est un sujet qui serait à traiter a part entière et qui ne le sera pas ici ;-). Il faut veiller que ces critiques/craintes audibles ne viennent pas "contaminer" votre équipe de développement sans quoi cette dernière doutera aussi de la réelle utilité de leur travail. Trouvez des early-adopters qui ne verront pas ce changement comme "imposé" et "inutile".

Bien que des workshop fûrent organisés, un travail d'accompagnement et de pédagogie **aurait du être entrepris avec une plus grande ferveur** avec nos utilisateurs afin d'être transparent sur l'avancée de la migration (très difficile à estimer du fait du maintien de l'existant et d'ajout des nouvelles fonctionnalités).

Un rôle de Product Owner nous à fait défaut pour collecter les retours utilisateurs, qui parfois, se sont sentis un peu ignorés face la taille du backlog JIRA ( >320 Tickets, syndrôme du "de toute façon, il ne va pas être traité")
et les axes d'améliorations de l'outil que vous êtes en train de faire évoluer.
 
## Conclusion
Ne vous engagez dans ce genre de migration que lorsque vous avez un soutien de l'opérationnel et de l'équipe en place. Sinon vous allez vous heurter à des murs qui risquent d'être fatal pour un travail de longue haleine.

NB: Il s'agit d'un Bilan anticipé rendu possible grace à cette migration continue. A l'heure ou j'écris ces lignes, nous sommes en cours de validation dans un environnement final ou tout à été migré (front et back)
NB2: Je ne parle pas ici, par choix, de tout ce qui a été introduit de manière annexe (industrialisation des développements, metriques métiers).
