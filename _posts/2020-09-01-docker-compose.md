---
layout: post
title:  "Memanfaatkan Docker Compose"
date: 2020-09-01 13:16:00 +0700
categories: devel infrastruktur
---

Pada post yang lalu telah dibahas cara membangun Docker Image, membuat dan menjalanakan sebuah container. Kali ini akan kita bahas cara membuat dan menjalankan beberapa container sekaligus menggunakan tool: `docker-compose`.

Langkah pertama adalah memasang `docker-compose` dengan perintah:

{% highlight bash %}
sudo apt install docker-compose
{% endhighlight %}

Selanjutnya, buat file `docker-compose.yml` yang isinya seperti berikut:

{% highlight yml %}
version: '3'
services:
#Registry
  registry:
    image: registry:2
    restart: always
    environment:
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry-Realm
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/registry.passwd
      REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
    volumes:
      - ./data:/data
      - ./auth:/auth
    networks:
      - registry-ui-net
#Registry UI Service
  ui:
    image: joxit/docker-registry-ui:static
    restart: always
    ports:
      - 8080:80
    environment:
      - REGISTRY_TITLE=My Private Docker Registry
      - REGISTRY_URL=http://registry:5000
    depends_on:
      - registry
    networks:
      - registry-ui-net
#Docker Networks
networks:
  registry-ui-net:
    driver: bridge
{% endhighlight %}

Lalu jalankan perintah:

{% highlight bash %}
docker-compose up -d
{% endhighlight %}

Perintah tersebut akan membuat dan menjalankan 2 buah container yang bernama `registry` dan `ui`.

Catatan:

`registry` yang kita buat di atas, digunakan sebagai `privat repostory` seperti `hub.docker.com`, yang menyimpan berbagai image yang kita buat di jaringan lokal.

Bersambung...
