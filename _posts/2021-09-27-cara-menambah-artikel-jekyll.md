---
layout: post
title: Cara Gampang Menambah Artikel Di Website Pribadi Dengan Github Page Dan Jekyll
author: Made Raditya Pujamurti
tags: [general]
cover: cover/add-article-jekyll.jpg
excerpt: Penjelasan tentang menambah artikel di Github Page dan Jekyll. Github Page sangat berguna kalau kita mau hosting website pribadi dengan gratis. Yang lebih serunya lagi, kita bisa tambah fitur content management system (CMS) di Github Page dengan Jekyll.
---

<iframe src="https://www.youtube.com/embed/UIrRt33tOpg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Ini adalah lanjutan [artikel sebelumnya](/blog{% post_url 2021-09-21-setup-website-gratis-cms-github-page %}) tentang membuat website pribadi dengan Github Page dan Jekyll.

Di artikel sebelumnya kita udah bahas tentang Jekyll. Sekarang gimana caranya buat nambahin artikel di blog atau portfolio.

Ada banyak cara yang bisa kita pilih buat nambah atau edit konten websitenya. Bisa edit repository lokal yang ada di laptop, tapi kali ini kita akan tambah artikel langsung dari website Github.

Cara ini sangat simpel dan bisa langsung selesai cuma dari Github.

## Buat file markdown

Pertama di text editor pilihan masing-masing buat file markdown. Bisa pakai Notepad, Microsoft Word dan sebagainya. 

Tapi di sini saya pakai Visual Studio Code karena lebih gampang untuk liat preview hasilnya seperti di bawah.

![contoh markdown](/blog/images/blog/jekyll/md-example.png)

Tapi teman-teman juga bisa ketik langsung file markdown nya di website Github. Nanti di akhir artikel kita bahas caranya. 

Nama filenya harus ngikutin format: `tahun-bulan-tanggal-nama-file.md`. Contohnya: `2021-09-27-blog1.md`

Format ini bakal dipake sama Jekyll buat nentuin tanggal publikasi artikel kita. 

## Isi konten markdown

Sekarang kita isi konten file markdown nya. 

Pertama adalah bagian header. 

```md
---
layout: post
title: Project Pertama
cover: cover/add-article-jekyll.jpg
excerpt: Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed a euismod arcu. Integer pretium orci mauris, nec eleifend eros venenatis et. Donec lacinia pellentesque tincidunt.
---
```

### Layout

Layout ini akan dipakai Jekyll untuk nentuin format HTML yang bakal dibikin nanti. Jadi di dalam folder `layout`, ada file `post.html`. File ini isinya adalah template untuk layout yang kita pilih. 

![layout folder](/blog/images/blog/jekyll/layout-folder.png)

Isi title, cover dan excerpt atau deskripsi singkat sesuai kebutuhan. 

### Konten postingan

Di bagian ini kita bisa  atur format tampilannya dengan ngikutin aturan dari file markdown.

Misalnya buat nampilin di HTML jadi H1, kita bisa tambahkan '#' di depan kalimat tertentu. 

![contoh markdown](/blog/images/blog/jekyll/md-example.png)

Bisa kita lihat dari contoh di atas, ngatur format artikel jadi lebih gampang dan rapi dibanding kita perlu bikin `<h1></h1>` tiap kali mau bikin tulisan yang besar.

Ada banyak syntax markdown yang kita bisa pakai buat ngatur format konten. Untuk selengkapnya bisa lihat di [cheat sheet](https://www.markdownguide.org/cheat-sheet/) ini.


## Upload ke Github

Setelah isi artikel kita udah mantap. Cara upload ke Github sangat gampang.

1. Di Github, buka repository blog
1. Klik folder `_posts` 
1. Pilih 'Add file' 
1. Klik 'Upload files'

![upload ke github](/blog/images/blog/jekyll/github-upload-file.png)

Upload file markdown yang kita bikin tadi. 

Alternatifnya adalah pilih 'Create new file' dan bisa ketik file markdown nya langsung di website Github.

Selesai. Tunggu beberapa saat buat Github sync artikel barunya. Gampang kan 😎

Semoga bermanfaat!