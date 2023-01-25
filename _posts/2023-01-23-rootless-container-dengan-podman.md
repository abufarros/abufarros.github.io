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
- Mengelola Podman secara remote

Tulisan ini diawali dengan penjelasan secara singkat proses instalasi *Roxy Linux* sebagai server container. Alasan penulis menggunakan *RoxyLinux* adalah karena dukungan dari pengembang distro ini cukup panjang hingga 10 tahun, selain itu proses instalasi dan konfigurasinya cukup simpel. Untuk memudahkan pengelolaan, server dilengkapi dengan antarmuka berbasis web bernama *Cockpit*. 

*Podman* adalah *container management* yang digunakan untuk membuat, menjalankan dan mengelola container. *Podman* singkatan dari *Pod Manager*. *Pod* adalah konsep yang populer dalam projek *Kubernetes*, *pod* memuat satu atau lebih *container* yang saling bekerja sama, berbagi sumber daya yang sama, dengan *pod* ini proses *deploy container* di *Kubernetes* menjadi lebih mudah. 

Salah satu hal penting di server *Linux* adalah *service* yang menjalankan aplikasi secara otomatis saat komputer menyala dan berjalan di *background*. *Rootless service*, sesuai namanya *tanpa root*, yaitu menjalankan service dengan user biasa, dengan menggunakan *rootless* sistem menjadi lebih aman. Di sini akan saya sajikan cara menjalankan Podman menggunakan *systemd* secara *rootless*.

## Menyiapkan Roxy Linux Container Management

*Roxy Linux* adalah sebuah distribusi *Linux* yang diinisiasi oleh *Co-founder CentOS* Gregory Krutzer pada tahun 2021. Alasan dibuatnya distribusi ini adalah karena pada 8 Desember 2020 *RedHat* mengumumkan akan menghentikan pengembangan *CentOS*. *Roxy Linux* adalah sistem operasi enterprise open source yang didesain 100% *bug-for-bug* dengan *RedHat Enterprise Linux*. 

*Roxy Linux* didistribusikan dalam tiga paket ISO, yaitu: *Minimal, DVD* dan *Boot*. Penulis menggunakan *Boot ISO* dengan pilihan mode *Server* dan paket tambahan *Container Management*. Setelah proses instalasi selesai, *Podman* sudah terpasang, tinggal melakukan beberapa konfigurasi yang akan saya jelaskan kemudian.

Untuk memudahkan dalam pengelolaan server bisa mengaktifkan Cockpit, dengan menjalankan perintah:

    sudo systemctl enable --now cockpit.socket

Selain menggunakan *Cockpit*, administrasi server *Linux* yang paling populer menggunakan *SSH*. Anda dapat me-*remote* server tanpa password (*passwordless*), *passwordless* diperlukan untuk mengelola *Podman* secara *remote*, yang akan saya jelaskan di bagian akhir tulisan ini.

Langkah-langkah membuat koneksi *SSH* tanpa password:

Buat *SSH key* dengan perintah:

    ssh-keygen -t rsa

Buka *Cockpit*: https://[server_address]:9090, kemudian login dengan user linux yang telah dibuat sebelumnya. Klik menu **Account** di samping kiri, klik nama anda, pada **Authorized public SSH keys** klik tombol **Add key**, masukkan isi teks *id_rsa.pub* yang telah dibuat menggunakan *ssh-keygen*, lalu simpan. Sekarang anda bisa login ke server dengan mode *password less*.

Sekarang anda dapat login *SSH* ke server tanpa menggunakan password menggunakan *Putty* atau *SSH* client di *Windows*, *Windows 11* sudah disertai *SSH client*.

## Mengelola container, image dan pod dengan Podman

*Podman* dikembangkan untuk meningkatkan desain dari *Docker*. Berikut perbandingan antara *Podman* dengan *Docker*:

