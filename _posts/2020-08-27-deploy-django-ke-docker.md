---
layout: post
title:  "Deploy Django ke Docker"
date: 2020-08-27 14:37:00 +0700
categories: devel infrastruktur
---

Docker adalah sebuah teknologi container software open source yang memungkinkan Anda untuk membangun, menguji, dan menyebarkan aplikasi terdistribusi dalam lingkungan yang terisolasi. Banyak manfaat dari Docker, seperti dalam tulisan mas [Katon Fergiawan B, Lebih Produktif dengan Docker](https://medium.com/@katonfergie/lebih-produktif-dengan-docker-c49b9d6ded8b).

Tuliasan ini saya buat untuk mengulas kembali saat saya mencoba praktek deploy Django ke Docker di komputer saya dengan sistem operasi Ubuntu 20.04. Proses instalasi Docker dan pembuatan aplikasi dengan Django saya lewati. Anggap saja di komputer sudah terpasang Docker (instalasi Docker di Ubuntu dapat dilihat [di sini](https://docs.docker.com/engine/install/ubuntu/)), Django project dan `requirements.txt` juga sudah ada.

Baiklah, kita mulai.

Di direktori project Django, buat file `Dockerfile` yang isinya sbb:

{% highlight bash %}
FROM python:3.8-alpine
ENV PYTHONUNBUFFERED 1

# set work directory
WORKDIR /app

# copy project
COPY . .

# install dependencies
RUN apk add --no-cache --virtual .build-deps \
    build-base ca-certificates gcc linux-headers musl-dev \
    libffi-dev jpeg-dev zlib-dev openldap-dev \
    libsasl postgresql-dev \
    && pip install --upgrade pip \
    && pip install -U setuptools \
    && pip install -r requirements.txt \
    && find /usr/local \
        \( -type d -a -name test -o -name tests \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' + \
    && runDeps="$( \
        scanelf --needed --nobanner --recursive /usr/local \
                | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                | sort -u \
                | xargs -r apk info --installed \
                | sort -u \
    )" \
    && apk add --virtual .rundeps $runDeps \
    && apk del .build-deps

# run django
CMD exec uvicorn dirman.asgi:application --host=0.0.0.0 --port=8000
{% endhighlight %}

Jalankan perintah berikut untuk membuat image dengan nama `dirman`:

{% highlight bash %}
docker build -t dirman .
{% endhighlight %}

Tunggu sampai proses selesai, mungkin memerlukan waktu lama, karena mengunduh file-file yang dibutuhkan dari internet.

Jalankan perintah berikut untuk membuat container dengan nama `dirman`, dan menjalankannya di port 8000:

{% highlight bash %}
docker run \
    -d \
    --rm \ 
    --name dirman \
    -p 8000:8000 \
    -v ~/:/data \
    --log-driver=journald \
    dirman
{% endhighlight %}

Parameter `-v ~/:/data` adalah parameter tambahan untuk mengaitkan direktori `/data` ke home directory kita.

Log disimpan di `journald`, untuk membaca log, jalankan perintah:

{% highlight bash %}
sudo journalctl -b CONTAINER_NAME=dirman
{% endhighlight %}

Sekarang kita bisa mengakses django kita dengan membuka alamat `http://localhost:8000/` dengan browser kesayangan kita. 

`Dockerfile` dapat dibuat lebih kompleks seperti contoh di bawah, bandingkan dengan contoh di atas.

{% highlight bash %}
FROM python:3.8-alpine
ENV PYTHONUNBUFFERED 1

# set work directory
WORKDIR /app

# copy project
COPY . .
COPY dirman/settings_docker.py dirman/settings.py

# install dependencies
RUN apk add --no-cache --virtual .build-deps \
    build-base ca-certificates gcc linux-headers musl-dev \
    libffi-dev jpeg-dev zlib-dev openldap-dev \
    libsasl postgresql-dev mariadb-dev \
    mariadb-client python3-dev \
    && pip install --upgrade pip \
    && pip install -U setuptools \
    && pip install -r requirements.txt \
    && find /usr/local \
        \( -type d -a -name test -o -name tests \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' + \
    && runDeps="$( \
        scanelf --needed --nobanner --recursive /usr/local \
                | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                | sort -u \
                | xargs -r apk info --installed \
                | sort -u \
    )" \
    && apk add --virtual .rundeps $runDeps \
    && apk del .build-deps

# run django
CMD uvicorn dirman.asgi:application --host=0.0.0.0 --port=8000
{% endhighlight %}

Selamat mencoba.

Bersambung...