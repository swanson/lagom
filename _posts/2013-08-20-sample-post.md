---
layout: post
title: Attention à l'IP load balancée chez OVH
categories:
- blog
---

Dans le cadre du projet de migration d'infrastructure, j'avais prévu de faire du load balancing via <a href="https://www.ovh.com/fr/solutions/ip-load-balancing/" target="_blank">l'IP Load balancing d'OVH</a> entre **datacenter**.

---


## Tout ne s'est pas passé comme prévu

L'idée initiale était pourtant simple, répartir les requêtes entre 2 serveurs frontaux web situés dans 2 datacenter différents (Strasbourg et Roubaix).

Pourquoi 2 datacenter différents ? Quand on commence à penser "infrastructure redondée", on commence à devenir parano:

> Que se passe t'il si le datacenter A explose ? MJ.


![Idée initiale](/assets/images/ovh_lb_initial_idea.png)


J'ai donc souscrit à l'option IP LB compatible **multidatacentre**, mais après avoir déclarés mes deux backend (frontaux web) afin que l'IP LB répartisse la charge, je me suis vite apperçu que seul le serveur de Roubaix recevait des requêtes. Mais pourquoi ?

{% tweet 501338034051502080 %}

## L'IP Anycast, le mal de ce monde

VHS post-ironic cred **bespoke** banjo. Yr wayfarers literally gentrify, flexitarian fap
dreamcatcher plaid cornhole Intelligentsia paleo. Beard try-hard direct trade, shabby chic
Helvetica `look ma, I can code`. Lo-fi American Apparel tattooed [Vice](#) tofu, yr vinyl.
Williamsburg butcher hella mumblecore fixie mlkshk, cliche wolf keytar mixtape kitsch banh mi
salvia. High Life Odd Future *chambray* kale chips hoodie, cray pop-up. Helvetica narwhal
iPhone try-hard jean shorts.

> This is a quote from someone famous about productivity


Syntax highlighting with Solarized theme.

{% highlight ruby %}
class User < ActiveRecord::Base
  attr_accessible :email, :name

  ... tons of other crap ...

end

{% endhighlight %}
