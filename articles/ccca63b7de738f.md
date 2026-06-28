---
title: "9-⑤[AI][Kaggle][python]Kaggle入門(ディープラーニング入門 5.ドロップアウトとバッチ正規化)"
emoji: "🔖"
type: "idea"
topics:
  - "ai"
  - "python"
  - "kaggle"
  - "ディープラーニング入門"
published: true
published_at: "2026-02-22 20:16"
---

[Kaggle入門9(ディープラーニング入門 1.単一のニューロン)](https://zenn.dev/rg687076/articles/0599ad6be868ef)
[Kaggle入門9(ディープラーニング入門 2.ディープニューラルネットワーク)](https://zenn.dev/rg687076/articles/6b2f696bb125a3)
[Kaggle入門9(ディープラーニング入門 3.確率的勾配降下法)](https://zenn.dev/rg687076/articles/364a11feb27c9e)
[Kaggle入門9(ディープラーニング入門 4.過剰適合と不足適合)](https://zenn.dev/rg687076/articles/e899b174b75bb5)
[Kaggle入門9(ディープラーニング入門 5.ドロップアウトとバッチ正規化)](https://zenn.dev/rg687076/articles/ccca63b7de738f)
[Kaggle入門9(ディープラーニング入門 6.最終回 バイナリ分類)](https://zenn.dev/rg687076/articles/24deba89b38315)

← [Kaggle入門8(高度なSQL)](https://zenn.dev/rg687076/articles/9034c3f8cadc88)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle入門10(畳み込み分類器)](https://zenn.dev/rg687076/articles/175f02ebe77996) →

## Abstract
Kaggle「ディープラーニング入門の[ドロップアウトとバッチ正規化](https://www.kaggle.com/code/ryanholbrook/dropout-and-batch-normalization)」の翻訳と実行方法の解説

## ドロップアウトとバッチ正規化
過剰適合を防ぎ、トレーニングを安定させるために、これらの特別なレイヤーを追加します。

## 理論編
### はじめに(Introduction)
ディープラーニングの世界には、Dense層だけでなく、さまざまな種類の層があります。（サンプルとして、[Kerasのドキュメント](https://www.tensorflow.org/api_docs/python/tf/keras/layers)を眺めてみるのもおすすめです！）
中にはDense層のようにニューロン同士の接続を定義するものもあれば、前処理やさまざまな変換を行う層もあります。

このレッスンでは、**ニューロン自体は持たない特別な層を2種類**学びます。これらはモデルに追加の機能を与え、状況によっては性能向上に役立ちます。どちらも現代的なアーキテクチャで広く使われています。

### ドロップアウト(Dropout)

最初に紹介するのは「ドロップアウト層」です。これは**過学習（overfitting）を抑えるのに役立ちます**。

前回のレッスンでは、過学習はネットワークが訓練データ中の偶然的なパターン（本質的でないパターン）を学習してしまうことで起こると説明しました。
こうしたパターンを捉えるために、ニューラルネットワークはしばしば**特定の重みの組み合わせ**、いわば「重みの共謀（conspiracy）」が発生します。これが過学習の要因なのですが、このような特定的な組み合わせは壊れやすいため、どれか一つでも欠けさせるだけで回避できるようになります。

これがドロップアウトの発想です。
こうした「共謀」を壊すために、**各学習ステップごとに入力ユニットの一部をランダムに無効化**します。
これにより、ネットワークが偶然的なパターンを学習することが難しくなります。
代わりに、より広く一般的なパターンを探すようになり、結果としてより頑健な重み構造が形成されます。

（↓図：さまざまなランダムなドロップアウト構成を循環するネットワークのアニメーション）
![](https://storage.googleapis.com/kaggle-media/learn/images/a86utxY.gif)*Here, 50% dropout has been added between the two hidden layers.<br/>ここでは、2 つの隠し層の間に 50% のドロップアウトが追加されています。*

ドロップアウトは、**ネットワークのアンサンブル（集合）を作っている**と考えることもできます。予測は1つの大きなネットワークではなく、小さなネットワークの「委員会」によって行われます。それぞれは異なる種類の誤りを犯しますが、同時に正しい予測もするため、全体としては個々より優れた性能になります。（決定木のアンサンブルであるランダムフォレストと同じ発想です。）

#### Dropoutの追加方法

Kerasでは、`rate` 引数で**入力ユニットの何％を無効化するか**を指定します。ドロップアウトを適用したい層の直前に `Dropout` 層を置きます。

```python
keras.Sequential([
    # ...
    layers.Dropout(rate=0.3), # 次の層に30%のドロップアウトを適用
    layers.Dense(16),
    # ...
])
```

### バッチ正規化(Batch Normalization)

次に紹介する特別な層は「バッチ正規化（batch normalization、略してbatchnorm）」です。
これは**学習が遅い・不安定になる問題を改善するのに役立ちます**。

ニューラルネットワークでは、データを共通のスケールに揃えることが一般に重要です。
たとえば、scikit-learn の [`StandardScaler`](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html) や [`MinMaxScaler`](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html) などが使われます。

理由は、SGD（確率的勾配降下法）が、データの活性化の大きさに比例して重みを更新するからです。活性化の大きさが大きく異なる特徴量があると、学習が不安定になることがあります。

それなら、**ネットワーク内部でも正規化すればさらに良いのでは？**
そうです。そのための層がバッチ正規化層です。

バッチ正規化層は、入力される各バッチごとに、

1. バッチ自身の平均と標準偏差で正規化し
2. さらに2つの学習可能なスケーリングパラメータで再スケーリングします

つまり、入力を協調的に再スケーリングする処理を行います。

多くの場合、バッチ正規化は**最適化を助ける目的**で追加されます（予測性能が向上する場合もあります）。
バッチ正規化を使ったモデルは、少ないエポック数で学習が完了する傾向があります。
また、学習が「行き詰まる」問題を解消することもあります。

学習がうまく進まないときは、バッチ正規化を追加してみる価値があります。

#### Batch Normalizationの追加方法

バッチ正規化は、ほぼどこにでも追加できます。層の後に置くこともできます：

```python
layers.Dense(16, activation='relu'),
layers.BatchNormalization(),
```

あるいは、層と活性化関数の間に置くこともできます：

```python
layers.Dense(16),
layers.BatchNormalization(),
layers.Activation('relu'),
```

また、ネットワークの最初の層として追加すると、
scikit-learn の `StandardScaler` の代わりになる**適応型前処理器**のように機能します。

### 例 — DropoutとBatch Normalizationの使用

Red Wineモデルの開発を続けましょう。
今回はモデルの容量をさらに増やしますが、**過学習を抑えるためにドロップアウトを追加し、最適化を速めるためにバッチ正規化を追加**します。

今回はあえてデータの標準化を行いません。
これは、バッチ正規化がどれだけ学習を安定化できるかを示すためです。
```python
# Setup plotting
import matplotlib.pyplot as plt

plt.style.use('seaborn-whitegrid')
# Set Matplotlib defaults
plt.rc('figure', autolayout=True)
plt.rc('axes', labelweight='bold', labelsize='large',
       titleweight='bold', titlesize=18, titlepad=10)


import pandas as pd
red_wine = pd.read_csv('../input/dl-course-data/red-wine.csv')

# Create training and validation splits
df_train = red_wine.sample(frac=0.7, random_state=0)
df_valid = red_wine.drop(df_train.index)

# Split features and target
X_train = df_train.drop('quality', axis=1)
X_valid = df_valid.drop('quality', axis=1)
y_train = df_train['quality']
y_valid = df_valid['quality']
```

ドロップアウトを追加する場合、Dense層のユニット数を増やす必要があることがあります。

```python
from tensorflow import keras
from tensorflow.keras import layers

model = keras.Sequential([
    layers.Dense(1024, activation='relu', input_shape=[11]),
    layers.Dropout(0.3),
    layers.BatchNormalization(),
    layers.Dense(1024, activation='relu'),
    layers.Dropout(0.3),
    layers.BatchNormalization(),
    layers.Dense(1024, activation='relu'),
    layers.Dropout(0.3),
    layers.BatchNormalization(),
    layers.Dense(1),
])
```

学習の設定方法自体は今回変更する必要はありません。

```python
model.compile(
    optimizer='adam',
    loss='mae',
)

history = model.fit(
    X_train, y_train,
    validation_data=(X_valid, y_valid),
    batch_size=256,
    epochs=100,
    verbose=0,
)

# 学習曲線の表示
history_df = pd.DataFrame(history.history)
history_df.loc[:, ['loss', 'val_loss']].plot();
```
![](https://storage.googleapis.com/zenn-user-upload/15cafba1134b-20260220.png)

通常は、学習前にデータを標準化したほうがより良い性能が得られます。
それでも、生データのままでも学習できたという事実は、バッチ正規化が難しいデータセットに対して非常に効果的であることを示しています。

## 実践編
### はじめに(Introduction)
この演習では、Exercise 4 で作成した Spotify モデルにドロップアウトを追加し、さらにバッチ正規化が難しいデータセットでどのように学習を成功させるかを確認します。

次のセルを実行して始めましょう！
![](https://storage.googleapis.com/zenn-user-upload/589b39308b87-20260220.png)

#### まずは Spotify データセットを読み込みます。
![](https://storage.googleapis.com/zenn-user-upload/d9487644b68a-20260220.png)

**結果:** Input shape: [18]

数値特徴量には `StandardScaler` を適用し、カテゴリ変数には `OneHotEncoder` を適用します。
また、同じアーティストのデータが訓練用と検証用にまたがらないよう、`GroupShuffleSplit` を使って分割しています。

目的変数 `track_popularity` は 0〜100 の値なので、100 で割って 0〜1 にスケーリングしています。

# 1) SpotifyモデルにDropoutを追加する

Exercise 4 の最後のモデルがこちらです。

128ユニットのDense層の後と、64ユニットのDense層の後に、それぞれ dropout率0.3 のDropout層を追加してください。

修正後のコード：

```python
model = keras.Sequential([
    layers.Dense(128, activation='relu', input_shape=input_shape),
    layers.Dropout(0.3),           # ← 追加
    layers.Dense(64, activation='relu'),
    layers.Dropout(0.3),           # ← 追加
    layers.Dense(1)
])
```
モデルを学習させると、ドロップアウトを追加した効果が確認できます。
![](https://storage.googleapis.com/zenn-user-upload/62d53661bbf3-20260221.png)

### 2) Dropoutの評価

Exercise 4 では、このモデルは **およそエポック5あたりで過学習**していました。ドロップアウトを追加すると、今回は過剰適合を防ぐのに役立ったようですか?
![](https://storage.googleapis.com/zenn-user-upload/a0fe78062f49-20260221.png)
今回、ドロップアウトを追加したことで：

* 訓練損失と検証損失の乖離が小さくなる
* 検証損失の悪化が遅くなる
* より安定した学習曲線になる

といった改善が見られます。

つまり、**ドロップアウトは過学習の抑制に役立っている**と考えられます。

### 次にバッチ正規化(Batch Normalization)の検証

今回は Concrete データセットを使います。
**あえて標準化は行いません。** これにより、バッチ正規化の効果がよりはっきりと現れます。

![](https://storage.googleapis.com/zenn-user-upload/8a44c1d65dcf-20260221.png)

次のセルを実行して、標準化されていないコンクリート データでネットワークをトレーニングします。
![](https://storage.googleapis.com/zenn-user-upload/38f57bd96423-20260221.png)

結局、グラフは空白になってしまいました。SGD はスケールの違いに敏感なので、標準化されていないデータでは学習が不安定になりやすいです。

実際、バッチ正規化なしでは：
* グラフが空白になる
* 発散する
* 非常に大きな値に収束する
といった問題が起きやすくなります。

### 3) BatchNormalization層を追加する
バッチ正規化はこのような問題の修正に役立ちます。
各Dense層の前に **`BatchNormalization`** 層を追加します。（input_shape は最初の BatchNormalization 層へ移します。）

修正後のコード：

```python
model = keras.Sequential([
    layers.BatchNormalization(input_shape=input_shape), # ← 追加
    layers.Dense(512, activation='relu'),
    
    layers.BatchNormalization(),                        # ← 追加
    layers.Dense(512, activation='relu'),
    
    layers.BatchNormalization(),                        # ← 追加
    layers.Dense(512, activation='relu'),
    
    layers.BatchNormalization(),                        # ← 追加
    layers.Dense(1),
])
# Check your answer
q_3.check()
```
**結果:** Correct

ヒントと答え
![](https://storage.googleapis.com/zenn-user-upload/8b078ad9d9c9-20260221.png)

次のセルを実行して、バッチ正規化によってモデルをトレーニングできるかどうかを確認します。
![](https://storage.googleapis.com/zenn-user-upload/f4e5cace7592-20260221.png)

### 4) Batch Normalizationの評価

バッチ正規化を追加すると：

* 学習が安定する
* 損失がきちんと減少する
* 発散しなくなる
* より小さな検証損失に収束する

ことが確認できます。

つまり、

* **ドロップアウト → 過学習対策**
* **バッチ正規化 → 学習の安定化と高速化**

という役割の違いがあります。

次の章[6.[バイナリ分類](https://zenn.dev/rg687076/articles/24deba89b38315)]へ