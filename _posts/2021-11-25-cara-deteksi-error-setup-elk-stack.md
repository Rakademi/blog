---
layout: post
title: Cara Deteksi Error Di Server Dengan Setup ELK Stack
author: Made Raditya Pujamurti
tags: [database]
cover: cover/setup-elk.jpg
excerpt: Ketika infrastruktur aplikasi kita jadi makin kompleks, akan makin susah untuk memonitor begitu banyak server yang ada. Sangat tidak efisien untuk mendeteksi error dengan cek server satu per satu. Salah satu solusinya adalah dengan memakai Elasticsearch, Logstash dan Kibana (ELK) stack. 

---

<iframe src="https://www.youtube.com/embed/kjOAQDJr1so" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Ketika infrastruktur aplikasi kita jadi makin kompleks, akan makin susah untuk memonitor begitu banyak server yang ada. Sangat tidak efisien untuk mendeteksi error dengan cek server satu per satu. 

Salah satu solusinya adalah dengan memakai [Elasticsearch, Logstash dan Kibana (ELK) stack](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html). 

Selain ELK, kita juga akan pakai Filebeat untuk mengambil isi dari log file yang kita mau. 

Kita akan setup ELK stack dan cari tau gimana caranya supaya bisa sampai ditampilin ke dashboard Kibana. Dengan solusi ini, kita bisa bikin dashboard untuk memfilter darimana sumber masalah tersebut. 

## Install Elasticsearch

![elasticsearch](/blog/images/blog/elk-stack/elasticsearch-intro.png)

Hal pertama yang kita perlu adalah tempat nampung data server log. Ada banyak pilihan untuk tugas ini tapi di sini kita akan pakai Elasticsearch.

[Elasticsearch](https://www.elastic.co/what-is/elasticsearch) adalah sebuah database yang dirancang khusus untuk pencarian teks. Database tipe ini sangat berguna ketika kita mau cari beberapa kata kunci misalnya kata kunci masalah yang tersimpan di server log.

Cara installnya bisa ikutin [dokumentasi lengkap ini](https://www.elastic.co/guide/en/elasticsearch/reference/7.15/targz.html#install-linux)

## Install Logstash & Filebeat

Setelah tempat penampungan datanya udah siap, sekarang gimana caranya buat ngirim data ke sana. 

Komponen yang berperan untuk ini adalah Logstash dan Filebeat.

![filebeat intro](/blog/images/blog/elk-stack/filebeat-intro.png)

[Filebeat](https://www.elastic.co/beats/filebeat) adalah sebuah komponen yang khusus dibuat untuk mengirim data dari berbagai sumber misalnya log file ke tempat tertentu. 

Kita bisa ikut proses instalasinya di [dokumentasi ini](https://www.elastic.co/guide/en/beats/filebeat/7.15/filebeat-installation-configuration.html#installation).

Begitu ada data baru di sumber tersebut, Filebeat akan secara otomatis mengirim data terbaru ke suatu tempat untuk diproses.

Suatu tempat di kasus ini adalah [Logstash](https://www.elastic.co/logstash/).

![logstash intro](/blog/images/blog/elk-stack/logstash-intro.png)

Logstash berfungsi untuk memproses berbagai macam data. Salah satunya adalah data yang dikirim oleh Filebeat.

Proses ini bisa merubah bentuk data, mengurangi atau menambah data sesuai kebutuhan. Sebagai contoh: delete data dengan kata kunci tertentu atau tentukan bagian data mana saja yang akan masuk di kategori tertentu.

Kenapa bagian ini penting utamanya adalah untuk memfilter data yang ada, jadi database kita gak penuh dengan data yang gak penting. Selain itu untuk menganalisa data nanti, data mentah harus diproses dulu.  

Manipulasi data ini bisa dilakukan dengan berbagai macam filter, salah satunya adalah Grok filter. Intinya, Grok akan mencocokan bagian log message dengan *regular expression*. Untuk informasi lebih lanjut, bisa baca di [artikel ini](https://logz.io/blog/logstash-grok/).  

Cara install Logstash bisa dibaca di [dokumentasi resminya](https://www.elastic.co/guide/en/logstash/7.15/installing-logstash.html#_apt).

## Install Kibana

Sejauh ini kita sudah punya database untuk menyimpan data log yang kita mau, mekanisme untuk mengirim data log dan tempat untuk memproses datanya. 

Sekarang kita perlu cara untuk membuat tampilan data nya supaya lebih menarik dan membantuk kita buat menemukan pola tertentu.

Komponen terakhir adalah bagian visualisasi data dengan [Kibana](https://www.elastic.co/kibana/).

![kibana intro](/blog/images/blog/elk-stack/kibana-intro.png)

Seperti gambar di atas, Kibana akan nampilin data yang ada di Elasticsearch dalam bentuk dashboard atau chart.

Install Kibana dengan ngikutin petunjuk di [sini](https://www.elastic.co/guide/en/kibana/7.15/targz.html#install-linux64).

Semoga bermanfaat!
