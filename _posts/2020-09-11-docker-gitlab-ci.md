---
layout: post
title:  "Membuat Docker Image dengan Gitlab CI/CD"
date: 2020-09-10 11:16:00 +0700
categories: devel infrastruktur
---

`GitLab` merupakan platform `DevOps` yang cukup lengkap, semua proses pengembangan hingga deployment dapat diotomasi. Dengan otomasi ini, pengembangan dan perbaikan perangkat lunak dapat dilakukan dengan cepat. Apabila ada bug dari program baru, mudah untuk melakukan rollback ke versi sebelumnya. 

Skenario DevOps dengan GitLab dapat diatur sendiri, contohnya akan kami paparkan dalam tulisan ini.

**Skenario**: Ketika pengembang mebuat sebuah tag baru, GitLab membuat job tertentu misalnya `build`. Kemudian Gitlab Runner akan mengerjakan job tersebut dengan membuild Docker Image lalu melakukan push ke Docker Registry.

## Prasyarat

- **Docker Registry**. Docker registry adalah sebuah repositori yang menyimpan Docker Image yang nantinya akan diunduh dan dijalankan oleh server produksi. GitLab dapat digunakan sebagai docker registry, cara mengaktifkan dapat dilihat [di sini](https://docs.gitlab.com/ee/administration/packages/container_registry.html).
- **Gitlab Runner**. Gitlab Runner adalah perangkat lunak open source yang digunakan untuk menjalankan job dan mengirim hasilnya ke Gitlab. Dokumentasi Gitlab Runner dapat dilihat di [sini](https://docs.gitlab.com/runner/).

## Langkah-langkah

### Menjalankan GitLab Runner Register

Penulis menggunakan Docker sebagai eksekutor dengan menjalankan perintah seperti berikut:

{% highlight bash %}
sudo gitlab-runner register -n \
  --url https://gitlab.ums.ac.id/ \
  --registration-token xxxxxxxxxxxxxxxxxxx \
  --executor docker \
  --description "UMS Runner" \
  --docker-image "docker:19.03.12" \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock
{% endhighlight %}

Keterangan:
- url adalah alamat Gitlab server
- registration-token diperoleh dari menu: Project -> Settings -> Runners
- agar bisa menjalankan docker executor, harus terpasang docker pada mesin yang menjalankan gitlab runner, dan user gitlab runner tergabung dalam group docker.

### Membuat Trigger

Trigger di sini yang akan mengotomasi membuat job ketika aksi tertentu. Pada contoh ini job akan dibuat ketika ada tag baru. Untuk membuat trigger, cukup dengan membuat file `` di root direktori project kita. Contoh:

{% highlight bash %}
image: docker:19.03.12

stages:
  - test
  - build

before_script:
  - echo "MEMULAI STAGE..."

test:
  stage: test
  script:
    - docker info
  only:
    - tags

build:
  stage: build
  script:
    - docker build -t gitlab.ums.ac.id:5050/suyadi/dirman:$CI_COMMIT_TAG .
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push gitlab.ums.ac.id:5050/suyadi/dirman:$CI_COMMIT_TAG
  only:
    - tags
{% endhighlight %}

Bersambung...