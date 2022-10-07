---
layout: post
title: Bikin Robot Trading Gratis Dengan Python
author: Made Raditya Pujamurti
tags: [data_science, algo_trading]
cover: cover/robot-trading.jpg
excerpt: Sekarang lagi rame yang namanya robot trading. Sudah sering terjadi kasus dimana robot trading itu adalah penipuan. Video ini tidak hanya sebatas tutorial programming python tapi juga sebuah penjelasan bahwa sebuah robot atau AI itu punya masalah besar yang membuat seolah-olah hasil tradingnya itu bagus. 
---

<iframe src="https://www.youtube.com/embed/lxRGZEGXmFM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> Disclaimer: not financial advice

Artikel ini adalah lanjutan dari [artikel sebelumnya](/blog{% post_url 2021-11-04-teknikal-analisis-indikator-trading-dengan-python %}) tentang teknikal analisis dengan Python dan Streamlit.

## Kenapa bikin project ini

Sekarang lagi rame yang namanya trading pake robot. 

Tapi kebanyakan robot trading itu mahal dan saya gak percaya dengan robot yang dibuat itu mekanisme nya seperti apa.

Jadi kali ini kita bakal coba bikin robot trading sendiri. 

Di sini kita akan pakai semua istilah yang lagi ngetrend sekarang untuk buat robot ini biar keliatan makin canggih.

![fail robot](https://media3.giphy.com/media/EizPK3InQbrNK/giphy.gif?cid=ecf05e4737alnwcd8y2b9gcznnaojlo85125lulwfnbyeovz&rid=giphy.gif&ct=g)

Ada machine learning, artificial intelligence atau AI.Kita bakal beneran pake machine learning, jadi bukan *gimmick*. 

Kita pengen coba apakah bisa kita bikin robot ini punya win rate atau tingkat profit yang tinggi.

**Tapi spoiler dulu**, robot ini kelihatannya bisa ngasi win rate yang tinggi. Cuma sebenarnya ini adalah gejala dari masalah *overfitting* dari si robot. Kalau di tes dengan data baru, performa robot ini akan jauh lebih rendah. 

## Talk is cheap, show me the code!

[robot_trader.py](https://gist.github.com/Rakademi/ca6b0da5781a6bcc0d0e9cd53449276d)

[util.py](https://gist.github.com/Rakademi/f4fa2ef47a38a80eabef52a921fe0d0c)

### Input untuk parameter trading

{% highlight python linenos %}
ticker_symbol = st.sidebar.text_input(
"Please enter the stock symbol", 'MSFT'
)
is_training_mode = st.sidebar.checkbox('Training Mode', value=True)
data_period = st.sidebar.text_input('Period', '10d')
data_interval = st.sidebar.radio('Interval', ['15m','30m','1h','1d'])
rr = st.sidebar.text_input('Risk/Reward', 2)
atr_mult = st.sidebar.text_input('ATR Multiple SL', 3)
share_amount = st.sidebar.text_input('Number of Shares', 1)
initial_capital = st.sidebar.text_input('Initial Capital (USD)', 10000)
{% endhighlight %}

Salah satu yang perlu diperhatikan di sini adalah line 4: checkbox untuk toggle training mode. Tujuan kita supaya bisa tinggal kasi tick di sini untuk latih si robot dengan training data. Kalo gak ada tick, robot akan memprediksi trading sesuai model training. Jadi proses training dan testing model machine learningnya bisa lebih gampang.

Dan untuk bikin aplikasi ini, kita perlu indikator apa aja yang digunakan strategi trading tertentu. Misalnya di kasus ini stop loss (SL) nya berdasarkan nilai [average true range (ATR)](https://www.investopedia.com/terms/a/atr.asp) di line 8.

Kenapa ATR dipilih di kasus ini adalah menurut pengalaman pribadi. Ada banyak cara menentukan SL yang bisa kita coba. 

>Jadi untuk aplikasi data science, pengetahuan di bidang tertentu (domain knowledge) sangat penting.

Input lainnya mirip dengan aplikasi yang kita sudah bahas di artikel sebelumnya.

### Latih si robot

```py
def train_ml_model(X,Y):
    clf = tree.DecisionTreeClassifier()
    clf = clf.fit(X, Y)
    pickle.dump(clf, open("decision_tree_model.p", "wb"))
```

Robot trading kita ini bakal kita latih dengan algoritma Decision Tree. Berkat Scikit-Learn, kita cuma tinggal import library dan panggil metode `DecisionTreeClassifier()` seperti di atas. Kita bisa cari tau lebih lanjut tentang algoritma ini [di dokumentasi ini](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html)

Hal utama yang ingin kita prediksi apakah trading dengan indikator yang kita masukan sebagai independent variable, misalnya nilai Parabolic SAR dan ATR pada saat tertentu bisa memprediksi hasil trading nanti. Tentu kita mau buka posisi saat hasil trading nya profit dan hidari yang loss.

Kita juga akan pakai library [Pickle](https://docs.python.org/3/library/pickle.html) untuk menyimpan model hasil latihan kita. Jadi gak usah latih robotnya berulang kali untuk tes beberapa pair forex atau saham.

### Saatnya uji kemampuan robot

```py
def predict_using_saved_model(ticker_data, features, pickle_filename):
    clf = pickle.load(open(pickle_filename,"rb"))
    prediction_result = clf.predict(features)
    ticker_data['prediction'] = prediction_result
    return ticker_data
```

Setelah selesai latihan, sekarang saatnya kita uji kesaktian si robot dengan metode di atas. 

Tinggal ambil model yang di latih dengan `pickle.load`.

Terus bikin prediksi dengan `predict()`. Di dalam parameternya ada `features`, atau variable independent yang berfungsi untuk jadi penentu dependent variable atau hasil prediksi. Dalam kasus ini dependent variable nya adalah apakah hasil tradingnya akan profit atau loss. 

{% highlight python linenos %}
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
{% endhighlight %}

Function di atas adalah untuk membuat daftar trades sesuai hasil prediksi.

Kita hanya ambil trade yang punya nilai 1 atau diprediksi akan profit (line 3). Di daftar trade ini kita bisa isi stop loss (SL), take profit (TP) dan informasi berguna lain (line 7-9).

## Masalah overfitting

Kalau kita tes performa robot ini dengan berbagai macam data, akan kelihatan sebuah masalah yang besar.

Sewaktu tes dengan data yang sama dengan data untuk training, robot ini punya akurasi yang sangat tinggi (hampir 100%).

Tapi ketika dikasi data di luar training data, akrasinya akan jauh berkurang.

Masalah ini dikenal dengan masalah [overfitting](https://en.wikipedia.org/wiki/Overfitting).

Salah satu penyebabnya adalah independent variable yang punya korelasi yang tinggi satu sama lain.

```py
X = ticker_data[['sar_diff','sar_change_rate']].to_numpy()
Y = ticker_data['status']
```

Seperti yang kita bisa lihat di atas, kalau kita telusuri lebih lanjut, independent variable `sar_diff` dan `sar_change_rate` kemungkinan besar punya korelasi yang tinggi.

Selain itu algoritma decision tree dengan default setting sering kali bisa memperparah masalah overfitting karena struktur dari *tree* tersebut terlalu kompleks dan hanya cocok untuk training data itu saja.

## Kesimpulan

Hal berharga yang bisa kita pelajari di sini adalah jangan mudah percaya dengan hasil back testing orang lain. 

Teruatama dengan sistem yang kompleks dan menggunakan machine learning.

Kita perlu tau algoritma dan proses training sebelum bisa yakin kalau hasil prediksi si robot memang akurat.

Selamat bereksperimen!