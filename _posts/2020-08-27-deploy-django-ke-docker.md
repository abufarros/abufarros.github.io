---
layout: post
title:  "Deploy Django ke Docker"
date: 2020-08-27 14:37:00 +0700
categories: server, development
---

Docker adalah sebuah teknologi container software open source yang memungkinkan Anda untuk membangun, menguji, dan menyebarkan aplikasi terdistribusi dalam lingkungan yang terisolasi. Banyak manfaat dari Docker, seperti dalam tulisan mas [Katon Fergiawan B, Lebih Produktif dengan Docker](https://medium.com/@katonfergie/lebih-produktif-dengan-docker-c49b9d6ded8b).

Tuliasan ini saya buat untuk mengulas kembali saat saya mencoba praktek deploy Django ke Docker di komputer saya dengan sistem operasi Ubuntu 20.04. Proses instalasi Docker dan pembuatan aplikasi dengan Django saya lewati. Anggap saja di komputer sudah terpasang Docker (instalasi Docker di Ubuntu dapat dilihat [di sini](https://docs.docker.com/engine/install/ubuntu/)), Django project dan `requirements.txt` juga sudah ada.

Baiklah, kita mulai.

Di direktori project Django, buat file `Dockerfile` yang isinya sbb:

{% highlight ruby %}
FROM python:3.8

# set work directory
WORKDIR /app

# install dependencies
RUN pip install --upgrade pip
COPY ./requirements.txt .
RUN pip install -r requirements.txt

# copy project
COPY . .

# run django
CMD exec uvicorn djangotest.asgi:application --host=0.0.0.0 --port=8000
{% endhighlight %}

Jalankan perintah berikut untuk membuat image dengan nama `django`:

{% highlight ruby %}
docker build -t django .
{% endhighlight %}

Tunggu sampai proses selesai, mungkin memerlukan waktu lama, karena mengunduh file-file yang dibutuhkan dari internet.