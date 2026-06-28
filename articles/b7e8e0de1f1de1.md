---
title: "6-⑥[AI][Kaggle][python]Kaggle入門(特徴エンジニアリング 6.ターゲットエンコーディング)"
emoji: "🙆‍♀️"
type: "tech"
topics:
  - "ai"
  - "python"
  - "kaggle"
  - "特徴エンジニアリング"
published: true
published_at: "2026-02-12 21:46"
---

[Kaggle入門6(特徴エンジニアリング 1.特徴エンジニアリングとは)](https://zenn.dev/rg687076/articles/96f85e2e4dadc6)
[Kaggle入門6(特徴エンジニアリング 2.相互情報)](https://zenn.dev/rg687076/articles/b2ce4ad88d2883)
[Kaggle入門6(特徴エンジニアリング 3.特徴量の作成)](https://zenn.dev/rg687076/articles/6523714297acd2)
[Kaggle入門6(特徴エンジニアリング 4.K平均法によるクラスタリング)](https://zenn.dev/rg687076/articles/e6312c5f1d01b9)
[Kaggle入門6(特徴エンジニアリング 5.主成分分析)](https://zenn.dev/rg687076/articles/ea0893f848cc1d)
[Kaggle入門6(特徴エンジニアリング 6.最終回 ターゲットエンコーディング)](https://zenn.dev/rg687076/articles/b7e8e0de1f1de1)

← [Kaggle入門5(データの可視化)](https://zenn.dev/rg687076/articles/c99c2b11875b1a)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle入門7(SQL入門)](https://zenn.dev/rg687076/articles/d7cf749c7ba539) →

## Abstract
Kaggle「特徴エンジニアリングの[ターゲットエンコーディング](https://www.kaggle.com/code/ryanholbrook/target-encoding)」の翻訳と実行方法の解説

## ターゲットエンコーディング
この強力なテクニックを使用して、あらゆるカテゴリ機能を強化します。

## 理論編
### はじめに(Introduction)
このコースでこれまでに見てきた手法の多くは、数値特徴量を対象としたものでした。今回のレッスンで扱う「ターゲットエンコーディング」は、それとは異なり、カテゴリ特徴量を対象とする手法です。

これは、ワンホットエンコーディングやラベルエンコーディングのようにカテゴリを数値に変換する方法ですが、違いは「ターゲット（目的変数）」の情報も使ってエンコードを作成する点にあります。そのため、これは教師ありの特徴量エンジニアリング手法と呼ばれます。
```python
import pandas as pd

autos = pd.read_csv("../input/fe-course-data/autos.csv")
```

### ターゲットエンコーディング（Target Encoding）

ターゲットエンコーディングとは、**特徴量のカテゴリを、目的変数から導かれた数値で置き換えるエンコーディング手法**のことです。

シンプルで効果的な方法のひとつは、Lesson 3 で扱ったような **グループごとの集約（例えば平均）** を用いることです。Automobiles データセットを使うと、各メーカー（make）の平均価格は次のように計算できます。

```python
autos["make_encoded"] = autos.groupby("make")["price"].transform("mean")
autos[["make", "price", "make_encoded"]].head(10)
```
| id | make | price | make_encoded |
|:---|:---|:---|:---|
| 0 | alfa-romero | 13495 | 15498.333333 |
| 1 | alfa-romero | 16500 | 15498.333333 |
| 2 | alfa-romero | 16500 | 15498.333333 |
| 3 | audi | 13950 | 17859.166667 |
| 4 | audi | 17450 | 17859.166667 |
| 5 | audi | 15250 | 17859.166667 |
| 6 | audi | 17710 | 17859.166667 |
| 7 | audi | 18920 | 17859.166667 |
| 8 | audi | 23875 | 17859.166667 |
| 9 | bmw | 16430 | 26118.750000 |

これは、各車のメーカーごとに価格の平均を計算し、その値でカテゴリを置き換えています。

この種のターゲットエンコーディングは **mean encoding（平均エンコーディング）** と呼ばれることがあります。
目的変数が二値の場合には **bin counting** とも呼ばれます。

他にも次のような名前があります：

* 尤度エンコーディング: likelihood encoding
* 影響度エンコーディング: impact encoding
* 1つを除外するエンコード: leave-one-out encoding

### スムージング（Smoothing）

しかし、この方法にはいくつか問題があります。
#### 1.未知カテゴリの問題

ターゲットエンコーディングは**過学習のリスクが高い**ため、エンコーディングは独立した「encoding用のデータ分割」で学習する必要があります。

その後、将来のデータにこのエンコーディングを結合すると、
encoding分割に存在しなかったカテゴリには **欠損値（NaN）** が入ります。

これらは何らかの方法で補完する必要があります。

#### 2. レアカテゴリの問題

出現回数が少ないカテゴリでは、計算された統計量は信頼性が低くなります。

Automobiles データでは、`mercury` は1回しか登場しません。
その平均価格は、その1台の価格そのものです。

これは将来の Mercury 車の代表値としては不適切かもしれません。

レアカテゴリをそのままターゲットエンコードすると、**過学習が起こりやすくなります。**

### 解決策：スムージング
この問題を解決する方法が **スムージング** です。

考え方は、
> 「カテゴリ内の平均」と「全体平均」をブレンド（混合）する
というものです。

* 出現回数が少ないカテゴリ → 全体平均の重みを大きく
* 未知カテゴリ → 全体平均のみ

### 擬似コード
```
encoding = weight * in_category + (1 - weight) * overall
```

ここで weight は 0〜1 の値で、カテゴリの出現頻度から計算されます。

### m推定（m-estimate）
簡単な方法として、m推定を使います：
```
weight = n / (n + m)
```
* n：そのカテゴリの出現回数
* m：スムージング係数（ハイパーパラメータ）
m が大きいほど、**全体平均の重みが大きくなります。**
![](https://storage.googleapis.com/zenn-user-upload/8a3b818be635-20260211.png)

#### 例

Automobiles データでは、`chevrolet` は3台あります。
m=2.0 とすると：
```
weight = 3 / (3 + 2) = 0.6
```
つまり、
```
chevrolet = 0.6 × Chevrolet平均価格 + 0.4 × 全体平均価格
```

具体的には：
```
0.6 × 6000.00 + 0.4 × 13285.03
```

### m の選び方

m の値は「カテゴリ内のばらつき」に依存します。
* メーカー内で価格が大きく変動する → 大きめの m
* メーカーごとの平均価格が安定している → 小さめの m

### ターゲットエンコーディングの用途
#### ・高カーディナリティ特徴量
カテゴリ数が非常に多い特徴量に有効です。
* One-hot → 次元が爆発する
* Label encoding → 数値に順序性が生まれて不適切な場合あり
ターゲットエンコーディングは、「その特徴量がターゲットとどう関係しているか」という最も重要な情報を使って数値化します。

※高カーディナリティ(High Cardinality)とは、列(カラム)の値において、ユニーク（一意）な値の数、またはバリエーションが非常に多い状態を指します。

#### ・ドメイン知識に基づく特徴量

経験的に重要だと思われるカテゴリ特徴量が、単純な指標では重要度が低く出ることがあります。
ターゲットエンコーディングは、その特徴量の本当の情報量を引き出す助けになります。

### 例：MovieLens1M

MovieLens1M データセットには、100万件の映画評価データが含まれています。
```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns
import warnings

plt.style.use("seaborn-whitegrid")
plt.rc("figure", autolayout=True)
plt.rc(
    "axes",
    labelweight="bold",
    labelsize="large",
    titleweight="bold",
    titlesize=14,
    titlepad=10,
)
warnings.filterwarnings('ignore')


df = pd.read_csv("../input/fe-course-data/movielens1m.csv")
df = df.astype(np.uint8, errors='ignore') # reduce memory footprint
print("Number of Unique Zipcodes: {}".format(df["Zipcode"].nunique()))
```
Number of Unique Zipcodes: 3439
Zipcode のユニーク数は 3439 と、3000以上のカテゴリがあるため、ターゲットエンコーディングに適しています。

### エンコーディング用データ分割（25%）

```python
X = df.copy()
y = X.pop('Rating')

X_encode = X.sample(frac=0.25)
y_encode = y[X_encode.index]
X_pretrain = X.drop(X_encode.index)
y_train = y[X_pretrain.index]
```
scikit-learn-contrib の category_encoders パッケージは m-estimate エンコーダーを実装しており、これを使用して Zipcode 機能をエンコードします。

### m推定エンコーダの使用

```python
from category_encoders import MEstimateEncoder

# Create the encoder instance. Choose m to control noise.
# エンコーダインスタンスを作成します。ノイズを制御するにはmを選択します。
encoder = MEstimateEncoder(cols=["Zipcode"], m=5.0)

# Fit the encoder on the encoding split.
# エンコーダーをエンコーディング分割に適合させます。
encoder.fit(X_encode, y_encode)

# Encode the Zipcode column to create the final training data
# 郵便番号列をエンコードして最終的なトレーニングデータを作成します。
X_train = encoder.transform(X_pretrain)
```

エンコードされた値をターゲットと比較して、エンコードがどの程度有益であるかを確認してみましょう。

```python
plt.figure(dpi=90)
ax = sns.distplot(y, kde=False, norm_hist=True)
ax = sns.kdeplot(X_train.Zipcode, color='r', ax=ax)
ax.set_xlabel("Rating")
ax.legend(labels=['Zipcode', 'Rating']);
```
![](https://storage.googleapis.com/zenn-user-upload/7542bb09602b-20260211.png)

### 分布の比較
エンコードされた Zipcode の分布と、実際の Rating の分布を比較すると、下記が確認できます。
* エンコード値の分布が実際の評価の分布に概ね沿っている

これは、

> 郵便番号ごとに映画の評価傾向に十分な違いがあり、ターゲットエンコーディングが有益な情報を捉えられている

ことを意味します。

## 実践編
### はじめに(Introduction)
この演習では、Amesデータセットの特徴量にターゲットエンコーディングを適用します。 このセルを実行して設定を完了してください。

![](https://storage.googleapis.com/zenn-user-upload/d2ba1a0699dc-20260211.png)

まず、ターゲットエンコーディングを適用する特徴量を選択する必要があります。カテゴリ数が多いカテゴリ特徴量は、多くの場合、適切な候補となります。
以下セルを実行すると、Ames データセット内の各カテゴリ機能にカテゴリがいくつあるかを確認できます。
![](https://storage.googleapis.com/zenn-user-upload/c43b4592bc44-20260211.png)

M推定エンコーディングでは、まれなカテゴリに対する推定を改善するためにスムージングを用いる、という話をしました。

データセット内で各カテゴリが何回出現しているかを確認するには、value_counts メソッドを使うことができます。

このセルでは SaleType の出現回数を表示していますが、他のカテゴリについても確認してみるとよいでしょう。

![](https://storage.googleapis.com/zenn-user-upload/cd6e0e7b7c2e-20260211.png)

### 1) エンコーディングの特徴を選択する
ターゲットエンコーディングにどのような特徴を特定しましたか？ 答えを考えた後、次のセルで議論してみましょう。

![](https://storage.googleapis.com/zenn-user-upload/1bcb84df0cce-20260211.png)
Neighborhood 特徴量は有望に見えます。これはすべての特徴量の中で最も多くのカテゴリを持ち、そのうちのいくつかは出現頻度が低いものです。

他に検討する価値がありそうなのは、SaleType、MSSubClass、Exterior1st、Exterior2nd などです。

実際のところ、名義尺度（nominal）の特徴量のほとんどは、まれなカテゴリが多く存在するため、試してみる価値があるでしょう。

---

次に、選択した特徴量にターゲットエンコーディングを適用します。チュートリアルで説明したように、過学習を避けるため、トレーニングセットから除外したデータにエンコーダーを適合させる必要があります。このセルを実行して、エンコーディングとトレーニングの分割を作成します。

![](https://storage.googleapis.com/zenn-user-upload/f89e9cf9efb8-20260211.png)

### 2) M推定エンコーディングを適用する
選択したカテゴリ特徴量にターゲットエンコーディングを適用します。また、平滑化パラメータmの値も選択します（正解であれば任意の値で構いません）。

![](https://storage.googleapis.com/zenn-user-upload/648463d885ca-20260211.png)

ヒントと答え
![](https://storage.googleapis.com/zenn-user-upload/79b35b7e2ab0-20260211.png)

エンコードされた特徴がターゲットとどのように比較されるかを確認したい場合は、次のセルを実行できます。
![](https://storage.googleapis.com/zenn-user-upload/1d1daaf32ea0-20260211.png)
分布図から、エンコードは有益であるように見えますか？
このセルには、エンコードされたセットと元のセットを比較したスコアが表示されます。
![](https://storage.googleapis.com/zenn-user-upload/ee45fa44a223-20260211.png)

この場合、ターゲットエンコーディングは有効だったと思いますか。
選択した特徴量によっては、ベースラインよりも大幅に悪いスコアになったかもしれません。
その場合、エンコーディングによって得られた追加情報が、エンコーディングのために失われたデータの損失を補うことができなかった可能性が高いでしょう。

---

ここでは、ターゲットエンコーディングにおける過学習の問題を探ります。
これは、ターゲットエンコーダーを学習データとは別に取り分けたデータで学習させることの重要性を示すものです。

では、エンコーダーとモデルを同じデータセットで学習させると何が起こるかを見てみましょう。

過学習がどれほど劇的に起こり得るかを強調するために、SalePrice とは本来まったく関係がないはずの特徴量、つまり 0, 1, 2, 3, 4, 5, … というカウント値を平均エンコーディングしてみます。
![](https://storage.googleapis.com/zenn-user-upload/1c1a7ad1d50e-20260211.png)
ほぼ満点です！

![](https://storage.googleapis.com/zenn-user-upload/4cc8917dc87b-20260211.png)
そして分布もほぼ同じです。

### 3) ターゲットエンコーダによるオーバーフィッティング
平均エンコーディングの仕組みを理解した上で、カウント特徴量を平均エンコーディングした後、XGBoost がどのようにしてほぼ完璧なフィッティングを実現できたのかを説明していただけますか？

![](https://storage.googleapis.com/zenn-user-upload/8c58f4eea60b-20260211.png)
Count には重複する値がないため、mean-encoded された Count は本質的に target の正確なコピーとなります。言い換えれば、mean-encoded によって、全く意味のない特徴量が完全な特徴量に変換されたということです。

これがうまくいった唯一の理由は、XGBoostをエンコーダの学習に使用したのと同じデータセットで学習させたからです。もしホールドアウトデータセットを使用していたら、この「偽の」エンコードは学習データに全く反映されなかったでしょう。

教訓は、ターゲットエンコーダーを使用する場合、エンコーダーのトレーニングとモデルのトレーニングに別々のデータセットを使用することが非常に重要であるということです。そうしないと、結果が非​​常に残念なものになる可能性があります。

ヒント
![](https://storage.googleapis.com/zenn-user-upload/eaaf073e0733-20260211.png)
Countが0のとき、Targetの平均値はいくらでしょうか？0は最初の行にしか出現しないので、10になります。では、Countに重複する値が存在しないことを前提に、Countを平均エンコードするとどうなるでしょうか？

### 終わりに

以上で「特徴量エンジニアリング」は終了です！
私たちとの学習を楽しんでいただけたなら幸いです。

さて、新しく身につけたスキルを試してみる準備はできましたか？
今こそ、Housing Prices Getting Started コンペティションに参加する絶好のタイミングです。これまで一緒に取り組んできた内容をまとめたスターターノートブックを用意したボーナスレッスンもあります。

#### 参考文献
さらに詳しく学びたい方のために、いくつかの優れた資料をご紹介します。いずれも本コースの内容を形作るうえで参考にしたものです。
- 『The Art of Feature Engineering』Pablo Duboue 著
- 「An Empirical Analysis of Feature Engineering for Predictive Modeling」Jeff Heaton による論文
- 『Feature Engineering for Machine Learning』Alice Zheng、Amanda Casari 著（クラスタリングのチュートリアルは本書から着想を得ています）
- 『Feature Engineering and Selection』Max Kuhn、Kjell Johnson 著

## お疲れさまでした。
特徴エンジニアリングを修了しました！お疲れ様でした！

