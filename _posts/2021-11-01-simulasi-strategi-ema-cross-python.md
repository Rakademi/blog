---
layout: post
title: Cara Simulasi Strategi Trading Dengan Python
author: Made Raditya Pujamurti
tags: [data_science, algo_trading]
cover: cover/simulate-ema.jpg
excerpt: Cara test strategi trading dengan bikin aplikasi web yang bisa simulasi menggunakan python, gimana caranya? Di artikel ini kita akan belajar gimana python programming bisa membantu kita untuk test sebuah strategi trading saham. Belajar python pun jadi lebih bermanfaat! 
---

<iframe src="https://www.youtube.com/embed/LxAdm8rajag" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> Disclaimer: not financial advice

Cara test strategi trading dengan bikin aplikasi web yang bisa simulasi menggunakan python, gimana caranya? Di artikel ini kita akan belajar gimana python programming bisa membantu kita untuk test sebuah strategi trading saham. Belajar python pun jadi lebih bermanfaat! 

Strategi yang kita gunakan disini adalah strategi yang lumayan populer yaitu EMA crossover. Sebelum kita pakai strategi tertentu untuk trading uang beneran, sangat penting untuk kita tau berapa peluang profit strategi tersebut. 

Dengan simulasi ini kita juga jadi paham dengan ekspektasi probabilitas yang bisa diharapkan. Jadi kita tidak takut untuk mengambil suatu keputusan karena sudah di backing dengan data. 

Walaupun, hasil di masa lalu tidak selalu akan berulang di masa depan.

Artikel ini adalah lanjutan dari [postingan ini](/blog/{% post_url 2021-10-28-cari-posisi-buy-sell-ema-crossover-dengan-python-streamlit %}) tentang penjelasan dasar untuk EMA crossover dengan python dan Streamlit.

## Show me the code!

[simulate_ema_cross.py](https://gist.github.com/Rakademi/a56facfaa6fd081f206010eb8c638e14)

[util.py](https://gist.github.com/Rakademi/8d0e59d09222014c9bfcff974bf38a46)

Misi utama kita adalah menghitung seberapa besar untung/rugi setiap trade dengan otomatis.

### Menerima user input

{% highlight python linenos %}
data_period = st.sidebar.text_input('Period', '10d')
data_interval = st.sidebar.radio('Interval', ['15m','30m','1h','1d'])
ema1 = st.sidebar.text_input('EMA 1', 20)
ema2 = st.sidebar.text_input('EMA 2', 50)
share_amount = st.sidebar.text_input('Number of Shares', 1)
initial_capital = st.sidebar.text_input('Initial Capital (USD)', 10000)
{% endhighlight %}

Ada beberapa hal yang perlu diperhatikan:
- line 1: periode jumlah data yang akan dipakai, default value nya adalah '5d' atau 5 hari. Semakin banyak data akan semakin bagus.
- line 2: interval satu candle stick. Interval yang lebih kecil dari '1d' akan dibatasi periodenya maksimal sekitar '60d' oleh library yfinance. Ada pilihan interval lebih kecil misalnya '1m' tapi sebaiknya jangan pakai interval yang terlalu kecil supaya terhindar dari *overtrading*.

### Cari profit/loss setiap trade

{% highlight python linenos %}
def simulate_ema_cross_trading(trades):
    results = []
    buy_price = 0
    for trade in trades:
        if trade[3] == 'buy':
            buy_price = float(trade[1])
        elif buy_price != 0:
            sell_price = float(trade[1])
            results.append(sell_price - buy_price)
    return results
{% endhighlight %}

Salah satu cara yang lumayan gampang adalah dengan menghitung selisih harga saat buy (line 6) lalu sell (line 8), begitu juga sebaliknya (line 9). 

Cara menghitung ini punya asumsi kita hanya sell setelah open posisi buy.

Kita bisa juga buat supaya bisa open posisi sell dan tidak cuma buy. Untuk ini teman-teman bisa experimen di rumah masing-masing.

![experimen di rumah](https://media2.giphy.com/media/VIo556t5920j07cCR4/giphy.gif?cid=ecf05e473x4lagugbv5kzjp37qjsba3j48ws8wc7d3837sp9&rid=giphy.gif&ct=g)

*experimen di rumah*

### Lihat hasil simulasi

{% highlight python linenos %}
def get_sim_summary(sim_results, share_amount, initial_capital):
    accumulative_account_value = []
    np_sim_results = np.array(sim_results)
    win_rate = (np_sim_results > 0).sum() / len(sim_results) * 100
    sim_results_df = pd.DataFrame(sim_results, columns=['Change'])  
    sim_fig = go.Figure()
    sim_fig.add_trace(
        go.Scatter(
            x = sim_results_df.index,
            y = sim_results_df['Change']
        )
    )
    accumulative_account_value.append(initial_capital)
    total = initial_capital
    for item in sim_results:
        total = total + item*share_amount
        accumulative_account_value.append(total)
    accumulative_account_value_df = pd.DataFrame(accumulative_account_value, columns=['Acc Value']) 
    accumulative_fig = go.Figure()
    accumulative_fig.add_trace(
        go.Scatter(
            x = accumulative_account_value_df.index,
            y = accumulative_account_value_df['Acc Value']
        )
    )
    return win_rate, sim_results_df, sim_fig, accumulative_fig
{% endhighlight %}

Setelah kita punya daftar profit/loss setiap trade, kita bisa tampilin hasilnya supaya lebih enak dilihat.

Di sini kita akan hitung win rate atau di kasus ini berapa rasio trade yang profit dari keseluruhan (line 4).

Akan cukup berguna kalau kita bisa tampilkan chart profit/loss jadi bisa kita lihat apakah ada masa-masa dimana strategi ini menghasilkan profit yang berturut-turut atau malah loss beruntun (line 7). 

Di line 21 kita juga bikin sebuah line chart untuk menampilkan akumulasi modal yang kita punya. Jadi bisa dilihat dengan gampang volatilitas strategi ini, walaupun masih dengan sederhana (tanpa variable yang lebih kompleks seperti [Sharpe ratio](https://www.investopedia.com/terms/s/sharperatio.asp)).

## Hasil akhir

Setelah semua kodingan selesai dan aplikasi dijalankan dengan `streamlit run`, hasilnya akan seperti ini:

![Hasil akhir aplikasi web dengan python dan streamlit](/blog/images/blog/streamlit_intro/simulate-ema-cross.png)


Selamat bereksperimen!