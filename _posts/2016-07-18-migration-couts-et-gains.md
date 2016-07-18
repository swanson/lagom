---
layout: post
title: Le bilan de notre migration
categories:
- blog
---

Lorsque j'ai rejoins Y-Proximité en tant que lead développeur, j'ai ressenti le besoin de lancer un processus de migration de notre application "CMS" orienté réseau, multi-tenant & white-labelled. Il héberge aujourd'hui près de 2,000 sites ou landing page.

On peut lire de à certains endroits qu'[une refonte c'est le mal](http://www.joelonsoftware.com/articles/fog0000000069.html). J'ai décidé de raconter comment notre migration (moins abbrasive qu'une refonte) qui s'est déroulée sur 2 ans s'est passée. Voilà un cahier de route de la migration vers "Yprox v2" qui couvre plusieurs sujets.

Voici un cahier de route de la migration vers "Yprox v2" qui couvre plusieurs sujets.

* I. [Migration: Les raisons]({% post_url 2016-07-16-les-raisons-dune-migration %})
* II. [Migration: Le processus de migration continue]({% post_url 2016-07-17-migration-continue %})
* III. Migration: Le bilan

# Les gains de cette migration

![12700 commits](/assets/images/yprox_github_stats.png)


## Une "meilleure" structure, introduction de microservices.
_Cliquez pour une version dépliée de l'architecture_

[![Architecture yprox v2 version simplifiée](/assets/images/yprox_architecture_v2.png)](https://www.evernote.com/l/ARGX1eBvtjFLCKMrVYdtkgAmmnsbCa8ow_Q)

Je tiens à préciser qu'il s'agit d'une migration, le code/logique n'a pas été ré-écrit "from scratch" puisque le coeur à bénéficié d'une **base solide**.

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
* Réfactorisation des modules
* Suppression de code mort
* Supression de fonctionnalités très couteuse à maintenir par rapport à son utilisation (module e-commerce "maison")

Certaines tâches métiers ont étés déportés dans d'autres repository. Nous avons pour certaines tâches un système de Queue et de Worker (en Silex qui tourne avec du Docker) chez Iron.io qui viennent performer certaines tâches en dehors de notre infrastructure. 

![architecture globale v2](/assets/images/yprox-architecture-globale.png)

## Tests fonctionnels et unitaire

Nous avons augmentés le taux de couverture des tests **unitaires**, **de classe** et **fonctionnels**. Afin de nous épauler, Travis a été boosté pour obtenir 5 build parallèles.
Entre nous, on ne va pas se mentir, sortir des taux de couverutre, c'est bidon si les tests sont mals pensés. 

## Un design plus "dans l'ère du temps"

_Cliquez pour une version aggrandie_
[![Screenshot yproximite v2](/assets/images/yprox_bo_dashboard_v2.png)](https://www.evernote.com/shard/s273/sh/8c037dfa-92cc-4a7c-99f4-755f17e97a6c/1d4dcc0379aa92ce/res/32b9bd57-66c7-4089-bf96-47c8fdb13a2f/skitch.png)
[![Screenshot yproximite v2 - Listing](/assets/images/yprox_bo_list_v2.png)](https://www.evernote.com/shard/s273/sh/892d3a0a-7b1c-4a37-b8e3-b1998d9f1107/cd9c19cbb776fa64/res/f1c5b924-3edd-4f16-be7e-de9e92908304/skitch.png)

Certes, c'est un template d'admin qui a été acheté sur un site de template, mais il fait le travail. Notre équipe d'intégration et de graphistes, très occupés (eux aussi) n'ont presque pas été sollicités, parfois à notre grand desespoir.

## Migration = période de jachère ? Pas forcémment.

Bien qu'ayant frenné certains besoins des utilisateurs, au court de cette migration plus de **20 nouvelles fonctionnalités** ont vu le jour:

- De nouveaux modules métiers
- Des ré-écritures de modules en Vue.js avec des nouvelles features.
- Une API Rest consommée par des partenaires
- Le support d'HTTP2 pour le backoffice

Attention cepandant, le fait de continuer a faire évoluer le produit pendant une migration, à un coût conséquent sur le temps de migration. Il aura fallu tenir un rythme parfois soutenu pendant 2 ans (début 05/2014) avec environ 3 à 4 (selon périodes) ressource a temps plein (il a aussi fallu maintenir l'existant. A lire dans la partie II) 

## La résistence des utilisateurs: attention à la contamination.

Nous avons "perdus" l'adhésion de certains utilisateurs internes en 2 ans qui se demandent "pourquoi cette migration ?", "est-elle vraiment nécessaire d'un point de vue de l'utilisateur ?"
La résistance au changement est un sujet qui serait à traiter a part entière et qui ne le sera pas ici ;-). Il faut veiller que ces critiques/craintes audibles ne viennent pas "contaminer" votre équipe de développement sans quoi cette dernière doutera aussi de la réelle utilité de leur travail. Trouvez des early-adopters qui ne verront pas ce changement comme "imposé" et "inutile".

Bien que des workshop fûrent organisés, un travail d'accompagnement et de pédagogie **aurait du être entrepris avec une plus grande ferveur** avec nos utilisateurs afin d'être transparent sur l'avancée de la migration (très difficile à estimer du fait du maintien de l'existant et d'ajout des nouvelles fonctionnalités).

Un rôle de Product Owner nous à fait défaut pour collecter les retours utilisateurs, qui parfois, se sont sentis un peu ignorés face la taille du backlog JIRA ( >320 Tickets, syndrôme du "de toute façon, il ne va pas être traité")
et les axes d'améliorations de l'outil que vous êtes en train de faire évoluer.
 
## Conclusion
Ne vous engagez dans ce genre de migration que lorsque vous avez un soutien de l'opérationnel et de l'équipe en place. Sinon vous allez vous heurtez à des murs qui risquent d'être fatal pour un travail de longue haleine.

NB: Il s'agit d'un Bilan anticipé rendu possible grace à cette migration continue. A l'heure ou j'écris ces lignes, nous sommes en cours de validation dans un environnement final ou tout à été migré (front et back)
