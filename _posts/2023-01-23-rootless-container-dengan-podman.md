---
layout: post
title:  "Rootless container di RoxyLinux 9 dengan Podman"
date: 2023-01-23 13:05:00 +0700
categories: infrastruktur
---

Ikhtisar:

- Menyiapkan Roxy Linux Container Management
- Mengelola container, image dan pod dengan Podman
- Menyiapkan rootless service dengan systemd

Tulisan ini diawali dengan penjelasan secara singkat proses instalasi *Roxy Linux* sebagai server container. Untuk memudahkan pengelolaan, server dilengkapi dengan *Cockpit web based interface* untuk server. Alasan penulis menggunakan *RoxyLinux* adalah karena dukungan dari pengembang distro ini cukup panjang hingga 10 tahun, selain itu proses instalasi dan konfigurasinya cukup simpel.

*Podman* adalah *container management* yang digunakan untuk membuat, menjalankan dan mengelola container. *Podman* singkatan dari *Pod Manager*. *Pod* adalah konsep yang populer dalam projek *Kubernetes* yang memuat satu *container* atau lebih yang bekerja sama, berbagi sumber daya yang sama, dengan *pod* ini proses *deploy container* di *Kubernetes* menjadi lebih mudah. 

Salah satu hal penting di server *Linux* adalah *service* yang menjalankan aplikasi secara otomatis saat komputer menyala dan berjalan di *background*. *Rootless service*, sesuai namanya 'tanpa root', yaitu menjalankan service dengan user biasa. *Rootless* membuat sistem lebih aman. Di sini akan saya sajikan cara menjalankan Podman menggunakan *systemd* secara *rootless*.

## Menyiapkan Roxy Linux Container Management

*Roxy Linux* adalah sebuah distribusi *Linux* yang diinisiasi oleh *Co-founder CentOS* Gregory Krutzer pada tahun 2021. Alasan dibuatnya distribusi ini adalah karena pada 8 Desember 2020 *RedHat* mengumumkan akan menghentikan pengembangan *CentOS*. *Roxy Linux* adalah sistem operasi enterprise open source yang didesain 100% *bug-for-bug* dengan *RedHat Enterprise Linux*. 

*Roxy Linux* didistribusikan dalam tiga paket ISO, yaitu: *Minimal, DVD* dan *Boot*. Penulis menggunakan *Boot ISO* dengan pilihan mode *Server* dan paket tambahan *Container Management*. Setelah proses instalasi selesai, *Podman* sudah terpasang, tinggal melakukan beberapa konfigurasi yang akan saya jelaskan kemudian.

Untuk memudahkan dalam pengelolaan server bisa mengaktifkan Cockpit, dengan menjalankan perintah:

    sudo systemctl enable --now cockpit.socket

Selain menggunakan *Cockpit*, administrasi server *Linux* yang paling populer menggunakan *remote SSH*, *Podman* juga dapat di-*remote*. Dalam membuat tulisan ini, penulis me-*remote* menggunakan terminal *Windows 11* dengan langkah-langkah sebagai berikut:

Buat *SSH key* dengan perintah seperti berikut:

    ssh-keygen -t rsa

Buka *Cockpit*: https://[server_address]:9090, kemudian login dengan user linux yang telah dibuat sebelumnya. Klik menu **Account** di samping kiri, klik nama anda, pada **Authorized public SSH keys** klik tombol **Add key**, masukkan isi teks *id_rsa.pub* yang telah dibuat menggunakan *ssh-keygen*, lalu simpan. Sekarang anda bisa login ke server dengan mode *password less*.

Install *Podman* di *Windows*, saya menggunakan *Podman* versi yang sama dengan server yaitu 4.2.0. *Podman for Windows* dapat diunduh di: (https://github.com/containers/podman/releases/download/v4.2.0/podman-v4.2.0.msi)
 
## Mengelola container, image dan pod dengan Podman

In progress...

## Menjalankan Podman Mode Rootless dengan Systemd

    loginctl enable-linger
    systemctl --user enable --now podman
    podman --remote info
