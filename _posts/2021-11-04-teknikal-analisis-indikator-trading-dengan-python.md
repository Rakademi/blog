---
layout: post
title: Teknikal Analisis Dengan Python
author: Made Raditya Pujamurti
tags: [data_science, algo_trading]
cover: cover/ta-lib-intro.jpg
excerpt: Python punya banyak library yang sangat berguna termasuk untuk analisa teknikal atau technical analysis. Salah satu library yang banyak dipakai adalah TA-lib.

---

<iframe src="https://www.youtube.com/embed/S224au81jRw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> Disclaimer: not financial advice

Sebelumnya kita udah coba hitung EMA secara manual. Tapi bakal agak repot kalau kita bikin indikator lain satu per satu. 

Tujuan kita kali ini adalah nyari beberapa indikator misalnya EMA, Parabolic SAR dan RSI yang bisa berguna untuk ngasi sinya kapan sebaiknya trading. Kita bakal gabungin semua indikator ini dalam satu candlestick chart seperti di bawah.

![hasil akhir ta-lib](/blog/images/blog/ta-lib_intro/1.jpg)

Di Python ada banyak library yang sangat berguna, termasuk untuk teknikal analisis. Salah satu library ini adalah TA-lib. 

Instruksi untuk cara install nya bisa diikuti di [dokumentasi ini](https://github.com/mrjbq7/ta-lib).

Artikel ini adalah lanjutan dari [artikel sebelumnya](/blog{% post_url 2021-11-01-simulasi-strategi-ema-cross-python %}) tentang teknikal analisis dengan Python dan Streamlit.

## Membedah kodingan

[ta_intro.py](https://gist.github.com/Rakademi/d9a49dcce47f4a3a151937d544664145)

[util.py](https://gist.github.com/Rakademi/29bc91cf08e5d0f33126c9f29204ad11)

Tanpa basa basi kita langsung ke bagian penting kodingan di project ini.

### Import talib

```py
import streamlit as st
import yfinance as yf
import plotly.graph_objs as go
from plotly.subplots import make_subplots
import pandas as pd
import numpy as np
import talib
import util
```

Setelah instalasi TA-lib selesai, kita bisa pakai library ini hanya dengan `import talib` seperti di atas.

Library lainnya sudah kita bahas di artikel-artikel sebelumnya.

```py
ticker_data['fast_ema'] = talib.EMA(ticker_data['Close'], int(ema1))
ticker_data['slow_ema'] = talib.EMA(ticker_data['Close'], int(ema2))
ticker_data['rsi'] = talib.RSI(ticker_data['Close'], timeperiod=14)
ticker_data['sar'] = talib.SAR(ticker_data['High'], ticker_data['Low'], acceleration=0.02, maximum=0.2)
```

Kode di atas adalah contoh untuk dapetin EMA, RSI dan Parabolic SAR.

Sangat mudah dan elegan 😎

Tapi ada bagusnya kita tau cara ngitung indikator secara manual, kalau kita ingin perhitungannya lebih pasti dan gak bergantung library buatan orang lain. Dan ada bagusnya juga hitung manual jadi kita lebih ngerti mekanisme di balik indikator itu.

Untuk sekarang kita bakal tetep pakai TA-lib supaya lebih singkat.

### Tampilkan di candlestick

{% highlight python linenos %}
def add_row_trace(candle_fig, x_value, y_value, trace_name, color, row_num, mode = 'lines'):
    candle_fig.add_trace(
        go.Scatter(
            x = x_value,
            y = y_value,
            name = trace_name,
            line = dict(color=color),
            mode = mode
        ),
        row = row_num,
        col = 1
    )
    return candle_fig

fig = make_subplots(rows=2, cols=1, shared_xaxes=True)
fig = util.get_candle_chart(fig, ticker_data)
fig = util.add_row_trace(fig, ticker_data.index, ticker_data['fast_ema'], 'fast EMA', 'yellow', 1)
fig = util.add_row_trace(fig, ticker_data.index, ticker_data['slow_ema'], 'slow EMA', 'blue', 1)
fig = util.add_row_trace(fig, ticker_data.index, ticker_data['sar'], 'Parabolic SAR', 'black', 1, mode='markers')
fig = util.add_row_trace(fig, ticker_data.index, ticker_data['rsi'], 'RSI', 'red', 2)
{% endhighlight %}

Di candlestick chart akan ada 2 row atau baris: baris atas untuk candlestick, EMA dan Parabolic SAR. RSI dipisah ke baris kedua karna akan mengganggu tampilan dari candlesstick chart jika dijadikan 1 row.

Karena kita ingin 2 row, untuk figure nya kita pakai metode `make_subplots` di line 15.

Terus kita tinggal bikin custom function `add_row_trace()` di line 1 untuk menambahkan berbagai indikator yang ada. Jadi 1 function ini bisa untuk lebih dari 1 indikator untuk mengurangi duplikasi kode.

Setelah di run dengan `streamlit run`, hasil akhirnya akan seperti ini:

![hasil akhir ta-lib](/blog/images/blog/ta-lib_intro/1.jpg)

Selamat bereksperimen!