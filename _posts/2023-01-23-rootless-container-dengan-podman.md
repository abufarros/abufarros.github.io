---
layout: post
title:  "Rootless container di RoxyLinux 9 dengan Podman"
date: 2023-01-23 13:05:00 +0700
categories: devel infrastruktur
---

Ikhtisar:

- Menyiapkan RoxyLinux Container Management
- Mengelola container, image dan pod dengan Podman
- Menyiapkan rootless service dengan systemd

Tulisan ini diawali dengan penjelasan secara singkat proses instalasi RoxyLinux sebagai server container. Untuk memudahkan, server dilengkapi dengan Cockpit web based interface untuk server. Alasan penulis menggunakan RoxyLinux adalah karena dukungan dari pengembang distro ini cukup panjang hingga 10 tahun, selain itu proses instalasi dan konfigurasinya cukup simpel dan mudah.

*Podman* adalah container management yang digunakan untuk membuat, menjalankan dan mengelola container. *Podman* singkatan dari *Pod Manager*. *Pod* adalah konsep yang populer dalam projek *Kubernetes*; yang memuat satu container atau lebih yang bekerja sama, berbagi sumber daya yang sama; *Deploy* container di *Kubernetes* menjadi lebih mudah. 

## Menyiapkan RoxyLinux Container Management

boot iso -> Server -> add: Container management

Untuk mengaktifkan Cockpit, jalankan perintah:

    sudo systemctl enable --now cockpit.socket

## Mengelola container, image dan pod dengan Podman

## Menjalankan Podman Mode Rootless dengan Systemd

    loginctl enable-linger
    systemctl --user enable --now podman
    podman --remote info
