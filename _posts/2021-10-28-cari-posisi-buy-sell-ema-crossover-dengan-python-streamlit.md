---
layout: post
title: Cari Posisi Buy Sell Strategi EMA Crossover Dengan Python Dan Streamlit
author: Made Raditya Pujamurti
tags: [data_science, algo_trading]
cover: cover/show-crossing-dot.jpg
excerpt: Sering kali trader pemula tidak backtest strategi yang akan dipakai. Hal ini penting untuk tau seberapa bagus performa strategi itu di masa lalu. Dengan aplikasi python ini, kita gak perlu test strateginya dengan manual. 
---

<iframe src="https://www.youtube.com/embed/993ukhqwpoM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> Disclaimer: not financial advice

Sering kali trader pemula tidak backtest strategi yang akan dipakai. Hal ini penting untuk tau seberapa bagus performa strategi itu di masa lalu. Dengan aplikasi python ini, kita gak perlu test strateginya dengan manual. 

Jadi tidak repot untuk kita liatin persilangan EMA satu per satu. Waktu yang diperlukan kalau test tanpa program ini akan sangat lama, terutama kalau kita mau test dengan data yang banyak, di tiap saham dan interval yang berbeda. 

Program yang kita buat ini bakal ngasi list trade yang bisa di ambil dan tampilin trade nya di candle chart. Proses backtesting jadi lebih cepat dan tidak melelahkan 🚀

Artikel ini adalah lanjutan dari [postingan ini](/blog{% post_url 2021-10-18-strategi-trading-ema-cross-dengan-python-streamlit %}) tentang penjelasan dasar untuk EMA crossover dengan python dan Streamlit.

## Show me the code!

Kode lengkap bisa dilihat di [sini](https://gist.github.com/Rakademi/e47d7f199d07c0ad8621ee2521639d58) dan [sini](https://gist.github.com/Rakademi/cdd3b38e2101053da18a7d7d58c130f7).

Sekarang kita bahas beberapa komponen penting di dalam kode tersebut.

### Bikin daftar trades

{% highlight python linenos %}
def create_ema_trade_list(ticker_data, ema1_col_name, ema2_col_name):
    ticker_data['ema_diff'] = ticker_data[ema1_col_name] - ticker_data[ema2_col_name]
    prev_state = 'unknown'
    trades = []
    for i in range(len(ticker_data)):
        if ticker_data['ema_diff'][i] >= 0:
            state = 'positive'
        else:
            state = 'negative'
        if prev_state != 'unknown':
            if state == 'positive' and prev_state == 'negative':
                 try:
                     trade = str(ticker_data.index[i+1]) + ',' + str(ticker_data['Open'][i+1]) + ',cyan,buy'
                     trade = trade.split(',')
                     trades.append(trade) 
                 except:
                    continue
            elif state == 'negative' and prev_state == 'positive':
                 try:
                    trade = str(ticker_data.index[i+1]) + ',' + str(ticker_data['Open'][i+1]) + ',magenta,sell'
                    trade = trade.split(',')
                    trades.append(trade) 
                 except:
                    continue
        prev_state = state
    return trades
{% endhighlight %}

- Line 2: untuk mendeteksi persilangan kedua EMA, kita bisa hitung selisih kedua EMA tersebut. Sebagai contoh, kalau EMA yang periodenya lebih rendah (lebih lincah) akan crossing ke atas EMA yang punya periode lebih tinggi (lebih lambat), selisihnya akan berubah dari positif ke negatif (begitu juga sebaliknya).
- Line 11: disini kita dapat sinyal buy. Selisih sebelumnya (`prev_state`) adalah negatif dan selisih sekarang adalah positif. Dengan kata lain EMA yang lebih lincah crossing ke atas EMA yang lebih lambat. 
- Line 13: ambil data yang akan berguna untuk bikin daftar trades. Tambahkan beberapa informasi penting seperti warna untuk membedakan buy/sell di chart (misalkan *cyan* untuk buy)

### Gabungkan daftar trades ke dataframe

{% highlight python linenos %}
def join_trades_to_ticker_data(trades, ticker_data):
    trades_df = pd.DataFrame(trades, columns=['Time','Trade Price','Trade Color','Trade Type']).set_index('Time')
    trades_df['Trade Price'] = trades_df['Trade Price'].astype(float).round(4)
    ticker_data = pd.concat([ticker_data, trades_df], axis=1, join='outer')
    return ticker_data
{% endhighlight %}

Kita bisa gabungkan 2 dataframe dengan metode [`concat()`](https://pandas.pydata.org/docs/reference/api/pandas.concat.html) di line 4. Di metode tersebut kita bisa atur prosedur untuk `join`. 

Di kasus ini kita mau semua baris yang ada di dataframe `ticker_data`, walaupun tidak ada value di dalam dataframe `trades_df`. Untuk itu kita bisa pakai pilihan `outer` seperti SQL outer join. Selain itu set juga `axis=1` karena kita ingin menggabungkan kolom dan bukan baris.  

### Tampilkan chart dengan trades

{% highlight python linenos %}
def add_trades_trace(candle_fig, ticker_data):
    candle_fig.add_trace(
        go.Scatter(
            x = ticker_data.index,
            y = ticker_data['Trade Price'],
            name = 'Trade Triggers',
            marker_color = ticker_data['Trade Color'],
            mode = 'markers'
        )
    )
    return candle_fig
{% endhighlight %}

Hampir sama dengan saat menambahkan garis EMA, kita bisa pakai function `add_trace` tapi set `mode = 'markers'` di line 8 supaya menjadi titik-titik dan bukan garis.

Warna untuk trades adalah *cyan* untuk buy dan *magenta* untuk sell (line 7) dan oleh karena itu kita tambahkan `Trade Color` di `ticker_data` pada saat membuat daftar trades. Warna ini bisa disesuaikan saat membuat daftar trades. 

## Hasil akhir

Setelah semua kodingan selesai dan aplikasi dijalankan dengan `streamlit run`, hasilnya akan seperti ini:

![Hasil akhir aplikasi web dengan python dan streamlit](/blog/images/blog/streamlit_intro/show-crossing-dot.png)

Selamat bereksperimen!