---
layout: post
title:  "Rootless container di RoxyLinux 9 dengan Podman"
date: 2023-01-23 13:05:00 +0700
categories: devel infrastruktur
---

Ikhtisar:

- Menyiapkan RoxyLinux Container Management
- Mengelola container dan image
- Mengelola pod
- Menyiapkan rootless service dengan systemd

Tulisan ini diawali dengan penjelasan secara singkat proses instalasi RoxyLinux sebagai server container. Untuk memudahkan, server dilengkapi dengan Cockpit web based interface untuk server. Alasan penulis menggunakan RoxyLinux adalah karena dukungan dari pengembang distro ini cukup panjang, selain itu proses instalasi dan konfigurasinya cukup mudah.

*Podman* adalah container management yang digunakan untuk membuat, menjalankan dan mengelola container. *Podman* singkatan dari *Pod Manager*. Pod adalah konsep yang populer dalam projek Kubernetes; yang memuat satu container atau lebih yang bekerja sama, berbagi sumber daya yang sama. 

boot iso -> Server -> add: Container management
sudo systemctl enable --now cockpit.socket

loginctl enable-linger
systemctl --user enable --now podman
podman --remote info