| Feature | Podman | Docker | Description |
| :------ | :----: | :----: | :---------- |
| Support all OCI and Docker images | Y | Y | Pulls and runs container images from cantainer registries. |
| Launches OCI container engines | Y | Y | Lauches runc, crun, Kata, gVisor, and OCI container engines. |
| Simple command-line interface | Y | Y | Podman and Docker share the same CLI. |
| Integration with systemd | Y | N | Podman support running systemd inside container as well as many systemd features. |
| Fork/exec model | Y | N | The container is a child of the command. |
| Fully suppors user namespace | Y | N | Only Podman support running containers in separate user namespaces. |
| Client-server model | Y | Y | Docker is a REST API daemon. Podman supports REST APIs via a systemd socket-activated service. |
| Support docker-compose | Y | Y | Compose scripts work against both REST APIs. Podman works in rootless mode. |
| Support docker-py | Y | Y | Docker-py Python bindings work against bot REST APIs. Podman works in rootless mode. Podman also support podman-py for running advances features. |
| Daemonless | Y | N | The Podman command runs like a traditional command-line tool, while Docker requires multiple root-running daemons. |
| Supports Kubernetes-like pods | Y | N | Podman supports running multiple containers within the same pod. |
| Support Kubernetes YAML | Y | N | Podman can launch containers and pods bases on Kubernetes YAML. |
| Support Docker Swarm | N | Y | Podman beleave the future for orchestrated multinode containers is Kubernetes and does not plan on implementing Swarm. |
| Customizable registries | Y | N | Podman alows yout tu configure registries for short-name expansion. Docker is hardcoded to docker.io when you specify a short name. |
| Customizable defaults | Y | N | Podman support fully customizing all of its defaults, including security, namespaces, and volumes. |
| macOS support | Y | Y | Podman and Docker support running containers on a Mac via a VM running Linux. |
| Windows support | Y | Y | Podman and Docker support running container on Windows WSL2 or a VM running Linux. |
| Linux support | Y | Y | Podman and Docker are supported on all major Linux distributions. |
| Conatainers aren't stopped on software upgrade | Y | N | Podman is not required to remain running when containers are running. Since the Docker daemon is monitoring containers, by default, when it stops, all containers stop |  

Fitur *Podman* yang paling signifikan adalah kemampuan berjalan dalam mode *rootless*.

## Menjalankan Podman Mode Rootless dengan Systemd

    loginctl enable-linger
    systemctl --user enable --now podman.socket
    podman --remote info
    mkdir -p $HOME/.config/systemd/user
    podman generate systemd myapp > $HOME/.config/systemd/user/myapp.service
    systemctl --user enable --now myapp

## Mengelola Podman secara remote

*Podman* juga dapat di kelola secara *remote*, penulis me-*remote Podman* menggunakan terminal *Windows 11*. Langkah-langkah mengaktifkan *Podman remote* sebagai berikut:

Install *Podman* di *Windows*, saya menggunakan *Podman* versi yang sama dengan server yaitu 4.2.0. *Podman for Windows* dapat diunduh di: [GitHub](https://github.com/containers/podman/releases/download/v4.2.0/podman-v4.2.0.msi).

Buat koneksi *Podman* ke server dengan perintah seperti berikut (hanya contoh, tiap user tidak sama):

    podman system connection add suyadi --identity c:\Users\Suyadi\.ssh\id_rsa ssh://suyadi@192.168.1.2:22/run/user/1000/podman/podman.sock

Keterangan:
- `suyadi` adalah nama koneksi
- `c:\Users\Suyadi\.ssh\id_rsa` adalah file yang dibuat dengan perintah `ssh-keygen -t rsa`
- `/run/user/1000/podman/podman.sock` adalah *file socket Podman* untuk user *suyadi*.

Test koneksi dengan perintah:

    podman -c suyadi info

Apabila muncul kira-kira seperti berikut, berarti *Podman remote* sudah siap digunakan, semua perintah *Podman* bisa dijalankan secara remote:

    ...
    version:
      APIVersion: 4.2.0
      Built: 1668531608
      BuiltTime: Tue Nov 15 12:00:08 2022
      GitCommit: ""
      GoVersion: go1.18.4
      Os: linux
      OsArch: linux/amd64
      Version: 4.2.0
