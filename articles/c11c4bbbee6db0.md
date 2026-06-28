---
title: "4-②[AI][Kaggle][python]Kaggle入門(中級機械学習 2.欠損値)"
emoji: "✨"
type: "tech"
topics:
  - "ai"
  - "python"
  - "kaggle"
  - "中級機械学習"
published: true
published_at: "2026-01-21 20:59"
---

[Kaggle入門4(中級機械学習 1.はじめに)](https://zenn.dev/rg687076/articles/4adfbc1ce1de20)
[Kaggle入門4(中級機械学習 2.欠損値)](https://zenn.dev/rg687076/articles/c11c4bbbee6db0)
[Kaggle入門4(中級機械学習 3.カテゴリ変数)](https://zenn.dev/rg687076/articles/ca86ff28e6a56f)
[Kaggle入門4(中級機械学習 4.パイプライン)](https://zenn.dev/rg687076/articles/1a71243868720c)
[Kaggle入門4(中級機械学習 5.クロスバリデーション)](https://zenn.dev/rg687076/articles/9c65a6431a1458)
[Kaggle入門4(中級機械学習 6.XGBoost)](https://zenn.dev/rg687076/articles/1dc42ebf33e52d)
[Kaggle入門4(中級機械学習 7.最終回 データ漏洩(データリーク))](https://zenn.dev/rg687076/articles/54c0cf1a587f46)

← [Kaggle入門3(地理空間データ分析)](https://zenn.dev/rg687076/articles/4028c3853f6ebf)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle入門5(データの可視化)](https://zenn.dev/rg687076/articles/c99c2b11875b1a) →

参加コンペティション
https://www.kaggle.com/competitions/home-data-for-ml-course/submissions

## Abstract
Kaggle「地理空間データ分析の[欠損値](https://www.kaggle.com/code/alexisbcook/missing-values)」の翻訳と実行方法の解説

## 欠損値
欠損値は起こり得ます。実際のデータセットではよくあるこの事象に備えてください。

このチュートリアルでは、欠損値に対処するための3つのアプローチを学習します。その後、実際のデータセットでこれらのアプローチの有効性を比較します。

## 理論編
欠損値の扱い方に関する新しい知識を試す番です。おそらく大きな違いが出てくるでしょう。

### はじめに
データに欠損値が含まれてしまう理由はたくさんあります。例えば：
- 2部屋の物件データには、「3部屋目の広さ」という値は存在しません。
- アンケートの回答者が、自分の年収を「答えない」と選択するかもしれません。

scikit-learnを含むほとんどの機械学習ライブラリは、欠損値があるままモデルを作ろうとするとエラーを出してしまいます。そのため、以下のいずれかの戦略を選ぶ必要があります。

#### 3つのアプローチ
##### 1) シンプルな選択肢：欠損値のある列を削除する
一番手っ取り早いのは、欠損値が一つでも含まれている列（カラム）を丸ごと削除してしまうことです。
![](https://storage.googleapis.com/kaggle-media/learn/images/Sax80za.png)

ただし、その列のほとんどが欠損値でない限り、この方法だと多くの「有用かもしれない情報」を捨ててしまうことになります。極端な例ですが、10,000行あるデータのうち、重要な列でたった1ヶ所だけ値が抜けていたとします。この方法だと、その列を完全に削除してしまいます！

##### 2) より良い選択肢：補完（Imputation）
「補完」とは、欠損値を何らかの数値で埋める方法です。例えば、その列の平均値で埋める、といった具合です。
![](https://storage.googleapis.com/kaggle-media/learn/images/4BpnlPA.png)
補完された値は、ほとんどの場合で正確な正解ではありませんが、列ごと削除してしまうよりは、モデルの精度が高くなるのが一般的です。

##### 3) 補完の拡張版
補完は標準的な手法で、基本的にはうまくいきます。しかし、補完された値は、実際の値（収集されなかった真の値）よりも系統的に高かったり低かったりする可能性があります。あるいは、欠損値がある行自体に何か特別な特徴があるかもしれません。その場合、「どの値がもともと欠損していたか」をモデルに考慮させることで、より良い予測ができるようになります。

![](https://storage.googleapis.com/kaggle-media/learn/images/UWOyg4a.png)

このアプローチでは、まず通常通り欠損値を補完します。それに加えて、もともと欠損があった各列に対して、「どこが補完されたか」を示す新しい列を追加します。 これが劇的に結果を改善することもあれば、全く役に立たないこともあります。

### 例題：メルボルンの住宅データ
メルボルンの住宅データを使って、部屋数や土地の広さから価格を予測してみましょう。
※データの読み込みステップは省略し、すでに学習用データ（X_train, y_train）と検証用データ（X_valid, y_valid）が準備できている状態からスタートします。

```python
import pandas as pd
from sklearn.model_selection import train_test_split

# Load the data
data = pd.read_csv('../input/melbourne-housing-snapshot/melb_data.csv')

# Select target
y = data.Price

# To keep things simple, we'll use only numerical predictors
melb_predictors = data.drop(['Price'], axis=1)
X = melb_predictors.select_dtypes(exclude=['object'])

# Divide data into training and validation subsets
X_train, X_valid, y_train, y_valid = train_test_split(X, y, train_size=0.8, test_size=0.2, random_state=0)
```

#### 各アプローチの質を測る関数の定義
欠損値処理のどのアプローチが良いかを比較するために、score_dataset() という関数を定義します。この関数は、ランダムフォレストモデルを使って 平均絶対誤差 (MAE) を算出します。

#### アプローチ1（列の削除）のスコア
学習データと検証データの両方で、欠損値がある同じ列を削除します。

```python
# 欠損値がある列の名前を取得
cols_with_missing = [col for col in X_train.columns
                     if X_train[col].isnull().any()]

# 学習データと検証データから該当する列を削除
reduced_X_train = X_train.drop(cols_with_missing, axis=1)
reduced_X_valid = X_valid.drop(cols_with_missing, axis=1)

print("アプローチ1（列削除）の MAE:")
print(score_dataset(reduced_X_train, reduced_X_valid, y_train, y_valid))
```
結果: 183550.22137772635

#### アプローチ2（補完）のスコア
次に、SimpleImputer を使って欠損値を平均値で埋めます。 平均値での補完はシンプルですが、通常は非常に良いパフォーマンスを発揮します。統計学者はより複雑な補完方法（回帰補完など）を試すことがありますが、高度な機械学習モデルに組み込む場合、複雑な戦略をとっても追加のメリットが得られないことがよくあります。

```python
from sklearn.impute import SimpleImputer

# 補完（Imputation）
my_imputer = SimpleImputer()
imputed_X_train = pd.DataFrame(my_imputer.fit_transform(X_train))
imputed_X_valid = pd.DataFrame(my_imputer.transform(X_valid))

# 補完によって消えてしまった列名を元に戻す
imputed_X_train.columns = X_train.columns
imputed_X_valid.columns = X_valid.columns

print("アプローチ2（補完）の MAE:")
print(score_dataset(imputed_X_train, imputed_X_valid, y_train, y_valid))
```
結果: 178166.46269899711

アプローチ2の方がアプローチ1よりもMAEが低いため、このデータセットでは補完の方が優れた結果となりました。

#### アプローチ3（補完の拡張版）のスコア
最後に、値を補完しつつ、「どこを補完したか」の履歴を残す方法です。

```python
# 元のデータを書き換えないようにコピーを作成
X_train_plus = X_train.copy()
X_valid_plus = X_valid.copy()

# 補完される箇所を示す新しい列を作成
for col in cols_with_missing:
    X_train_plus[col + '_was_missing'] = X_train_plus[col].isnull()
    X_valid_plus[col + '_was_missing'] = X_valid_plus[col].isnull()

# 補完
my_imputer = SimpleImputer()
imputed_X_train_plus = pd.DataFrame(my_imputer.fit_transform(X_train_plus))
imputed_X_valid_plus = pd.DataFrame(my_imputer.transform(X_valid_plus))

# 列名を元に戻す
imputed_X_train_plus.columns = X_train_plus.columns
imputed_X_valid_plus.columns = X_valid_plus.columns

print("アプローチ3（補完の拡張版）の MAE:")
print(score_dataset(imputed_X_train_plus, imputed_X_valid_plus, y_train, y_valid))
```
結果: 178927.503183954

見ての通り、アプローチ3はアプローチ2よりもわずかに悪い結果となりました。

### なぜ「補完」が「列削除」より良かったのか？
この学習データには10,864行と12列のデータがあり、そのうち3つの列に欠損値が含まれています。しかし、どの列も欠損値は半分以下です。そのため、列ごと削除してしまうと多くの有用な情報が失われてしまうため、補完した方が良い結果になるのは納得がいきます。

```python
# 学習データの形を確認 (行数, 列数)
print(X_train.shape)

# 各列の欠損値の数を確認
missing_val_count_by_column = (X_train.isnull().sum())
print(missing_val_count_by_column[missing_val_count_by_column > 0])
```
(10864, 12)
Car（車庫）: 49
BuildingArea（建物面積）: 5156
YearBuilt（建築年）: 4307

### まとめ
よくあるケースですが、欠損値のある列を単に削除する（アプローチ1）よりも、欠損値を補完する（アプローチ2および3）方が、より良い結果を得ることができました。

## 実践編
次は、欠損値の処理に関する新しい知識を試してみる番です。きっと大きな違いが感じられるでしょう。

### 設定
質問はあなたの作業に対するフィードバックを提供します。次のセルを実行してフィードバックシステムを設定してください。
![](https://storage.googleapis.com/zenn-user-upload/a63866ec4e5e-20260121.png)

この演習では、Kaggle Learn ユーザー向けの住宅価格コンペティションのデータを操作します。
![Ames Housing dataset image](https://storage.googleapis.com/kaggle-media/learn/images/lTJVG4e.png)
次のコードセルを変更せずに実行すると、トレーニングセットと検証セットがX_train、X_valid、y_train、y_validにロードされます。テストセットはX_testにロードされます。

![](https://storage.googleapis.com/zenn-user-upload/75d3b7331c02-20260121.png)

![](https://storage.googleapis.com/zenn-user-upload/1616b3838c9c-20260121.png)
最初の数行にはすでに欠損値がいくつか見られます。次のステップでは、データセット内の欠損値についてより包括的に理解していきます

### ステップ1：予備調査
以下のコードセルを変更せずに実行します。
![](https://storage.googleapis.com/zenn-user-upload/58b15240bfd4-20260121.png)

#### パートA
上記の出力を使用して、以下の質問に答えてください。
![](https://storage.googleapis.com/zenn-user-upload/9d5e19b0d6be-20260121.png)

##### ヒントと答えは以下の通り
![](https://storage.googleapis.com/zenn-user-upload/8ade0ecf1eb4-20260121.png)

#### パートB
上記の回答を考慮すると、欠損値を処理するための最善のアプローチはどれだと思いますか?
![](https://storage.googleapis.com/zenn-user-upload/2467944b1ba6-20260121.png)
このデータには欠損値が比較的少ないため（欠損率が最も高い列でも20%未満です）、列ごと削除してしまう手法では、あまり良い結果は期待できないでしょう。なぜなら、削除によって多くの貴重なデータを捨ててしまうことになるからです。したがって、統計的な値で穴埋めをする『補完（imputation）』の方が、より良いパフォーマンスを発揮する可能性が高いです。

##### ヒントは以下の通り
![](https://storage.googleapis.com/zenn-user-upload/6060b153c77a-20260121.png)
ヒント: データセットには欠損値がたくさんありますか、それとも少ししかありませんか？欠損値のある列を完全に無視すると、多くの情報が失われるでしょうか？

欠損値の処理方法を比較するために、チュートリアルと同じ score_dataset() 関数を使用します。この関数は、ランダムフォレストモデルの平均絶対誤差（MAE）を報告します。

### ステップ2：欠損値のある列を削除する
このステップでは、X_trainとX_validのデータを前処理して、欠損値のある列を削除します。前処理済みのDataFrameをそれぞれreduced_X_trainとreduced_X_validに設定します。
![](https://storage.googleapis.com/zenn-user-upload/7b3eb4e0257b-20260121.png)

##### ヒントと答えは以下の通り
ヒント: まず、データ内の欠損値を含む列のリストを見つけます。次に、drop() メソッドを使用して、トレーニングデータと検証データの両方からこれらの列を削除します。
![](https://storage.googleapis.com/zenn-user-upload/1362642d8706-20260121.png)

このアプローチの MAE を取得するには、次のコード セルを変更せずに実行します。
![](https://storage.googleapis.com/zenn-user-upload/1d95c775d6e0-20260121.png)

### ステップ 3: 補完（Imputation）
#### パート A
次のコードセルを使用して、各列の平均値（mean value）で欠損値を補完してください。前処理済みのデータフレームの名前は imputed_X_train および imputed_X_valid としてください。その際、列名が元の X_train および X_valid と一致していることを確認してください。

Check: スターターコードを更新すると、check() でコードが正しいかどうかを確認できます。imputed_X_train、imputed_X_valid という変数を作成するコードを更新する必要があります。

##### ヒントと答えは以下の通り
ヒント: まず、SimpleImputer() クラスのインスタンスを定義します。次に、このimputerを使用してトレーニングデータをフィッティングおよび変換し、検証データを変換します。元のデータフレーム X_train と X_valid から元の列名を取得します。
![](https://storage.googleapis.com/zenn-user-upload/4fc1be4c67b2-20260121.png)
このアプローチの MAE を取得するには、次のコード セルを変更せずに実行します。
![](https://storage.googleapis.com/zenn-user-upload/ea31b4db3805-20260121.png)

#### パートB
それぞれのアプローチのMAEを比較してください。結果に何か意外な点はありますか？あるアプローチが他のアプローチよりも優れていると思う理由は何ですか？

![](https://storage.googleapis.com/zenn-user-upload/01a1914526eb-20260121.png)
データセット内の欠損値が非常に少ないことを踏まえれば、列を丸ごと削除するよりも、補完（imputation）を行う方が良い結果になると予想されます。しかし、実際には列を削除した方がわずかにパフォーマンスが良いという結果になりました！

これにはデータセット内のノイズも影響していると考えられますが、もう一つの可能性として、『補完方法がこのデータセットにうまく適合していない』という説明が成り立ちます。つまり、平均値（mean）で埋める代わりに、すべての欠損値を『0』にしたり、最も頻繁に現れる値（最頻値）で埋めたり、あるいは別の方法を使う方が理にかなっているのかもしれない、ということです。

例えば、GarageYrBlt 列（ガレージが建てられた年）を見てみましょう。欠損値がある場合、それは『その家にはガレージがない』ということを示している可能性があります。この場合、各列の中央値（median）で埋めるのが適切でしょうか？それとも、各列の最小値で埋めた方が良い結果が得られるでしょうか？

何がベストかは一概には言えませんが、即座に除外できる選択肢もあります。例えば、この（建設年の）列の欠損値を『0』に設定すると、おそらく悲惨な結果を招くことになるでしょう！
ステップ 4: テストデータの予測値を生成する

#### ステップ 4: テストデータの予測値を生成する
この最終ステップでは、欠損値を処理するために好きな手法を選んで実行します。学習用と検証用の特徴量を前処理したら、ランダムフォレスト・モデルを学習させ、評価を行います。その後、コンペティションに提出するための予測値を生成する前に、テストデータに対しても前処理を行ってください！

#### パート A
次のコードセルを使用して、学習データと検証データを前処理してください。前処理済みのデータフレームの名前は final_X_train および final_X_valid としてください。ここではどのような手法を使っても構いません！ このステップが「正解（Correct）」と判定されるためには、以下の条件を満たしている必要があります：

- 前処理済みの各データフレーム（学習用・検証用）の列数が同じであること。
- 前処理済みのデータフレームに欠損値が含まれていないこと。
- final_X_train と y_train の行数が一致していること。
- final_X_valid と y_valid の行数が一致していること。
![](https://storage.googleapis.com/zenn-user-upload/3c21a2f9069e-20260121.png)

次のコードセルを実行して、ランダムフォレストモデルをトレーニングおよび評価します。（上記の score_dataset() 関数は使用しないことに注意してください。これは、トレーニング済みのモデルを使用してすぐにテスト予測を生成するためです。）
![](https://storage.googleapis.com/zenn-user-upload/1d729388ad1a-20260121.png)

#### パート B
次のコードセルを使用して、テストデータを前処理してください。このとき、必ず学習データや検証データに行った前処理と同じ方法を用いるようにしてください。前処理済みのテスト用特徴量は final_X_test という変数名に設定してください。

その後、その前処理済みデータと学習済みモデルを使って、テストデータの予測値を生成し、preds_test に代入してください。

このステップが「正解（Correct）」と判定されるためには、以下の条件を満たしている必要があります：

- 前処理済みのテスト用データフレーム（final_X_test）に欠損値が含まれていないこと。
- final_X_test の行数が、元の X_test の行数と一致していること。
![](https://storage.googleapis.com/zenn-user-upload/3e504e4ca126-20260121.png)

##### ヒントと答えは以下の通り
![](https://storage.googleapis.com/zenn-user-upload/fc74b44768e0-20260121.png)

次のコード セルを変更せずに実行すると、結果が CSV ファイルに保存され、コンテストに直接送信できるようになります。

![](https://storage.googleapis.com/zenn-user-upload/7e3dd0af4420-20260121.png)

#### 結果を提出する
ステップ4を無事完了したら、リーダーボード（順位表）に結果を提出する準備は万端です！（提出方法は前回の演習でも学びましたが、もしやり方を再確認したい場合は、以下の手順に従ってください。）

まず、まだ参加していない場合はコンペティションに参加する必要があります。こちらの[リンク](https://www.kaggle.com/c/home-data-for-ml-course)をクリックして新しいウィンドウを開き、**「Join Competition（コンペに参加）」**ボタンをクリックしてください。
![join competition image](https://storage.googleapis.com/kaggle-media/learn/images/wLmFtH3.png)
次に、以下の手順に従ってください：
画面右上の 「Save Version」 ボタンをクリックします。ポップアップウィンドウが表示されます。
「Save and Run All」 オプションが選択されていることを確認し、「Save」 ボタンをクリックしてください。
すると、ノートブックの左下にウィンドウが表示されます。実行が完了したら、「Save Version」ボタンの右側にある数字をクリックしてください。画面右側にバージョンの一覧が表示されます。
最新バージョンの右側にある三点リーダー（…）をクリックし、「Open in Viewer」 を選択します。これで閲覧モードに切り替わります（この説明箇所に戻るには、下にスクロールする必要があります）。
画面上部にある 「Data」 タブをクリックします。提出したいファイルを選択し、「Submit」 ボタンをクリックして結果をリーダーボードに提出してください。
これでコンペティションへの提出は完了です！

さらにスコアを伸ばしたい場合は、画面右上の 「Edit」 ボタンを選択してください。コードを変更して、同じ手順を繰り返すことができます。改善の余地はたくさんあります。取り組めば取り組むほど、リーダーボードの順位を上げることができるはずです！

次の章[3.カテゴリ変数](https://zenn.dev/rg687076/articles/ca86ff28e6a56f)へ