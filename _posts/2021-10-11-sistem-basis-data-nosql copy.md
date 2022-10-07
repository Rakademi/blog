---
layout: post
title: Sistem Basis Data NoSQL
author: Made Raditya Pujamurti
tags: [database]
cover: cover/database-nosql.jpg
excerpt: Penjelasan tentang sistem basis data NoSQL. Istilah ini gak punya definisi yang baku tapi arti yang paling sering diterima adalah Not Only SQL (disingkat jadi NoSQL). Semakin banyak tools atau jenis NoSQL yang kita tau, akan semakin mudah juga buat kita untuk bikin solusi masalah tertentu. Ini karena setiap tipe NoSQL punya keunggulan masing-masing. 
---

<iframe src="https://www.youtube.com/embed/PIQlNoZ4aI4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Definisi NoSQL

Sebenarnya istilah NoSQL ini gak punya definisi yang baku. 

Istilah ini dipake untuk nyebutin system basis data yang bukan SQL.

Nah karena definisinya gak baku, jadi NoSQL itu punya beberapa ciri-ciri. Misalnya, open source, distributed dan non relational.

Salah satu contoh NoSQL adalah Redis. Bentuk dokumen nya disebut key and value. Misalnya key nya itu adalah user ID dan value nya skor tertinggi. Redis itu juga distributed, yang artinya Redis bisa di install di banyak server untuk membentuk satu klaster besar. 

## Kenapa perlu NoSQL?

![but why](https://media2.giphy.com/media/1M9fmo1WAFVK0/giphy.gif?cid=ecf05e47wpq82rxb49yz0teggxj2u66te2qzmolsc7b4gywr&rid=giphy.gif&ct=g)

Mungkin kita ada yang pernah denger debat sengit antara kubu SQL dan NoSQL. 

NoSQL itu dibuat karena makin banyaknya tipe data dan aplikasi yang ada sekarang. 

Basis data tipe SQL kadang gak bisa memenuhi kebutuhan itu. Contohnya, ada data baru dari sistem internet of things (IoT) yang strukturnya beda dari skema tabel SQL yang ada. 

### Skema data flexibel

Mungkin kita yang udah akrab dengan SQL tau kalau mau merubah skema itu gak gampang. Perlu pindahin data dari tabel yang lama ke skema tabel yang baru. 

Bayangkan kalau data di tabel lama itu udh sangat banyak, migrasi datanya akan sangat lama.

Oleh karena itulah NoSQL dibuat, contohnya MongoDB. Kalau ada skema baru yang beda dari skema yang lama, si Mongo gak akan komplain. 

Tapi saya baru bilang positifnya dari NoSQL. Negatif nya juga ada. Tapi nanti kelamaan bahas point ini, yang jelas kita jangan beranggapan SQL itu udh ketinggalan jaman dan semua harus pakai NoSQL.

### Bisa horizontal scaling

![horizontal scaling](/blog/images/blog/nosql/2.jpg)

Alasan kedua adalah scalability. 

Sistem basis data SQL biasanya hanya bisa vertical scaling. Artinya di satu server, kita perlu tambah RAM atau HDD karna data nya udah gak muat lagi. 

Tapi tentu ada batasan seberapa besar kita bisa upgrade komponen ini. 

Syukur gak pake GPU, dijaman harga GPU pada mahal gini kalo perlu upgrade grafik card pasti tekor 💸

NoSQL dari design nya memang udh dibuat supaya gampang untuk horizontal scaling. Scaling tipe ini maksudnya nambah server lagi buat dijadiin kluster. 

Contohnya adalah MongoDB lagi, dari sana nya udh ada fitur sharding. 

Fitur ini bisa bantu kita untuk nambah server kalau perlu. 

Sharding itu akan ngasi suatu mekanisme supaya kumpulan server ini bisa kerja sama. Seolah-olah yang request data itu ngeliat kumpulan server ini satu basis data yang super besar.

## Ragam NoSQL

Sejauh ini ada 4 jenis NoSQL:

- Yang pertama tipe *key value* contohnya redis dan RocksDB.

- Kedua ada *document store* misalnya Mongodb dan Couchdb.

- Terus ada juga *column family* ada google Bigtable dan Cassandra.

- Kalau *graph* DB itu misalnya GraphDB dan Neo4j. 

Jadi ada banyak tipe tapi kita gak usah bingung. Yang penting kita tau ada tipe atau tools apa saja dan kegunaan nya itu apa. 

Karena tiap tipe ini punya kelebihan tertentu untuk tipe masalah tertentu.

## Teori CAP

![CAP theorem](/blog/images/blog/nosql/1.png)

[sumber gambar](https://miro.medium.com/max/473/1*rxTP-_STj-QRDt1X9fdVlA.png)

Lanjut ke konsep berikutnya yaitu CAP theorem. Huruf C, A dan P itu artinya *consistency, availability dan partition*. Jadi intinya, teori ini bilang kalau kita cuma bisa pilih 2 dari 3 huruf ini. 

Dan disini kita bisa liat, kalau misalnya mau database yang konsisten dan available itu pakai MySQL. 

Terus cara pilihnya gimana? Itu balik lagi tergantung masalah dan konteksnya. 

Kalau misalnya kita lagi ngerjain aplikasi yang menyangkut hal sensitif misalnya system uang di bank: 
di sini kita pengennya system yang konsisten dan available. Kalau gak konsisten nanti misalnya kita transfer uang ke keluarga terus ada error di sistemnya, di database jumlah saldo udah dikurangi tapi uangnya belum di transfer karena error. 

Nah system seperti ini perlu pilih huruf, C dan A, misalnya pake system SQL. 

Tapi kalau misalnya fokus aplikasinya itu lebih ke supaya di kedepan hari bisa horizontal scaling lebih gampang, mungkin karena datanya bakal membludak dan kadang ada downtime nya sedikit itu gak masalah. Downtime maksudnya sistemnya dalam perbaikan karena error. Di kasus ini lebih cocok pakai MongoDB.

## Kesimpulan
Ada banyak macam NoSQL dan tiap tipe itu ada kelebihan dan kekurangan tertentu. Jadi menurut saya daripada kita susah payah debat mana yang lebih bagus, sebaiknya kita pakai kombinasi dari teknologi ini. Pada akhirnya kita pengen supaya produk kita itu reliable dan performanya bagus. 

Semoga bermanfaat!