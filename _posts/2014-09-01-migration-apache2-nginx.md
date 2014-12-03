---
layout: post
title: Apache vs Nginx / php5-fpm, réduire par 2 le temps de réponse d'une application
categories:
- blog
---

Ce billet, au titre racolleur, s'inscrit dans le cadre d'une série d'article visant à expliquer comment j'ai réussi à diviser par 2 le temps de réponse de notre application Symfony2 sans toucher au côté applicatif.

Dans la série:


* Part1. [MySQL vs MariaDB]({% post_url 2014-08-27-migration-mysql-mariadb %})
* Part2. Apache2 vs Nginx
* Part3. [L'impact de la migration sur les performances]({% post_url 2014-09-02-migration-resultats-performance %})

---

## Apache vs Nginx (PHP5-FPM)

L'application tournait avec `Apache 2.2` + `PHP 5.4.6` + `APC`. J'ai décidé de migrer sur du `nginx 1.6` + `php-fpm 5.5.15` + `APCu` + `OPCache`

## Le Hardware

Avant :

| Nom        | Reference                                      |
|------------|------------------------------------------------|
| CPU        | Intel Xeon L3426 4c/8t @ 1,8Ghz                |
| RAM        | 16 Go                                          |
| Hard Drive | 2x1,8To @ 7200rpm                              |
| Network    | 400Mbps                                        |

<br />

Après :

| Nom        | Reference                                      |
|------------|------------------------------------------------|
| CPU        | Intel Xeon E5-1620v2 4c/8t @ 3,8Ghz            |
| RAM        | 32 Go DDR3 ECC 1600MHz                         |
| Hard Drive | 3x 160Go SSD Intel DCS3500 SATA3 6Gbps (Raid 1)|
| Network    | 1 Gbps Public network + 1 Gbps Private network |

<br />

## Tuning nginx 1.6.x

La configuration nginx :

{% highlight nginx %}
daemon on;
user www-data;
worker_processes 10;
pid /var/run/nginx.pid;
worker_rlimit_nofile 20000;

events {
    worker_connections 4096;
    multi_accept on;
    use epoll;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 15;
    types_hash_max_size 2048;
    server_tokens off;

    client_max_body_size 400m;
    client_body_buffer_size 128k;

    send_timeout 4;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main buffer=16k;
    error_log /var/log/nginx/error.log;

    open_file_cache          max=5000  inactive=20s;
    open_file_cache_valid    30s;
    open_file_cache_min_uses 2;
    open_file_cache_errors   on;

    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
{% endhighlight %}

La configuration du vhost:

{% highlight nginx %}
server {
    listen 8080 default_server;
    root /path/to/current/web;

    location / {
        try_files $uri @rewriteapp;
    }

    location @rewriteapp {
        rewrite ^(.*)$ /site.php/$1 last;
    }

    location ~ ^/(site|site_dev|admin|admin_dev)\.php(/|$) {
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param HTTPS off;
        fastcgi_buffer_size 128k;
        fastcgi_buffers 256 16k;
        fastcgi_busy_buffers_size 256k;
        fastcgi_temp_file_write_size 256k;
        fastcgi_read_timeout 240;
        fastcgi_max_temp_file_size 0;
    }

    location ~* \.(jpg|jpeg|png|gif|ico)$ {
        log_not_found off;
        access_log off;
    }

    access_log /var/log/nginx/yproximite_access.log main buffer=16k;
    error_log /var/log/nginx/yproximite_error.log;
}
{% endhighlight %}

Configuration de php-fpm:

{% highlight php %}
pm_type: dynamic
pm_max_children: 300
pm_start_servers: 5
pm_min_spare_servers: 5
pm_max_spare_servers: 35
pm_process_idle_timeout: 10s
pm_max_requests: 500
{% endhighlight %}

Autres tweaks:

 * Augmentation de la limite de fichiers ouvert en simultané `ulimit`
 * Augmentation dans le kernel du maximum de connexion par socket autorisé `net.core.somaxconn`


## Performance avant migration

Notre application Symfony2 est assez gourmande de base, notamment sur le nombre de sub request qu'elle génère afin de rendre des blocs de contenus.

Pour le premier test de montée de charge, j'ai été un peu gourmand, j'ai demandé à loader.io de balancer en continu sur 60 secondes de 50 jusqu'à 100 user en simultané. Le verdict tombe comme une guillotine :

![100 clients per sec before with apache2](/assets/images/100_before_apache2.png)

  * 50% d'erreurs (status HTTP != 200) où le serveur n'a rien répondu
  * En moyenne, **17.2 secondes de temps de réponse**
  * Le test s'est arrêté à 70 user / seconde

Alors j'ai décidé de grandement réduire mes exigences avec 25 users/secondes progressif:

![25 clients per sec before with apache2](/assets/images/25_before_apache2.png)

* 0% d'erreurs (status HTTP != 200)
* En moyenne, 4.2 secondes de temps de réponse

## Performance après migration

Puis, avec mon pool de 2 serveurs load balancé, j'ai refais les même scénarios.

Jusqu'à 100/user par seconde en continu:

![100 clients per sec after with nginx](/assets/images/100_after_nginx.png)

* 0% d'erreurs (status HTTP != 200)
* En moyenne, **3.6 secondes de temps de réponse**

Jusqu'à 25/user par seconde en continu:

* 0% d'erreurs (status HTTP != 200)
* En moyenne, 1.3 seconde de temps de réponse

![25 clients per sec after with nginx](/assets/images/25_after_nginx.png)


## PHP 5.5 et la gestion de la mémoire.

La grande nouveauté de PHP 5.4, c'était la gestion de la mémoire améliorée et d'ailleurs nombre d'entre vous ont publiés des benchmark interessant montrant le gain de consomation mémoire.
Du coup voilà un comparatif de la consommation mémoire entre PHP 5.4 vs PHP 5.5. On peux constater qu'il y à une légère amélioration de la consommation.

![Memory usage comparaison php 5.4 vs php 5.5](/assets/images/memory_php.png)

## Résultat

_Remarque_: J'ai été surpris de voir à quel point PHP était CPU depandant. Quand j'ai remarqué que tous les CPU étaient a 100% pendant
le load test et que la mémoire consommée par PHP est montée à peine à 3Go sur les 32Go disponibles, je me suis même demandé
si je n'avais pas oublié quelque chose...

Voilà l'impact, dans l'usage réel de l'application du gain de performance sur les request per minutes.

![rpm before after](/assets/images/rpm_after.png)

Allez, en bonus un petit scénario jusqu'à 300 utilisateurs. Avec 5 secondes de moyenne de temps de réponse,
ce n'est bien sûr pas acceptable pour une application web, mais Varnish viendra dans quelques mois faire exploser ces statistiques.

![300 clients per sec after with nginx](/assets/images/results_nginx.png)

Les performances globales de l'application après la migration vous seront présentés dans le prochain billet (on fait durer le suspense...)
