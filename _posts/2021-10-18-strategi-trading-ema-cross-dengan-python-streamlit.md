---
layout: post
title: Strategi Trading EMA Cross Dengan Python Dan Streamlit
author: Made Raditya Pujamurti
tags: [data_science, algo_trading]
cover: cover/ema-cross.jpg
excerpt: Salah satu strategi trading yang paling simpel dan populer adalah Exponential Moving Average (EMA cross). Dengan bantuan python dan Streamlit, kita bisa aplikasikan strategi ini dengan membuat sebuah aplikasi web. Kita perlu mengolah data yang kita punya untuk menghitung EMA. 
---

<iframe src="https://www.youtube.com/embed/Ykkye1_zOsY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> Disclaimer: not financial advice

Salah satu strategi trading yang paling simpel dan populer adalah Exponential Moving Average (EMA cross). Dengan bantuan python dan Streamlit, kita bisa aplikasikan strategi ini dengan membuat sebuah aplikasi web. Kita perlu mengolah data yang kita punya untuk menghitung EMA. 

Jadi selain belajar tentang strategi trading, kita juga akan belajar contoh manipulasi data yang sederhana. Di studi kasus ini, kita akan membuat suatu aplikasi yang berguna dengan python dan streamlit, terutama untuk teman-teman yang ingin belajar trading saham atau forex. 🚀 

**WARNING**: Tapi ingat, tidak ada strategi yang sempurna dan trading itu perlu sangat berhati-hati. Perlu money management, testing dan pengalaman yang banyak untuk bisa profit konsisten. Sebagian besar orang yang mencoba trading akhirnya gagal. Jadi semoga dengan program yang kita buat ini, kita jadi bisa lebih hati-hati sebelum mulai trading dan jangan percaya dengan claim yang bisa ngasi keuntungan yang pasti. 

Artikel ini adalah lanjutan dari [postingan pertama](/blog{% post_url 2021-10-06-bikin-aplikasi-monitor-harga-saham-forex-dengan-python-streamlit %}) tentang python dan Streamlit.

## Langsung ke kodingan!

Kode lengkap bisa dilihat di:

[ema_cross.py](https://gist.github.com/Rakademi/0127e1f6f50becdc955824f18e993b5e)

[util.py](https://gist.github.com/Rakademi/7f206b46762902e7b67f96725f29f4ef)

Sekarang kita bahas beberapa komponen penting di dalam kode tersebut.

### Input untuk periode EMA

```py
ema1 = st.sidebar.text_input('EMA 1', 20)
ema2 = st.sidebar.text_input('EMA 2', 50)
```

Di sini kita akan tampilkan 2 EMA jadi kita bisa ambil 2 user input dari sidebar untuk periode EMA. Persilangan kedua EMA ini akan menentukan apakah kita *buy* atau *sell*.

### Menghitung EMA

{% highlight python linenos %}
def get_ema(ticker_data, period):
    ema = ticker_data['Close'].ewm(span=period).mean()
    column_name = 'ema_' + str(period)
    ticker_data[column_name] = ema
    return ticker_data
{% endhighlight %}

Kita bisa dapetin EMA dengan gampang berkat function pandas dataframe [`ewm()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.ewm.html) dan [`mean()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.mean.html) di line 2.

### Menggambar garis EMA di candlestick chart

{% highlight python linenos %}
def add_ema_trace(candle_fig, timestamp, ema, trace_name, color):
    candle_fig.add_trace(
        go.Scatter(
            x=timestamp,
            y=ema,
            name=trace_name,
            line=dict(color=color)
        )
    )
    return candle_fig
{% endhighlight %}

Function di atas berasumsi kita udah punya candlestick chart untuk diisi di parameter. Candlestick chart ini akan diberi dekorasi sesuai kebutuhan kita.

Setiap kali kita mau gambar garis EMA, kita bisa pakai function `add_trace()` di line 2. Di dalamnya bisa kita tambahkan bermacam-macam jenis element chart, misalnya [Plotly Scatter](https://plotly.com/python-api-reference/generated/plotly.graph_objects.Scatter.html). 

## Hasil akhir

Setelah semua kodingan selesai dan aplikasi dijalankan dengan `streamlit run`, hasilnya akan seperti ini:

![Hasil akhir aplikasi web dengan python dan streamlit](/blog/images/blog/streamlit_intro/ema-cross.png)

Selamat bereksperimen!