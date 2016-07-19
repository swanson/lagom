---
layout: post
title: Les raisons de la migration de notre application
categories:
- blog
---

Lorsque j'ai rejoins Y-Proximité, j'ai ressenti, après avoir collecté de nombreux signaux décrit dans cette suite de billets, le besoin de lancer un processus de migration de notre application "CMS" orientée réseau, multi-tenant (1BDD = X clients) & marque blanche. Elle héberge aujourd'hui environ 2,000 sites/landing pages.

On peut lire à certains endroits qu'[une refonte c'est le mal](http://www.joelonsoftware.com/articles/fog0000000069.html). J'ai décidé de raconter comment s'est passée notre migration (moins abrasive sur le plan politique qu'une refonte) qui s'est déroulée sur 2 ans. Voici mon cahier de route de la migration vers "Yprox v2" qui couvre plusieurs sujets.

* I. Migration: Les raisons
* II. [Migration: Le processus de migration continue]({% post_url 2016-07-17-migration-continue %})
* III. [Migration: Le bilan]({% post_url 2016-07-18-migration-couts-et-gains %})

# Les raisons de la migration

## 1. Du code deprécié
Le projet "Yprox" a été crée en Symfony 2.0-ALPHA. 

A cette époque, Symfony était taggué avec des tags pré-[SemVer](http://semver.org/) tel que: "[vPR12](https://github.com/symfony/symfony/releases/tag/vPR12)". 

Les formulaires reposaient sur le composant form dans une version qui n'a plus rien à voir avec l'actuel. La sauvegarde de ce code est [disponible ici](https://github.com/Yproximite/symfony-legacy-form) pour les aventuriers.

Mais si, rappellez-vous, une époque lointaine entre la ferveur Symfony 1.4 et Symfony 2 pas encore sorti qui apportait son lot d'inquiétude ("mais ou est passé mon admin generator ?", ["l'injection de dépandance WTF IS THAT"](https://blog.elao.com/fr/dev/symfony-2-linjection-de-dependances/)). On y faisait à l'époque: 

{% highlight php %}
<?php 

public function configure()
{
    ...
    $this->add(new TextField('first_name'));
    $this->add(new TextField('last_name'));
    $this->add(new ChoiceField('delivery_country', array(
        'multiple' => false,
        'choices' => $countriesForSelect,
        'value_transformer' => $countriesTransformer,
    )));
}
{% endhighlight php %}

Environ 70% des formulaires se basaient sur du code obsolète et donc plus maintenu. Le projet a évolué jusqu'en Symfony 2.8, mais toujours pas cette dette technique que représentaient les formulaires "legacy" (entre autres).

## 2. Une montée en compétence difficile & longue

Monter en compétence sur l'ancienne version, c'est un peu comme escalader l'Everest. Beaucoup de choses sont obscures sans l'aide de dev historique et avant de pouvoir fournir son premier patch, il faut être sûr d'avoir compris les nombreux concepts et enjeux de l'application (l'héritage, le multi-tenant, les réseaux, la marque blanche...)

Chaque mise en prod, qui intervenait 1 fois toutes les 2 semaines, est un poil hasardeuse et stressante. (**spoiler**: dans le prochain article j'explique comment on est passé à 32 déploiements par jour)

## 3. Une structure tentaculaire

L'application multi-tenant est en fait 2 apps :

* Un admin qui permet d'administrer son/ses site(s) et de pousser du contenu sur les sites membres du réseau.
* Un front chargé de rendre le site demandé en fonction de son domaine.

_Clique sur l'image pour voir une seconde structure dans la structure initiale :_
[![Architecture yprox v1 version simplifiée1](/assets/images/yprox_architecture_v1.png)](https://www.evernote.com/l/ARFVTTcLDkpKlLMWrHYcza_8IXehjwjOdB4)

Un `SiteKernel` et un `AdminKernel` sont responsables de charger les "bons" bundles selon le contexte. Mais cette séparation n'est pas réelle puisque tout est mélangé.

Pas loin de **300,000 lignes de code** JS et **130,000 lignes PHP** sont contenues dans cette architecture en "spaghetti" (`src/Ylly`). 

![Nombre de ligne yProx v1](/assets/images/yprox_cloc_v1.png)

## 4. Plus de développeurs "historiques"
 
Le rachat de la société par [Fiducial](http://www.fiducial.fr/) et son déménagement sur Lyon lui aura coûté les derniers développeurs fondateurs de la solution, laissant derrière eux, malgré des principes de base solides, peu de documentation, et un temps de passasion de compétences pas assez conséquent.
Il était important, à mes yeux, de se "ré-approprier le code" afin de mieux répondre aux besoins du métier, et dans de meilleurs délais.

## 5. Un design obsolète

Bon, d'une part, faire une migration de code, ça parle pas aux utilisateurs, alors vendre le projet aux décideurs avec un argument de plus mentionnant un design responsive et plus adapté, ça permet de gagner des points (et de s'épargner la rétine au quotidien).

_(Cliquez pour aggrandir)_
[![Prévisualisation backoffice yprox v1](/assets/images/yprox-screenshot-admin-v1.png)](https://www.evernote.com/l/ARF6mfz3MYtL646QCLdeXi0lI5pGAkaIHAQ)
[![Prévisualisation article backoffice yprox v1](/assets/images/yprox_bo_v1_list.png)](https://www.evernote.com/l/ARHnVWNmttBKg6c7IYGYrI3msig9e0WwwpU)


En résumé nous pouvons dire que le choix d'une migration se fait de manière subjective et forcément couplé à un contexte particulier (équipe en place, compétences, dette technique, qualité de code, attentes...)


Lire la suite : II. [Migration: Le processus de migration continue]({% post_url 2016-07-17-migration-continue %})
