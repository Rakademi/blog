---
layout: post
title: Cara Gratis Membuat Website Content Management System (CMS) Dengan Github Page
author: Made Raditya Pujamurti
tags: [general]
cover: cover/setup-jekyll.jpg
excerpt: Di artikel ini teman-teman bisa cari tau gimana caranya setup website pribadi yang ada fitur CMS dengan cepat dan mudah plus gratis! Sudah langsung hosting dengan Github Page jadi tidak perlu repot buat mikirin hosting dimana. Lebih kerennya lagi, website kita bisa dimodifikasi asalkan kita bisa HTML dan css.
---

<iframe src="https://www.youtube.com/embed/Xh2tp8GHx8s" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Di [artikel sebelumnya](/blog{% post_url 2021-09-15-tips-belajar-programming %}) saya ngasi saran untuk membuat website pribadi. 

Bikin nya bisa pakai HTML, CSS dan Javascript. Terus host di [Github Page](https://pages.Github.com/) supaya bisa di akses semua orang dengan gratis. 

Salah satu hal yang bisa dibuat lebih bagus adalah content management. 

Artinya artikel yang kta publikasikan bisa ditampilkan secara teratur. Misalnya hanya tampilkan 3 artikel terbaru dan sisanya bisa di explore di page lain. 

Website kita akan jadi lebih rapi dan performanya akan lebih cepat.

Di artikel ini saya akan berbagi cara untuk bisa seperti itu dengan bantuan [Jekyll](https://jekyllrb.com/).

## Mengenal Jekyll

![jekyll homepage](/blog/images/blog/jekyll/jekyll-homepage.png)

[Jekyll](https://jekyllrb.com/) termasuk dalam kategori site generator. Intinya, Jekyll akan bantu kita untuk merubah konten atau artikel yang kita buat jadi HTML, CSS dan Javascript yang kemudian bisa kita upload ke Github Page. 

Jadi kita gak usah repot ngedit HTML dan CSS setiap kita mau bikin konten baru.

Selain itu Jekyll juga didesain untuk membuat blog. Pengaturan konten/artikel udah ada secara otomatis jadi kita gak perlu manual ngatur artikel mana yang tampil di atas, berdasarkan kategori dsb.

Blog ini juga dibuat dengan Jekyll dan pengaturan kontennya jadi sangat gampang. 

## Cara setup

### Akun Github

Pertama kita perlu akun Github. Kita bisa tonton penjelasan tentang Github di channel keren [Web Programming Unpas](https://youtu.be/65Jv9Y13eVo).

Di sana penjelasan nya sudah lengkap jadi saya tidak ulangi di sini.

Kita bisa install Jekyll dengan ngikutin dokumentasi tapi ada cara yang lebih gampang. 

### Jekyll Now

[Jekyll Now](https://github.com/barryclark/jekyll-now) adalah sebuah repository yang sangat berguna untuk bantu kita setup Jekyll dengan cepat.

Dengan Jekyll Now kita udah bisa bikin blog yang simpel tanpa perlu install Ruby, dll. Ngedit atau nambahin konten bisa langsung dari website Gitub.

Detail untuk cara setup Jekyll Now sudah lengkap dijelaskan di bagian README, yang secara garis besarnya:

1. Fork repository ke akun Github masing-masing
1. Ubah nama repository menjadi `<username>.github.io`
1. Edit `_config.yml` dengan pengaturan sesuai kebutuhan 
1. Tunggu beberapa menit dan Github page akan muncul

Sangat ringkas dan cepat 🚀

Tapi kekurangannya adalah akan sangat sulit untuk ngubah desain website kita. Supaya website kita punya desain yang lebih custom, kita tetep perlu setup Jekyll di komputer kita.

Semoga bermanfaat!