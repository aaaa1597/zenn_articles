---
title: "13-③[AI][Kaggle][python]Kaggle入門(機械学習の説明可能性 3.部分的なプロット)"
emoji: "👏"
type: "idea"
topics:
  - "ai"
  - "python"
  - "kaggle"
  - "機械学習の説明可能性"
published: true
published_at: "2026-03-25 19:42"
---

[Kaggle入門13(機械学習の説明可能性 1.モデルの洞察のユースケース(Model Insights))](https://zenn.dev/rg687076/articles/89fe247589e22a)
[Kaggle入門13(機械学習の説明可能性 2.順列の重要性)](https://zenn.dev/rg687076/articles/1060577e1e0b24)
[Kaggle入門13(機械学習の説明可能性 3.部分的なプロット)](https://zenn.dev/rg687076/articles/79a9edbf1fedfe)
[Kaggle入門13(機械学習の説明可能性 4.SHAP値)](https://zenn.dev/rg687076/articles/ab9684095a62b1)
[Kaggle入門13(機械学習の説明可能性 5.最終回 SHAP値の高度な使用法)](https://zenn.dev/rg687076/articles/55c834a07efcca)

← [Kaggle入門12(AI倫理入門)](https://zenn.dev/rg687076/articles/65cc05471a465b)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle入門14(ゲームAIと強化学習入門)](https://zenn.dev/rg687076/articles/49e1d162bfdeec) →

## Abstract
- Kaggle「AI倫理入門 の[部分的なプロット](https://www.kaggle.com/code/dansbecker/partial-plots)」の翻訳と実行方法の解説

## 部分的なプロット
それぞれの特徴は、予測にどのような影響を与えますか？

## 理論編
### 部分依存プロット(Partial Dependence Plots)
特徴量重要度（feature importance）が「どの変数が予測に最も影響するか」を示すのに対し、部分依存プロットは「ある特徴量がどのように予測へ影響するか」を示します。

これは、次のような質問に答えるのに役立ちます。

- 他のすべての住宅の特徴を一定としたとき、経度と緯度は住宅価格にどのような影響を与えるのでしょうか。言い換えると、同じような大きさの家は、地域によってどのように価格が変わるのでしょうか。
- 2つのグループ間で予測される健康の違いは、食生活の違いによるものなのでしょうか。それとも別の要因によるものなのでしょうか。

もし線形回帰やロジスティック回帰モデルに慣れていれば、部分依存プロットはそれらのモデルの係数と似たように解釈できます。ただし、複雑なモデルに対する部分依存プロットは、単純なモデルの係数よりも複雑なパターンを捉えることができます。もし線形回帰やロジスティック回帰に慣れていなくても、この比較については気にする必要はありません。

これからいくつかの例を示し、これらのプロットの解釈方法を説明した後、それらを作成するためのコードを確認します。

### 仕組み(How it Works)

Permutation Importance と同様に、**部分依存プロットはモデルが学習された後に計算されます**。モデルは、人工的に操作されていない実際のデータを使って学習されます。

サッカーの例では、チームはさまざまな点で異なります。例えば、パス数、シュート数、得点数などです。一見すると、これらの特徴量の影響を切り分けるのは難しそうに思えます。

部分依存プロットがどのように各特徴量の影響を分離するのかを理解するために、まず1行のデータを考えます。例えば、その1行のデータは、ボール保持率50%、パス100回、シュート10本、得点1点のチームを表しているかもしれません。

学習済みモデルを使って結果（そのチームの選手が「マン・オブ・ザ・マッチ」を獲得する確率）を予測します。ただし、**1つの変数の値だけを繰り返し変更しながら**予測を行います。例えば、ボール保持率が40%だった場合の予測を行います。次に、50%の場合を予測します。さらに60%の場合を予測します。このように続けます。ボール保持率（横軸）を小さい値から大きい値へと変化させながら、予測結果（縦軸）をプロットします。

ここでは1行のデータだけを使って説明しました。特徴量同士の相互作用によって、1行のデータだけでは典型的でない結果になる可能性があります。そこで、この思考実験を元のデータセットの複数の行で繰り返し、縦軸には予測結果の平均値をプロットします。

---

### コード例(Code Example)
ここではモデル構築が主題ではないため、データ探索やモデル構築のコードにはあまり焦点を当てません。
```python
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.tree import DecisionTreeClassifier

data = pd.read_csv('../input/fifa-2018-match-statistics/FIFA 2018 Statistics.csv')
y = (data['Man of the Match'] == "Yes")  # Convert from string "Yes"/"No" to binary
feature_names = [i for i in data.columns if data[i].dtype in [np.int64]]
X = data[feature_names]
train_X, val_X, train_y, val_y = train_test_split(X, y, random_state=1)
tree_model = DecisionTreeClassifier(random_state=0, max_depth=5, min_samples_split=5).fit(train_X, train_y)
```

最初の例では、以下に示す決定木を使用します。実際の応用では、より高度なモデルを使用することが多いでしょう。

```python
from sklearn import tree
import graphviz

tree_graph = tree.export_graphviz(tree_model, out_file=None, feature_names=feature_names)
graphviz.Source(tree_graph)
```
![](https://storage.googleapis.com/zenn-user-upload/d2dc17f25ed7-20260315.png)

この決定木を読むためのポイントは次の通りです。
- 子ノードを持つノードでは、上部に分割条件が表示されています。
- 下部にある2つの値は、そのノードに含まれるデータについて、ターゲットの False と True の数をそれぞれ示しています。

以下は、scikit-learn ライブラリを使って部分依存プロットを作成するコードです。
```python
from matplotlib import pyplot as plt
from sklearn.inspection import PartialDependenceDisplay

# Create and plot the data
disp1 = PartialDependenceDisplay.from_estimator(tree_model, val_X, ['Goal Scored'])
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/389cd4209a47-20260315.png)

縦軸は、基準となる値（最も左の値）での**予測からどれだけ変化したか**を表します。
このグラフから、1点得点すると「マン・オブ・ザ・マッチ」を獲得する確率が大きく上昇することがわかります。しかし、それ以上の追加得点は予測にほとんど影響を与えていないようです。

以下に別のグラフ例を示します。
```python
feature_to_plot = 'Distance Covered (Kms)'
disp2 = PartialDependenceDisplay.from_estimator(tree_model, val_X, [feature_to_plot])
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/17199e148c17-20260315.png)

このグラフは現実を表すには単純すぎるように見えます。しかし、それはモデル自体が非常に単純だからです。上の決定木を見ると、これがモデルの構造をそのまま表していることがわかるはずです。

異なるモデルの構造や意味合いを簡単に比較することもできます。こちらは同じプロットをランダムフォレストモデルで作成したものです。

```python
# Build Random Forest model
rf_model = RandomForestClassifier(random_state=0).fit(train_X, train_y)

disp3 = PartialDependenceDisplay.from_estimator(rf_model, val_X, [feature_to_plot])
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/62bd58a6509c-20260315.png)

このモデルでは、試合中に選手たちが合計100km程度走ると「マン・オブ・ザ・マッチ」を獲得する可能性が高いと予測しています。ただし、それ以上に走ると予測値は低くなります。

一般的に、この滑らかな曲線は決定木モデルの階段状の関数よりも現実的に見えます。ただし、このデータセットは小さいため、どのモデルの解釈にも慎重になる必要があります。

### 2次元部分依存プロット(2D Partial Dependence Plots)
特徴量同士の相互作用に興味がある場合、2次元の部分依存プロットも役立ちます。例を見ると理解しやすいでしょう。

このグラフでも決定木モデルを使用します。非常に単純なプロットになりますが、グラフの内容を決定木の構造と対応づけて理解できるはずです。

```python
fig, ax = plt.subplots(figsize=(8, 6))
f_names = [('Goal Scored', 'Distance Covered (Kms)')]
# Similar to previous PDP plot except we use tuple of features instead of single feature
disp4 = PartialDependenceDisplay.from_estimator(tree_model, val_X, f_names, ax=ax)
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/0587ada99967-20260315.png)

このグラフは、得点数と走行距離のあらゆる組み合わせに対する予測を示しています。

例えば、チームが少なくとも1点を取り、総走行距離が約100kmのときに最も高い予測値になります。もし得点が0点なら、走行距離は影響しません。得点0の場合を決定木でたどると、このことが確認できるでしょうか。

しかし、得点している場合には走行距離が予測に影響します。この点が2次元部分依存プロットから読み取れることを確認してください。このパターンが決定木の中にも見えるでしょうか。

## 実践編
### セットアップ(Set Up)
今日は、部分依存プロットを作成し、[Taxi Fare Prediction](https://www.kaggle.com/c/new-york-city-taxi-fare-prediction)コンペティションのデータを使って洞察を導く練習をします。

基本的なデータの読み込み、確認、モデル構築を行うコードはすでに用意されています。以下のセルを実行して準備を整えてください。

```python
import pandas as pd
from sklearn.ensemble import RandomForestRegressor
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split

# Environment Set-Up for feedback system.
from learntools.core import binder
binder.bind(globals())
from learntools.ml_explainability.ex3 import *
print("Setup Complete")

# Data manipulation code below here
data = pd.read_csv('../input/new-york-city-taxi-fare-prediction/train.csv', nrows=50000)

# Remove data with extreme outlier coordinates or negative fares
data = data.query('pickup_latitude > 40.7 and pickup_latitude < 40.8 and ' +
                  'dropoff_latitude > 40.7 and dropoff_latitude < 40.8 and ' +
                  'pickup_longitude > -74 and pickup_longitude < -73.9 and ' +
                  'dropoff_longitude > -74 and dropoff_longitude < -73.9 and ' +
                  'fare_amount > 0'
                  )

y = data.fare_amount

base_features = ['pickup_longitude',
                 'pickup_latitude',
                 'dropoff_longitude',
                 'dropoff_latitude']

X = data[base_features]


train_X, val_X, train_y, val_y = train_test_split(X, y, random_state=1)
first_model = RandomForestRegressor(n_estimators=30, random_state=1).fit(train_X, train_y)
print("Data sample:")
data.head()
```
Setup Complete
Data sample:
![](https://storage.googleapis.com/zenn-user-upload/0be703c6bd35-20260317.png)

```python
data.describe()
```
![](https://storage.googleapis.com/zenn-user-upload/b819e7fba050-20260318.png)

### 問1
pickup_longitudeの部分依存プロットを描画するコードを以下に示します。変更せずにそのまま実行してください。
```python
from matplotlib import pyplot as plt
from sklearn.inspection import PartialDependenceDisplay

feat_name = 'pickup_longitude'
PartialDependenceDisplay.from_estimator(first_model, val_X, [feat_name])
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/0b827ca2c5e6-20260318.png)

なぜこの部分依存プロットはU字型になるのでしょうか？

その説明から、他の特徴量の部分依存プロットがどのような形になるか予想できますか？

以下のforループで、他のすべての部分依存プロットを作成してください（上のコードから必要な行をコピーしてください）。

```python
for feat_name in base_features:
    PartialDependenceDisplay.from_estimator(first_model, val_X, [feat_name])
    plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/acf2faaf0ae6-20260318.png)

それらの形はあなたの予想と一致しましたか？実際に見た後で、その形を説明できますか？

以下の行のコメントアウトを外して、自分の考えを確認してください。

![](https://storage.googleapis.com/zenn-user-upload/c40f11118ad1-20260318.png)
順列重要度分析の結果から、タクシー料金を決定する最も重要な要素は距離であるということが示唆されます。
このモデルでは、距離の測定値（緯度や経度の絶対的な変化など）を特徴量として含めていないため、座標の特徴量（pickup_longitudeなど）が距離の影響を捉えています。経度値の中心付近で乗車すると、平均的に乗車距離が短くなるため、予測される運賃は平均的に低くなります。

同様の理由で、すべての部分依存プロットにおいて、概ねU字型の形状が見られます。

### 問2
次に、2次元の部分依存プロットを実行します。参考として、チュートリアルのコードを以下に示します。
```python
fig, ax = plt.subplots(figsize=(8, 6))
f_names = [('Goal Scored', 'Distance Covered (Kms)')]
PartialDependenceDisplay.from_estimator(tree_model, val_X, f_names, ax=ax)
plt.show()
```

pickup_longitudeとdropoff_longitudeの2つの特徴量について、2次元プロットを作成してください。

どのような形になると予想しますか？
```python
fig, ax = plt.subplots(figsize=(8, 6))

# Add your code here
f_names = [('pickup_longitude', 'dropoff_longitude')]
PartialDependenceDisplay.from_estimator(first_model, val_X, f_names, ax=ax)
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/7c091c1eb06f-20260318.png)

以下の行のコメントアウトを外すと、解答と、そのプロット形状の考え方の説明を見ることができます。
![](https://storage.googleapis.com/zenn-user-upload/33bb42602ddd-20260318.png)
グラフには対角線に沿って等高線が描かれているはずです。ある程度はその通りですが、興味深い注意点もあります。

対角線状の等高線が現れるのは、これらの値がピックアップ地点とドロップオフ地点の経度が近い値の組み合わせであり、他の要因を考慮した場合、移動距離が短いことを示しているためです。

中心対角線から離れるにつれて、集荷地点と配達地点の経度間の距離も長くなるため、価格も上昇すると予想されます。

驚くべき点は、このグラフの右上方向へ進むにつれて価格が上昇し、45度の線付近にとどまっている場合でも価格が上昇するということです。

これはさらに調査する価値があるかもしれませんが、このグラフの右上方向に移動することによる影響は、45度の線から離れることによる影響に比べると小さいです。

### 問3
have saved if they'd started the ride at longitude -73.98 instead.
経度-73.955から出発し、-74で終了する乗車を考えます。前の問題のグラフを使って、もし出発地点が-73.98だった場合、どれくらい料金を節約できたかを推定してください。
```python
savings_from_shorter_trip = 6

# Check your answer
q_3.check()
```
![](https://storage.googleapis.com/zenn-user-upload/e57211093ee5-20260318.png)
約6です。価格は15より少し下から9より少し下まで下がります。

解答やヒントを見るには、以下の該当行のコメントアウトを外してください。
![](https://storage.googleapis.com/zenn-user-upload/31e75a5428f3-20260318.png)
まず、経度-74度に対応する垂直レベルを見つけます。次に、切り替える水平値を読み取ります。等高線を使って、自分がどの値に近いかを把握しましょう。正確な価格を1セント単位まで気にするよりも、最も近い整数に丸めても構いません。

### 問4
これまでに見てきたPDPでは、位置情報の特徴量は主に移動距離を間接的に表す指標として使われていました。Permutation importanceのレッスンでは、距離をより直接的に表す指標としてabs_lon_changeとabs_lat_changeを追加しました。

ここでもこれらの特徴量を作成してください。上の2行だけを埋めれば十分です。その後、次のセルを実行してください。

**実行後、この部分依存プロットと、絶対値特徴量を使わない場合のプロットとの最も重要な違いを特定してください。絶対値特徴量なしのPDPを生成するコードは、このセルの冒頭にあります。**

```python
# This is the PDP for pickup_longitude without the absolute difference features. Included here to help compare it to the new PDP you create
feat_name = 'pickup_longitude'
PartialDependenceDisplay.from_estimator(first_model, val_X, [feat_name])
plt.show()

# Your code here
# create new features
data['abs_lon_change'] = abs(data.dropoff_longitude - data.pickup_longitude)
data['abs_lat_change'] = abs(data.dropoff_latitude - data.pickup_latitude)

features_2  = ['pickup_longitude',
               'pickup_latitude',
               'dropoff_longitude',
               'dropoff_latitude',
               'abs_lat_change',
               'abs_lon_change']

X = data[features_2]
new_train_X, new_val_X, new_train_y, new_val_y = train_test_split(X, y, random_state=1)
second_model = RandomForestRegressor(n_estimators=30, random_state=1).fit(new_train_X, new_train_y)

feat_name = 'pickup_longitude'
disp = PartialDependenceDisplay.from_estimator(second_model, new_val_X, [feat_name])
plt.show()

# Check your answer
q_4.check()
```
![](https://storage.googleapis.com/zenn-user-upload/48c1475ec99f-20260318.png)

以下の行のコメントアウトを外すと、ヒントや解答（プロット間の重要な違いの説明を含む）を見ることができます。
![](https://storage.googleapis.com/zenn-user-upload/f1ebd988f616-20260318.png)
ヒント: abs_lat_changeとabs_lon_changeフィーチャを作成する際は、abs関数を使用してください。それ以外の変更は必要ありません。
答え: 違いは、部分依存プロットが小さくなったことです。どちらのグラフも、垂直方向の最小値は8.5です。しかし、上のグラフの垂直方向の最大値は約10.7であるのに対し、下のグラフの垂直方向の最大値は9.1未満です。つまり、移動距離の絶対値を考慮に入れると、ピックアップ地点の経度が予測に与える影響は小さくなります。

### 問5
予測に使う特徴量が2つだけある状況を考えます。それらをfeat_Aとfeat_Bと呼びます。両方の特徴量の値の範囲は-1から1です。feat_Aの部分依存プロットは全範囲で急激に増加しますが、feat_Bのプロットはより緩やかに増加します。

これは、feat_Aの方がfeat_BよりもPermutation Importanceが高くなることを保証しますか？その理由は何ですか？

考えた後、以下の行のコメントアウトを外して解答を確認してください。
![](https://storage.googleapis.com/zenn-user-upload/2c82d5af2171-20260318.png)
いいえ。これはfeat_aがより重要であることを保証するものではありません。例えば、feat_aは変動する場合に大きな影響を与える可能性がありますが、99%の確率で単一の値を持つ可能性もあります。その場合、feat_a の順列を変更しても、ほとんどの値は変わらないため、あまり意味はありません。

### 問6
以下のコードセルでは次の処理を行います：

1. 範囲[-2, 2]のランダムな値を持つ2つの特徴量X1とX2を作成します。
2. 常に1である目的変数yを作成します。
3. X1とX2からyを予測するRandomForestRegressorモデルを学習します。
4. X1のPDPプロットと、X1とyの散布図を作成します。

このPDPプロットがどのようになるか予想できますか？セルを実行して確認してください。

yの初期化を変更して、PDPが範囲[-1,1]では正の傾きを持ち、それ以外では負の傾きを持つようにしてください（注意：変更するのはyの作成部分のみで、X1、X2、my_modelは変更しないこと）。
```python
import numpy as np
from numpy.random import rand

n_samples = 20000

# Create array holding predictive feature
X1 = 4 * rand(n_samples) - 2
X2 = 4 * rand(n_samples) - 2

# Your code here
# Create y. you should have X1 and X2 in the expression for y
y = -2 * X1 * (X1<-1) + X1 - 2 * X1 * (X1>1) - X2

# create dataframe 
my_df = pd.DataFrame({'X1': X1, 'X2': X2, 'y': y})
predictors_df = my_df.drop(['y'], axis=1)

my_model = RandomForestRegressor(n_estimators=30, random_state=1).fit(predictors_df, my_df.y)
disp = PartialDependenceDisplay.from_estimator(my_model, predictors_df, ['X1'])
plt.show()

# Check your answer
q_6.check()
```
![](https://storage.googleapis.com/zenn-user-upload/a6582b1247cb-20260318.png)

ヒントや解決策については、以下の行のコメントを解除してください。
![](https://storage.googleapis.com/zenn-user-upload/9295e24100d3-20260318.png)
ヒント：(X1 < -1) のような数式を含む用語を明示的に使用することを検討してください。
答え: # 解決策は数​​多く存在します。
      # y の表現例は次のとおりです。
      y = -2 * X1 * (X1<-1) + X1 - 2 * X1 * (X1>1) - X2
      # これ以上の変更は必要ありません。

### 問7
2つの特徴量と1つの目的変数からなるデータセットを作成してください。ただし、1つ目の特徴量のPDPはフラットである一方、Permutation Importanceは高くなるようにします。モデルにはRandomForestを使用します。
※注意：X1、X2、yを作成する行だけを書けば十分です。モデル構築や分析のコードはすでに用意されています。
```python
import eli5
from eli5.sklearn import PermutationImportance

n_samples = 20000

# Create array holding predictive feature
X1 = 4 * rand(n_samples) - 2
X2 = 4 * rand(n_samples) - 2
# Create y. you should have X1 and X2 in the expression for y
y = X1 * X2


# create dataframe because pdp_isolate expects a dataFrame as an argument
my_df = pd.DataFrame({'X1': X1, 'X2': X2, 'y': y})
predictors_df = my_df.drop(['y'], axis=1)

my_model = RandomForestRegressor(n_estimators=30, random_state=1).fit(predictors_df, my_df.y)


disp = PartialDependenceDisplay.from_estimator(my_model, predictors_df, ['X1'], grid_resolution=300)
plt.show()

perm = PermutationImportance(my_model).fit(predictors_df, my_df.y)

# Check your answer
q_7.check()

# show the weights for the permutation importance you just calculated
eli5.show_weights(perm, feature_names = ['X1', 'X2'])
```
![](https://storage.googleapis.com/zenn-user-upload/2dd8247847ac-20260318.png)

ヒントや解答を見るには、以下の行のコメントアウトを外してください。
![](https://storage.googleapis.com/zenn-user-upload/1d06bb6dc834-20260318.png)
ヒント：X1が予測に影響を与えることで、順列の重要度にも影響を与えるようになります。しかし、PDP要件を満たすには、平均効果が0である必要があります。これを実現するには、相互作用を作成し、X1の効果がX2の値に依存し、その逆も同様になるようにします。

部分依存プロットは非常に興味深いものです。実世界のどんなテーマや疑問に対して部分依存プロットを使ってみたいか、[議論するスレッド](https://www.kaggle.com/discussions/getting-started/65782)も用意されています。

次は、[SHAP値](https://www.kaggle.com/code/dansbecker/shap-values)が個々の予測の論理を理解する上でどのように役立つかを学びましょう。

次の章[4.[SHAP値](https://zenn.dev/rg687076/articles/ab9684095a62b1)]へ