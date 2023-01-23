---
layout: post
title:  "`Rootless container` di RoxyLinux 9 dengan Podman"
date: 2023-01-23 13:05:00 +0700
categories: devel infrastruktur
---

Tulisan ini menjelaskan:

- Menyiapkan RoxyLinux Container Management
- Mengelola container dan image
- Mengelola pod
- Menyiapkan rootless service dengan systemd

Tulisan ini akan menjelaskan secara singkat proses instalasi `RoxyLinux` sebagai server `container management`. Untuk memudahkan, server dilengkapi dengan `Cockpit web based interface` untuk server.

boot iso -> Server -> add: Container management
sudo systemctl enable --now cockpit.socket

loginctl enable-linger
systemctl --user enable --now podman
podman --remote info
