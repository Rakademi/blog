---
layout: post
title: Implementasi Feature Scaling Dengan Python
author: Made Raditya Pujamurti
cover: cover/feature-scaling.png
tags: [data_science]
excerpt: Ada banyak skenario dimana kita perlu merubah independent variables dengan scaling untuk algoritma tertentu. Di artikel ini kita akan bahas implementasi teknik ini dengan algoritma KNeighborsClassifier.
---

## Masalah

Seperti yang udah kita bahas di artikel sebelumnya tentang PCA, data yang ada gak keliatan jelas pengelompokannya. Di bawah ini adalah rangkuman kodingan yang ada di artikel sebelumnya:


```python
import numpy as np
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler
from sklearn import datasets
from sklearn.neighbors import KNeighborsClassifier
import matplotlib.pyplot as plt
```


```python
wine_data = datasets.load_wine()
X = wine_data.data
y = wine_data.target
pca_2 = PCA(n_components=2)
X_pca_2 = pca_2.fit_transform(X)
plt.scatter(X_pca_2[:, 0], X_pca_2[:, 1], c=y)
plt.show()
```


    
<img src="/blog/images/blog/implementasi-feature-scaling-di-python_files/output_2_0.png">
    


Seperti yang bisa kita lihat di atas, masalah muncul pas kita mau bikin clustering atau klasifikasi kelompok-kelompok yang ada.

Buat ilustrasi masalah ini kita bisa tes algoritma classification sederhana misalnya [KNeighborsClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsClassifier.html).

Pertama kita pisahin dulu data untuk training dan test. Biasanya rasio yang dipake adalah 0.7.


```python
X_train, X_test, y_train, y_test = train_test_split(X_pca_2, y, train_size=0.7)
```

Tinggal pakai `KNeighborsClassifier()` yang udah kita import di atas untuk latih model machine learningnya. Setelah itu kita bisa ukur performa model ini. Salah satunya dengan metric [mean_squared_error](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)


```python
classifier = KNeighborsClassifier()
classifier.fit(X_train, y_train)
prediction = classifier.predict(X_test)
mean_squared_error(y_test, prediction)
```




    0.5185185185185185



Kita dapat hasil sekitar 0.52. Nanti kita akan liat apakah dengan solusi di bawah nilai error ini bisa lebih kecil (performa model artinya lebih bagus).

## Solusi

Salah satu solusi yang bisa kita coba adalah scaling dengan `StandardScaler()` seperti di bawah:


```python
X_with_scaling = StandardScaler().fit_transform(X)
X_pca_with_scaling = pca_2.fit_transform(X_with_scaling)
```

Teknik scaling ini sederhananya akan merubah independent variable buat punya kontribusi yang sebanding dengan satu sama lain. Ini penting terutama untuk beberapa algoritma yang pake pengukuran jarak antara 2 poin yang ada di independent variable.

Mirip seperti sebelumnya, kita bakal tes performa model algoritma yang sama dengan transformasi fitur ini.

Tapi sebelum itu kita bisa liat dulu apakah pengelompokan data yang ada jadi lebih baik.


```python
X_train2, X_test2, y_train, y_test = train_test_split(X_pca_with_scaling, y, train_size=0.7)
```


```python
plt.scatter(X_train2[:, 0], X_train2[:, 1], c=y_train)
plt.show()
```


    
<img src="/blog/images/blog/implementasi-feature-scaling-di-python_files/output_11_0.png">
    


Mantap! Grup data yang sejenis udah makin ngumpul dan yang beda warna makin jelas terpisah.

Sekarang kita buktikan dengan angka, seberapa kecil mean squared error dari hasil transformasi ini.


```python
classifier_with_scalling = KNeighborsClassifier()
classifier_with_scalling.fit(X_train2, y_train)
prediction = classifier_with_scalling.predict(X_test2)
mean_squared_error(y_test, prediction)
```




    0.018518518518518517



Nice, angka ini jauh lebih kecil dari nilai error sebelumnya (0.52) 👍

Jadi bisa diliat seberapa pentingnya untuk bereksperimen dengan teknik scaling ini, terutama untuk beberapa algoritman tertentu.

Semoga bermanfaat!
