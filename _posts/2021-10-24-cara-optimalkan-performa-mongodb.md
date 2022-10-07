---
layout: post
title: Cara Optimalkan Performa MongoDB
author: Made Raditya Pujamurti
tags: [database]
cover: cover/database-boost-mongodb.jpg
excerpt: Cara yang penting untuk mengoptimalkan salah satu database atau basis data nosql MongoDB ada di konfigurasi server dan MongoDB service. Di artikel ini kita akan cari tau konfigurasi apa aja yang bisa mempercepat kinerja MongoDB agar lebih optimal. 
---

<iframe src="https://www.youtube.com/embed/Q9FqoTVwcj0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Kenapa perlu *tune up* settingan?

Untuk database dalam tahap produksi, ada beberapa hal yang perlu kita ubah supaya performa MongoDB bisa maksimal.

Mungkin kita bakal nanya, kenapa gak dari awal MongoDB kasi settingan yang optimal? Disini kita bisa bayangin, pihak MongoDB gak tau kita mau install MongoDB di OS yang mana. Apakah di linux, windows atau yang lainnya. 

Jadi settingan default nya hanya ditargetkan supaya banyak pengguna bisa nyobain MongoDB.

Tips yang akan saya sebutin ini khusus untuk OS linux. karena OS ini salah satu yang paling banyak digunakan di tahap produksi.


## Non-aktifkan NUMA

![ilustrasi NUMA](/blog/images/blog/optimalkan_mongodb/1.png)

Kita perlu non aktifkan NUMA atau [non uniform memory access](https://en.wikipedia.org/wiki/Non-uniform_memory_access). NUMA dapat membantu di beberapa skenario. 

Tapi untuk database, biasanya disarankan untuk mematikan NUMA. 


Tujuan adalah supaya alokasi memori di setiap node yang ada bisa lebih merata. Kalau gak, misalnya sewaktu ada proses yang memerlukan memori di node 0, memori yang ada disana harus di *swap* supaya bisa ngasi ruang untuk process ini.

![ilustrasi NUMA](/blog/images/blog/optimalkan_mongodb/2.png)

Swap ini membuat performa database berkurang karena data penting udah gak ada di memori setelah di swap.

## Pakai SSD

Tips berikutnya adalah denagn pakai SSD. Kalau pake SSD itu rasanya laptop yang jadul pun jd seperti hidup kembali. 

Tapi kalau misalnya karena budget atau alasan lain harus pake HDD, minimal pakai RAID 10. Hindari RAID 5 dan 6 karena RAID tipe ini lebih rentan error. 

Kalau RAID 10 lebih aman karena ada system duplikasi data. 
Dan data yang ada disana dibagi menjadi beberapa partisi. Jadi bisa di akses secara bersamaan. 

Dengan kata lain performa nya jd lebih kencang! 

![super fast](https://media4.giphy.com/media/d4blalI6x2oc4xAA/giphy.gif?cid=ecf05e476flcyyxk6ke4mcpqqv5sfj0n128ja9449okvr1tf&rid=giphy.gif&ct=g)

## Pakai XFS
XFS paling cocok untuk MongoDB terutama dengan Wired Tiger storage engine. 

Storage engine yang dulu, namanya mmapv1 udah gak disupport lagi di versi MongoDB yang baru jadi sbaiknya pakai XFS filesystem di linux.

## Setting ULIMIT
Next, kita perlu cek ULIMIT. 

ULIMIT dibuat supaya satu user gak terlalu pakai banyak komponen atau resource yang ada. 

Tapi untuk MongoDB, satu server biasanya didedikasikan untuk database dan setting default yang ada sering kali terlalu rendah. Akibatnya kadang bakal ada masalah di performa MongoDB. 

Kita bisa cek ULIMIT dengan ketik `ULIMIT -a`. Buat gantinya bisa run `sudo vim /etc/security/limits.conf`. Rekomendasi nilainya adalah di atas 64000.

## Sesuaikan WiredTiger internal cache

WiredTiger internal cache ini bisa dikecilkan supaya ada lebih banyak ruang untuk filesystem cache. 

Kenapa perlu lebih bnyak filesystem cache? 

Ini karena data yang ada di dalam filesystem cache itu masih terkompresi seperti di SSD atau HDD. 

Kalau di dalam WiredTiger cache, data nya gak terkompresi karena MongoDB gak bisa memproses data yang masih terkompresi. 

![ilustrasi internal cache](/blog/images/blog/optimalkan_mongodb/3.png)

Jadi dengan jumlah memori yang sama, data yang bisa tertampung jadi lebih banyak kalau kita punya lebih banyak filesystem cache. 

Semakin banyak data yang di RAM, peroforma MongoDB akan semakin cepat. Ini karena MongoDB gak perlu sering ngambil data dari SSD/HDD.

Tapi gak ada yang gratisan. Pasti ada harga yang harus di bayar. Kalo tidak, MongoDB bakal bikin WiredTiger cache nya sekecil mungkin. 

Harga ini salah satunya CPU load yang lebih tinggi. karena MongoDB perlu memprosess data yang terkompresi itu menjadi data yang gak terkompresi. Tapi menurut saya gak masalah, selama si CPU masih punya kapasitas untuk lebih sibuk.

Semoga bermanfaat!