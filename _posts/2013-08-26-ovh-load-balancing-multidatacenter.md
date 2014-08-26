---
layout: post
title: l'IP load balancée chez OVH : le piège de l'option multidatacenter
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

<blockquote class="twitter-tweet" lang="en"><p>L&#39;option &quot;compatible multidatacentre&quot; pour l&#39;ip load balancé ne permet pas de faire du load balancing entre datacenter. gros WTF. <a href="https://twitter.com/ovh_fr">@ovh_fr</a></p>&mdash; Tristan Bessoussa (@sf_tristanb) <a href="https://twitter.com/sf_tristanb/statuses/501338034051502080">August 18, 2014</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

## L'IP Anycast, le mal de ce monde

L'utilité de cette option réside dans le fait de vous **rediriger vers le datacenter le plus proche** en ignorant complètement le _poids de vos backend_

Etant donné la proximité de Roubaix et de Strasbourg, je vous laisse admirer ma réaction.

