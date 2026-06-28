---
title: "2-③[AI][Kaggle][python]Kaggle入門(Pandasライブラリの使い方 3.要約統計量関数)"
emoji: "🎉"
type: "tech"
topics:
  - "ai"
  - "python"
  - "pandas"
  - "kaggle"
published: true
published_at: "2025-12-27 13:22"
---

[Kaggle入門2(Pandasライブラリの使い方 1.生成/読込/書込)](https://zenn.dev/rg687076/articles/0467242dc7b343)
[Kaggle入門2(Pandasライブラリの使い方 2.インデックス作成、選択、割り当て)](https://zenn.dev/rg687076/articles/6daae3e6f2209a)
[Kaggle入門2(Pandasライブラリの使い方 3.要約統計量関数とマップ)](https://zenn.dev/rg687076/articles/dc7ccb8505be7b)
[Kaggle入門2(Pandasライブラリの使い方 4.グループ化と並べ替え)](https://zenn.dev/rg687076/articles/ee3ff9651aa073)
[Kaggle入門2(Pandasライブラリの使い方 5.データ型と欠損値)](https://zenn.dev/rg687076/articles/a86984e9ad26c5)
[Kaggle入門2(Pandasライブラリの使い方 6.名前の変更と結合) 最終回](https://zenn.dev/rg687076/articles/1be44cb619bfc2)


← [Kaggle入門1 機械学習Intro](https://zenn.dev/rg687076/articles/0386269e85da8f)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle入門3 地理空間データ分析](https://zenn.dev/rg687076/articles/4028c3853f6ebf) →

# Abstract
Kaggle「Pandasの[要約統計関数とマップ](https://www.kaggle.com/code/residentmario/summary-functions-and-maps)」の翻訳と実行方法の解説
データを抽出して洞察します。

# 要約統計関数とマップ

## 理論編

### 1. はじめに
前回のチュートリアルでは、DataFrame または Series から関連データを選択する方法を学びました。演習で示したように、データ表現から適切なデータを選択することは、作業を効率化するために非常に重要です。

ですが、メモリから読み出したデータが常に最初から理想通りの形式になっているとは限りません。自分のタスクに合わせて、自分たちでデータを整形（リフォーマット）する手間が必要になることもあります。このチュートリアルでは、入力を「ちょうどいい」形にするために、データに適用できるさまざまな操作について学んでいきます。

デモンストレーションには、Wine Magazine（ワイン・マガジン）のデータを使用します。

#### 準備
```python
import pandas as pd
pd.set_option('display.max_rows', 5)
import numpy as np
reviews = pd.read_csv("../input/wine-reviews/winemag-data-130k-v2.csv", index_col=0)
```
##### 確認
```python
reviews
```

||country|description|designation|points|price|province|region_1|region_2|taster_name|taster_twitter_handle|title|variety|winery|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|Italy|Aromas include tropical fruit, broom, brimston...|Vulkà Bianco|87|NaN|Sicily & Sardinia|Etna|NaN|Kerin O’Keefe|@kerinokeefe|Nicosia 2013 Vulkà Bianco (Etna)|White Blend|Nicosia|
|1|Portugal|This is ripe and fruity, a wine that is smooth...|Avidagos|87|15.0|Douro|NaN|NaN|Roger Voss|@vossroger|Quinta dos Avidagos 2011 Avidagos Red (Douro)|Portuguese Red|Quinta dos Avidagos|
|...|...|...|...|...|...|...|...|...|...|...|...|...|...|
|129969|France|A dry style of Pinot Gris, this is crisp with ...|NaN|90|32.0|Alsace|Alsace|NaN|Roger Voss|@vossroger|Domaine Marcel Deiss 2012 Pinot Gris (Alsace)|Pinot Gris|Domaine Marcel Deiss|
|129970|France|Big, rich and off-dry, this is powered by inte...|Lieu-dit Harth Cuvée Caroline|90|21.0|Alsace|Alsace|NaN|Roger Voss|@vossroger|Domaine Schoffit 2012 Lieu-dit Harth Cuvée Car...|Gewürztraminer|Domaine Schoffit|

129971 rows × 13 columns

### 2. サマリー関数
Pandas は、データを何らかの有用な方法で再構成する、多くのシンプルな「サマリー関数」（正式名称ではありません）を提供しています。例えば、describe() メソッドをみてみましょう。
**実行**
```python
reviews.points.describe()
```
**結果**
```
count    129971.000000
mean         88.447138
             ...      
75%          91.000000
max         100.000000
Name: points, Length: 8, dtype: float64
```
このメソッドは、指定した列の属性に関するハイレベルな（概要的な）サマリーを生成します。このメソッドは「データ型を認識（type-aware）」するため、入力データの型に応じて出力内容が変化します。

上の出力結果は数値データの場合にのみ意味をなすものです。
文字列データの場合は下記のとおりです。
**実行**
```python
reviews.taster_name.describe()
```
**結果**
```
count         103727
unique            19
top       Roger Voss
freq           25514
Name: taster_name, dtype: object
```

他にも、DataFrame または Series 内の列に関する特定の簡単な概要統計を取得したい場合は、通常、それを実現する便利な pandas 関数があります。
たとえば、割り当てられたポイントの平均（平均評価されたワインの評価など）を確認するには、mean() 関数を使用します。
**実行**
```
reviews.points.mean()
```
**結果**
```
88.44713820775404
```

重複を除く値のリストを表示するには、unique() 関数を使用します。
**実行**
```
reviews.taster_name.unique()
```
**結果**
```
array(['Kerin O’Keefe', 'Roger Voss', 'Paul Gregutt',
       'Alexander Peartree', 'Michael Schachner', 'Anna Lee C. Iijima',
       'Virginie Boone', 'Matt Kettmann', nan, 'Sean P. Sullivan',
       'Jim Gordon', 'Joe Czerwinski', 'Anne Krebiehl\xa0MW',
       'Lauren Buzzeo', 'Mike DeSimone', 'Jeff Jenssen',
       'Susan Kostrzewa', 'Carrie Dykes', 'Fiona Adams',
       'Christina Pickard'], dtype=object)
```
出現頻度を確認したい場合は、value_counts() メソッドを使用します。
**実行**
```
reviews.taster_name.value_counts()
```
**結果**
```
Roger Voss           25514
Michael Schachner    15134
                     ...  
Fiona Adams             27
Christina Pickard        6
Name: taster_name, Length: 19, dtype: int64
```

## マップ
「マップ」とは数学用語から派生した言葉で、ある値の集合を受け取り、それを別の値の集合へと「写像（対応付け）」する関数のことを指します。

データサイエンスにおいては、既存のデータから新しい表現を作成したり、現在のデータ形式を後で必要になる形式へと変換したりしなければならない場面が頻繁にあります。マップこそがこうした処理を担うものであり、作業を完遂させる上で極めて重要な役割を果たします。

よく使われるマッピング手法は2つあります。

1つ目は、よりシンプルな map() です。例えば、ワインのスコアから平均値を引き、平均が 0 になるように調整（remean）したいとかの局面です。
これは次のように記述します。
**実行**
```python
review_points_mean = reviews.points.mean()
reviews.points.map(lambda p: p - review_points_mean)
```
**結果**
```
0        -1.447138
1        -1.447138
            ...   
129969    1.552862
129970    1.552862
Name: points, Length: 129971, dtype: float64
```
map() に渡す関数は、シリーズ（Series）から単一の値（上の例で言えば、1個1個のスコア）を受け取り、それを変換した値を返すものである必要があります。map() を実行すると、すべての値がその関数によって変換された、新しいシリーズが返されます。

一方、各行に対してカスタムメソッドを呼び出し、データフレーム（DataFrame）全体を変換したい場合には、それに対応するメソッドとして apply() を使用します。
**実行**
```python
def remean_points(row):
    row.points = row.points - review_points_mean
    return row

reviews.apply(remean_points, axis='columns')
```
**結果**
||country|description|designation|points|price|province|region_1|region_2|taster_name|taster_twitter_handle|title|variety|winery|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|Italy|Aromas include tropical fruit, broom, brimston...|Vulkà Bianco|-1.447138|NaN|Sicily & Sardinia|Etna|NaN|Kerin O’Keefe|@kerinokeefe|Nicosia 2013 Vulkà Bianco (Etna)|White Blend|Nicosia|
|1|Portugal|This is ripe and fruity, a wine that is smooth...|Avidagos|-1.447138|15.0|Douro|NaN|NaN|Roger Voss|@vossroger|Quinta dos Avidagos 2011 Avidagos Red (Douro)|Portuguese Red|Quinta dos Avidagos|
|...|...|...|...|...|...|...|...|...|...|...|...|...|...|
|129969|France|A dry style of Pinot Gris, this is crisp with ...|NaN|1.552862|32.0|Alsace|Alsace|NaN|Roger Voss|@vossroger|Domaine Marcel Deiss 2012 Pinot Gris (Alsace)|Pinot Gris|Domaine Marcel Deiss|
|129970|France|Big, rich and off-dry, this is powered by inte...|Lieu-dit Harth Cuvée Caroline|1.552862|21.0|Alsace|Alsace|NaN|Roger Voss|@vossroger|Domaine Schoffit 2012 Lieu-dit Harth Cuvée Car...|Gewürztraminer|Domaine Schoffit|

129971 rows × 13 columns

axis='index' で reviews.apply() を呼び出した場合は、各行を変換する関数を渡す代わりに、各列を変換する関数を渡す必要があります。
注)
map() と apply() はそれぞれ、変換された新しい Series と DataFrame を返すことに注意してください。これらの関数は、呼び出し元のデータを変更しません。レビューの最初の行を見ると、ポイント値が元の値のままであることがわかります。

**実行**
```python
reviews.head(1)
```
**結果**
|country|description|designation|points|price|province|region_1|region_2|taster_name|taster_twitter_handle|title|variety|winery|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|Italy|Aromas include tropical fruit, broom, brimston...|Vulkà Bianco|87|NaN|Sicily & Sardinia|Etna|NaN|Kerin O’Keefe|@kerinokeefe|Nicosia 2013 Vulkà Bianco (Etna)|White Blend|Nicosia|

Pandasは、多くの一般的なマッピング操作を組み込み関数として提供しています。例えば、points列の意味をより速く再定義する方法は次のとおりです。

**実行**
```python
review_points_mean = reviews.points.mean()
reviews.points - review_points_mean
```
**結果**
```
0        -1.447138
1        -1.447138
            ...   
129969    1.552862
129970    1.552862
Name: points, Length: 129971, dtype: float64
```

このコードでは、左側の多数の値 (Series 内のすべて) と右側の単一の値 (平均値) の間で演算を実行しています。Pandas はこの式を見て、データセット内のすべての値からその平均値を減算する必要があることを理解します。

Pandasは、同じ長さのSeries間でこれらの操作を実行した場合の処理​​も理解します。例えば、データセット内の国と地域の情報を簡単に結合するには、次のようにします。
**実行**
```python
reviews.country + " - " + reviews.region_1
```
**結果**
```
0            Italy - Etna
1                     NaN
               ...       
129969    France - Alsace
129970    France - Alsace
Length: 129971, dtype: object
```
これらの演算子は、pandasに組み込まれた高速化機能を使用しているため、map()やapply()よりも高速です。Pythonの標準演算子（>、<、==など）はすべて同じように動作します。

ただし、加算と減算だけでは実行できない条件付きロジックの適用など、より高度な処理を実行できる map() や apply() ほど柔軟ではありません。

## 実践編
### はじめに
これで、データをより深く理解する準備が整いました。 次のセルを実行して、演習の準備をしてください。データといくつかのユーティリティ関数（回答を確認するコードを含む）を読み込みます。
Setup complete.が表示されればOKです。
※赤く警告が表示されてますが、"Setup complete."が表示されいるのでOKとします。
![](https://storage.googleapis.com/zenn-user-upload/a5136e8cb361-20251227.png)

### 演習
#### 1. レビューデータフレームのポイント列の中央値は何ですか?
![](https://storage.googleapis.com/zenn-user-upload/2c9766882526-20251227.png)
下記はヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/53d0d19e6a90-20251227.png)

#### 2. データセットにはどの国が含まれていますか? (回答に重複不可)
![](https://storage.googleapis.com/zenn-user-upload/339e90435067-20251227.png)

下記、ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/c50c27d275b3-20251227.png)

#### 3. 各国はデータセットにどのくらいの頻度で登場しますか? 国とその国のワインのレビュー数をマッピングするシリーズ reviews_per_country を作成します。
![](https://storage.googleapis.com/zenn-user-upload/e3110b5bdb61-20251227.png)
ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/835fe8d939c2-20251227.png)

#### 4. 平均価格を差し引いた価格列のバージョンを含む変数 centered_price を作成します。(注: この「センタリング」変換は、さまざまな機械学習アルゴリズムを適用する前の一般的な前処理手順です。)
![](https://storage.googleapis.com/zenn-user-upload/9ec7b6e3fd16-20251227.png)
ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/5403cfa44302-20251227.png)

#### 5. 私は経済的なワイン購入者です。どのワインが「一番お買い得」でしょうか？データセット内でポイント対価格比が最も高いワインのタイトルを格納した変数barget_wineを作成してください。
![](https://storage.googleapis.com/zenn-user-upload/672bc222f332-20251227.png)
ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/b8275473f90f-20251227.png)

#### 6. ワインのボトルを表現するのに使える言葉の数は、限られています。あるワインは「tropical（トロピカル）」と表現されることが多いでしょうか、それとも「fruity（フルーティー）」でしょうか？  データセットの description 列に、これら2つの単語がそれぞれ何回出現するかをカウントしたシリーズ descriptor_counts を作成してください。（簡略化のため、単語が文字（大文字）で始まっているケースは無視して構いません。）
![](https://storage.googleapis.com/zenn-user-upload/d036d110e346-20251227.png)
※reviews.description.map(lambda desc: "tropical" in desc)の部分は'tropical'が含まれるかどうかのリスト配列を返却している。
ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/382879fda281-20251227.png)
#### 7. 星評価の表示
私たちは自分のウェブサイトでこれらのワインレビューを公開したいと考えていますが、80点から100点という評価システムは少し分かりにくいので、シンプルな「星評価」に変換したいと考えています。
- 95点以上：星3つ
- 85点以上 95点未満：星2つ
- それ以外：星1つ

さらに、カナダ醸造家協会（Canadian Vintners Association）がサイトの広告を大量に掲載してくれたため、カナダ産のワインについては、点数に関わらず自動的に星3つとします。

データセット内の各レビューに対応する星の数を格納したシリーズ star_ratings を作成してください。
![](https://storage.googleapis.com/zenn-user-upload/1a224ae06a28-20251227.png)
ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/9175ffe4442b-20251227.png)

次の章[(4. グループ化と並べ替え)](https://zenn.dev/rg687076/articles/ee3ff9651aa073)へ