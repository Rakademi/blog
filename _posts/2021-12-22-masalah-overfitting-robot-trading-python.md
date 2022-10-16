---
layout: post
title: Masalah Overfitting Robot Trading Python
author: Made Raditya Pujamurti
cover: cover/masalah-overfitting.jpg
tags: [data_science]
excerpt: Robot trading memang terdengar menggiurkan. Tapi apakah performa si robot bisa dijamin bagus di segala kondisi pasar dan aset? Kita bakal cari tau salah satu masalah yang sering terjadi di machine learning yaitu overfitting.
---

<iframe src="https://www.youtube.com/embed/16_Xm8VM1r0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Masalah:
- Performa robot trading cuma bagus di data tertentu.
- Ada klaim robot trading dengan profit konsisten dan win rate yang tinggi. 

## Solusi:
Investigasi apakah model dari machine learning ada kemungkinan menderita [overfitting](https://en.wikipedia.org/wiki/Overfitting).


```python
import yfinance as yf
import talib
import mplfinance as fplt
import pandas as pd
import numpy as np
from sklearn import tree

```

Download data untuk training. Untuk contoh ini kita akan pakai forex pair EURUSD dengan data dari Yahoo Finance. Tapi bisa juga dicoba dengan saham atau pair lain.


```python
ticker_data = yf.download(tickers="EURUSD=X", period="100d", interval="1h")
# ticker_data.index = ticker_data.index.strftime("%d-%m-%Y %H:%M")
ticker_data.set_index(pd.to_datetime(ticker_data.index))
ticker_data.tail()
```

    [*********************100%***********************]  1 of 1 completed



    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_4_1.png">
    


Setelah dapat datanya, kita bisa hitung indikator sesusai kebutuhan. Salah satu contohnya adalah [Parabolic SAR](https://www.investopedia.com/trading/introduction-to-parabolic-sar/#:~:text=The%20parabolic%20SAR%20is%20a,relative%20strength%20index%20(RSI).). Kita bisa dapetin berbagai macam indikator dengan gampang berkat bantuan library [TA-lib](https://mrjbq7.github.io/ta-lib/). 


```python
ticker_data['sar'] = talib.SAR(ticker_data['High'], ticker_data['Low'], acceleration=0.02, maximum=0.2)
ticker_data['atr'] = talib.ATR(ticker_data['High'], ticker_data['Low'], ticker_data['Close'], timeperiod=14)
ticker_data.dropna(inplace=True)
ticker_data.tail()
```


    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_6_0.png">
    


Selanjutnya kita perlu bikin daftar trades. Ada beberapa variable di strategi ini: 
- `atr_mult` : kelipatan [average true range (ATR)](https://www.investopedia.com/terms/a/atr.asp) untuk stop loss (SL).
- `rr` : risk to reward ratio atau rasio berapa take profit (TP) yang akan kita cari dengan SL tertentu.


```python
trades = []
atr_mult = 2
rr = 2

state = 'neutral'
for i in range(len(ticker_data)):
    if ticker_data['Close'][i] > ticker_data['sar'][i] and state != 'buy':
        try:
            buy_price = ticker_data['Open'][i+1]
            sl = ticker_data['Open'][i+1] - ticker_data['atr'][i] * float(atr_mult)
            tp = ticker_data['Open'][i+1] + float(rr) * ticker_data['atr'][i] * float(atr_mult)
            trade = str(ticker_data.index[i+1]) + ',' + str(buy_price) + ',' + str(sl) + ',' + str(tp) + ',long,cyan'
            trade = trade.split(',')
            trades.append(trade)
            state = 'buy' 
        except:
            continue
    elif ticker_data['Close'][i] < ticker_data['sar'][i] and state != 'sell':
        try:
            buy_price = str(ticker_data['Open'][i+1])
            sl = ticker_data['Open'][i+1] + ticker_data['atr'][i] * float(atr_mult)
            tp = ticker_data['Open'][i+1] - float(rr) * ticker_data['atr'][i] *  float(atr_mult)
            trade = str(ticker_data.index[i+1]) + ',' + str(buy_price) + ',' + str(sl) + ',' + str(tp) + ',short,magenta'
            trade = trade.split(',')
            trades.append(trade)
            state = 'sell' 
        except:
            continue
trades[0]
```




    ['2022-05-30 15:00:00+01:00',
     '1.0773539543151855',
     '1.0805615867887224',
     '1.0709386893681117',
     'short',
     'magenta']



Hasil di atas adalah salah satu trade yang kita dapat dengan strategi Parabolic SAR:
- Buy: Parabolic SAR berubah dari atas ke bawah harga close
- Sell: Parabolic SAR berubah dari bawah ke atas harga close

Di setiap trade, ada beberapa variable:
- Waktu open posisi
- Harga open posisi
- SL
- TP
- Jenis trade
- Warna trade untuk di candlestick chart

Sekarang gimana caranya buat simulasi daftar trades yang ada, supaya bisa ketahuan berapa profit atau loss. Salah satunya bisa dengan function sederhana ini: 


```python
def simulate_trades(trades, ticker_data):
    for i in range(len(trades)):
        trade_row = trades[i]
        time = trade_row[0]
        start_pos_price = float(trade_row[1]) 
        stop_loss = float(trade_row[2])
        take_profit = float(trade_row[3])
        trade_type = trade_row[4]
        if 'nan' not in trade_row:
            start_pos_index = ticker_data.index.get_loc(time)
            for j in range(start_pos_index, len(ticker_data)):
                try:
                    close_price = ticker_data.iloc[j]['Close']
                    low_price = ticker_data.iloc[j]['Low']
                    high_price = ticker_data.iloc[j]['High']
                except:
                    break
                if trade_type == 'long':
                    if high_price >= take_profit:
                        trades[i].append(high_price - start_pos_price)
                        trades[i].append(1)
                        break
                    elif low_price <= stop_loss:
                        trades[i].append(low_price - start_pos_price)
                        trades[i].append(0)
                        break
                elif trade_type == 'short':
                    if low_price <= take_profit:
                        trades[i].append(start_pos_price - low_price)
                        trades[i].append(1)
                        break
                    elif high_price >= stop_loss:
                        trades[i].append(start_pos_price - high_price)
                        trades[i].append(0)
                        break
    return trades
```


```python
trades = simulate_trades(trades, ticker_data)
trades[0]
```




    ['2022-05-30 15:00:00+01:00',
     '1.0773539543151855',
     '1.0805615867887224',
     '1.0709386893681117',
     'short',
     'magenta',
     0.007720828056335449,
     1]



Bisa kita lihat salah satu hasil simulasi di atas, ada tambahan data profit/loss dan apakah profit(1) atau loss(0) 

Buat lebih gampang memproses daftar trades, kita bisa ubah list trades jadi dataframe.


```python
def convert_trades_to_df(trades):
    trades_df = pd.DataFrame(trades, columns=['time','start_pos_price','sl','tp','trade_type','trade_color','p/l','status'])
    trades_df.set_index(pd.to_datetime(trades_df['time']), inplace=True)
    trades_df.drop('time', axis=1, inplace=True)
    trades_df['start_pos_price'] = trades_df['start_pos_price'].astype(float).round(4)
    return trades_df
```


```python

trades_df = convert_trades_to_df(trades)
trades_df.tail()
```


    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_14_0.png">
    


Bikin function untuk rangkuman hasil simulasinya: misalnya berapa win rate yang dihasilkan dan chart perubahan modal setelah trading.


```python
def get_sim_summary(trade_pl, share_amount, initial_capital):
    np_sim_results = np.array(trade_pl)
    np_trade_pl = np_sim_results * share_amount
    win_rate = (np_trade_pl > 0).sum() / len(trade_pl) * 100
    win_rate = win_rate.round(1)
    np_accumulative_capital = np_trade_pl.cumsum() + initial_capital
    accumulative_capital_df = pd.DataFrame(np_accumulative_capital, columns=['Total Capital'])
    return win_rate, accumulative_capital_df
```

Pakai function di atas buat nampilin hasil tradingnya.


```python
win_rate, accumulative_capital_df = get_sim_summary(trades_df['p/l'].tolist(), share_amount=10000, initial_capital=2000)
```


```python
print(f'Win rate = {win_rate} %')
```

    Win rate = 31.1 %


Win rate sekitar 30% emang keliatan agak kecil. Tapi di sini kita pakai risk/reward rasio 2 jadi masih ada kemungkinan bisa paling gak break even. 


```python
accumulative_capital_df.plot()
```




    <AxesSubplot:>




    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_21_1.png">
    


Seperti yang kita lihat di atas, ternyata modal kita bakal jadi minus kalo pake strategi ini. Ada trades yang profit tapi kebanyakan rugi.

> Misi si robot adalah ngasi kita sinyal yang diprediksi bakal profit.

Function di bawah bakal gabungin daftar trades yang ada ke ticker data untuk jadi training data machine learning. 


```python
def join_trades_to_ticker_data(trades_df, ticker_data):
    trades_df['start_pos_price'] = trades_df['start_pos_price'].astype(float).round(4)
    ticker_data = pd.concat([ticker_data, trades_df], axis=1, join='outer')
    return ticker_data
```


```python

ticker_data = join_trades_to_ticker_data(trades_df, ticker_data)
ticker_data.head()
```


    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_24_0.png">
    


Di ticker_data yang udah digabungin bakal ada beberapa value `n/a` di kolom `status`. Tapi buat training algoritma si robot, penting untuk kolom ini punya nilai yang bukan n/a. Value ini bisa diganti dengan 0 karena kita gak trading di waktu tersebut.


```python
ticker_data.fillna({'status':0}, inplace=True)
ticker_data.tail()

```


    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_26_0.png">
    


Buat lebih pastinya, kita bisa liat tampilan candlestick chart setelah ditambah titik dimana ada trade.

Kita bakal ambil 100 baris pertama supaya chartnya gak kepenuhan.


```python
plot_data = ticker_data[:100]
sar = fplt.make_addplot(plot_data[["sar"]], type='scatter', color='black')
trades = fplt.make_addplot(plot_data[["start_pos_price"]], type='scatter', color=['cyan' if val == 'cyan' else 'magenta' for val in plot_data["trade_color"]], marker='x')

fplt.plot(
            plot_data,
            type='candle',
            addplot = [sar, trades],
            style='charles',
            title='EURUSD',
            ylabel='Harga',
            )
```


    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_28_0.png">
    


Mantap, strategi trading kita udah sesuai harapan! Setiap Parabolic SAR berubah dari di atas jadi di bawah candlestick, kita ambil posisi buy di candle berikutnya (warna cyan). Begitu juga sebaliknya untuk sell (warna magenta).

Untuk ngelatih si robot kita perlu beberapa features/independent variables untuk prediksi apakah di kondisi tertentu trading yang diambil bakal profit. 

Di sini kita bisa pakai variables yang udah ada, tapi bisa juga bikin variable yang baru. Contohnya kita bisa bikin `sar_diff` (selisih indikator SAR di 2 interval). `sar_diff` bisa diolah lagi jadi `sar_change_rate` (seberapa banyak SAR berubah relatif ke nilai SAR di periode sebelumnya).


```python
ticker_data['sar_diff'] = ticker_data['sar'].diff()
ticker_data['sar_change_rate'] = ticker_data['sar_diff'] / ticker_data['sar'].shift(periods=1)
ticker_data.dropna(subset=['sar_diff','sar_change_rate'], inplace=True)
```

Sekarang tinggal pilih kolom untuk independent variables `X` dan dependent variable atau di kasus ini label yang dipakai untuk melatih si robot  `y`.




```python
X = ticker_data[['sar_diff','sar_change_rate']].to_numpy()
y = ticker_data['status']
```

Ada banyak algoritma yang bisa kita pilih dan untuk skenario ini kita akan pakai [Decision Tree](https://scikit-learn.org/stable/modules/tree.html).

Decision Tree punya tendensi untuk mengalami overfitting jadi cocok untuk ilustrasi masalah overfitting yang kemungkinan dialami beberapa robot trading.

Ada banyak parameter yang bisa kita ganti di metode `DecisionTreeClassifier()` tapi buat kasus ini gak perlu kita ubah supaya makin keliatan efek overfitting nya.


```python
clf = tree.DecisionTreeClassifier()
clf = clf.fit(X, y)
```

Setelah dilatih dengan metode `fit()`, model atau otak si robot akan tersimpan di variable `clf`.

Model ini bisa dipakai untuk memprediksi apakah kondisi tertentu akan menghasilkan profit atau loss. Sekarang kita tes dulu performa prediksi robot trading dengan data yang sama dengan training data yang dipakai tadi. Jadi tinggal kasi `X` di parameter `predict()` untuk dapetin prediksinya.


```python
prediction_result = clf.predict(X)
ticker_data['prediction'] = prediction_result
ticker_data.tail()
```


    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_36_0.png">
    


Sekarang kita udah punya ticker data dengan hasil prediksi si robot. Seperti sebelumnya, kita perlu bikin daftar trades untuk jadi data simulasi.


```python
def create_trade_list_from_prediction(ticker_data, rr, atr_mult):
    trades = []
    recommended_trades_df = ticker_data.loc[ticker_data['prediction'] == 1]
    for i in range(len(recommended_trades_df)):
        buy_price = recommended_trades_df['Open'][i]
        if buy_price > recommended_trades_df['sar'][i]:
            sl = buy_price - recommended_trades_df['atr'][i] * float(atr_mult)
            tp = buy_price + recommended_trades_df['atr'][i] * float(atr_mult) * float(rr)
            trade = str(recommended_trades_df.index[i]) + ',' + str(buy_price) + ',' + str(sl) + ',' + str(tp) + ',long,cyan'
        else:
            sl = buy_price + recommended_trades_df['atr'][i] * float(atr_mult)
            tp = buy_price - recommended_trades_df['atr'][i] * float(atr_mult) * float(rr)
            trade = str(recommended_trades_df.index[i]) + ',' + str(buy_price) + ',' + str(sl) + ',' + str(tp) + ',short,magenta'
        trade = trade.split(',')
        trades.append(trade)
    return trades
```


```python
trades_from_prediction = create_trade_list_from_prediction(ticker_data, rr=2, atr_mult=2)
trades_from_prediction[0]
```




    ['2022-05-30 14:00:00+00:00',
     '1.0773539543151855',
     '1.0741597377524084',
     '1.08374238744074',
     'long',
     'cyan']



Salah satu trade hasil prediksi bisa dilihat di atas.

Lanjut, kita bakal simulasi daftar trades ini buat lihat profit dan loss.


```python
trades_from_prediction = simulate_trades(trades_from_prediction, ticker_data)
trades_from_prediction_df = convert_trades_to_df(trades_from_prediction)
win_rate, accumulative_capital_df = get_sim_summary(trades_from_prediction_df['p/l'].tolist(), share_amount=10000, initial_capital=2000)
```


```python
win_rate
```




    96.0



### Wow win rate hampir 100%!

Sekarang kita lihat perubahan modal, pasti grafiknya hampir selalu nanjak.


```python
accumulative_capital_df.plot()
```




    <AxesSubplot:>




    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_44_1.png">
    


Sangat bagus kan! Hasil trading ini bisa dipake untuk marketing material robot trading atau bisa membuat orang ngerasa udah yakin menemukan 'holy grail' dalam trading. Padahal ada masalah serius dari hasil trading ini.

Buat tes lebih mendalam lagi, kita perlu data lain yang belum pernah dipakai untuk training si robot. Caranya bisa pakai data forex pair lain atau pakai data di periode lain. Di bawah ini kita akan pakai data 700 hari, jadi beda dari yang sebelumnya data 100 hari untuk training data.


```python
test_data = yf.download(tickers="EURUSD=X", period="700d", interval="1h")
test_data.index = test_data.index.strftime("%d-%m-%Y %H:%M")
test_data['sar'] = talib.SAR(test_data['High'], test_data['Low'], acceleration=0.02, maximum=0.2)
test_data['atr'] = talib.ATR(test_data['High'], test_data['Low'], test_data['Close'], timeperiod=14)
test_data.dropna(inplace=True)
test_data['sar_diff'] = test_data['sar'].diff()
test_data['sar_change_rate'] = test_data['sar_diff'] / test_data['sar'].shift(periods=1)
test_data.dropna(subset=['sar_diff','sar_change_rate'], inplace=True)
```

    [*********************100%***********************]  1 of 1 completed


Penting untuk kita pakai model machine learning yang sama. Jadi gak bikin model baru dan pakai variable `clf` yang sama dengan sebelumnya.

Tinggal pakai metode `clf.predict()` lagi buat bikin prediksi dengan test data yang baru.


```python
X = test_data[['sar_diff','sar_change_rate']].to_numpy()
prediction_result = clf.predict(X)
test_data['prediction'] = prediction_result
test_data.tail()
```


    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_48_0.png">
    


Bisa kita lihat di atas hasil prediksi dengan data yang baru.

Sekarang saatnya pembuktian apakah si robot bisa ngasi performa yang bagus seperti sebelumnya.


```python
trades_from_prediction = create_trade_list_from_prediction(test_data, rr=2, atr_mult=2)
trades_from_prediction = simulate_trades(trades_from_prediction, test_data)
trades_from_prediction_df = convert_trades_to_df(trades_from_prediction)
win_rate, accumulative_capital_df  = get_sim_summary(trades_from_prediction_df['p/l'], share_amount=10000, initial_capital=2000)
```

Mari kita lihat satu per satu...


```python
win_rate
```




    39.7



Win rate cuma jadi sekitar 40%. Gimana nasib modal kita kalau pake robot ini?


```python
accumulative_capital_df.plot()
```




    <AxesSubplot:>




    
<img src="/blog/images/blog/masalah-overfitting-robot-trading-python_files/output_54_1.png">
    


Perfoma si robot jauh dari konsisten dan sebagus sebelumnya. Modal kita sempat minus sebelum naik lagi, yang artinya kita udah bangkrut duluan.

Kalo diperhatikan, di bagian akhir chart ada garis lurus itu adalah data yang sama dengan training data, jadi model machine learningnya bisa ngasi prediksi yang sangat tepat. Jadi bagian ini bisa diabaikan karena gak akurat. Tapi di bagian lain, prediksinya lumayan ngaco.

Inilah yang disebut dengan masalah overfitting di machine learning. Teman-teman bisa cari tau cara buat menghindari masalah ini, salah satunya dengan baca dokumentasi algoritma Decision Tree yang kita pakai tadi.

>Di sini kita juga bisa liat, ngoding bisa berguna buat menghindari kerugian atau bahkan margin call (MC) 💸 

Selamat bereksperimen!
