---
title: "12-③[AI][Kaggle][python]Kaggle入門(AI倫理入門 3.AIにおけるバイアスの特定)"
emoji: "🐕"
type: "idea"
topics:
  - "ai"
  - "python"
  - "kaggle"
  - "ai倫理入門"
published: true
published_at: "2026-03-13 20:29"
---

[Kaggle入門12(AI倫理入門 1.AI倫理入門)](https://zenn.dev/rg687076/articles/65cc05471a465b)
[Kaggle入門12(AI倫理入門 2.AIのための人間中心設計)](https://zenn.dev/rg687076/articles/dcdc3a9cba9882)
[Kaggle入門12(AI倫理入門 3.AIにおけるバイアスの特定)](https://zenn.dev/rg687076/articles/160201bffebffd)
[Kaggle入門12(AI倫理入門 4.AIの公平性)](https://zenn.dev/rg687076/articles/b3617b7cb2b949)
[Kaggle入門12(AI倫理入門 5.最終回 モデルカード)](https://zenn.dev/rg687076/articles/3201cdc11e0ca3)

← [Kaggle入門11(時系列)](https://zenn.dev/rg687076/articles/2db2ccc95543cf)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle入門13(機械学習の説明可能性)](https://zenn.dev/rg687076/articles/89fe247589e22a) →

## Abstract
- Kaggle「AI倫理入門 の[AIにおけるバイアスの特定](https://www.kaggle.com/code/alexisbcook/identifying-bias-in-ai)」の翻訳と実行方法の解説

## AIにおけるバイアスの特定
バイアスはパイプラインのどの段階でも入り込む可能性があります。有害なテキストを識別するシンプルなモデルを調査してみましょう。

## 理論編
### はじめに(Introduction)
機械学習（ML）は人々の生活を改善する可能性を持っていますが、一方で害をもたらす原因にもなり得ます。MLの応用は、人種、性別、宗教、社会経済的地位 などのカテゴリーに基づいて個人を差別してしまった例があります。

このチュートリアルでは、**バイアス（bias）** について学びます。バイアスとは、MLアプリケーションによって生じる 望ましくない負の結果 を指し、特にそれが特定の集団に不釣り合いに影響する場合を指します。

ここでは、あらゆるMLアプリケーションに影響し得る6種類のバイアス を取り上げます。その後、実践的な演習で実際のシナリオを使い、バイアスを特定する作業を行います。

### バイアスは複雑である
多くのML実務者は「偏ったデータ（biased data）」や「garbage in, garbage out（ゴミを入れればゴミが出る）」という概念をよく知っています。 例えば、反ユダヤ主義的なオンライン会話を含むデータセット（garbage in）でチャットボットを学習させると、そのチャットボットは反ユダヤ主義的な発言をする可能性があります（garbage out）。この例は、重要なタイプのバイアス（後で説明する **歴史的バイアス**）を示しており、認識して対処する必要があります。

しかし、MLアプリケーションが悪影響を受けるのは この方法だけではありません。

データに含まれるバイアスは非常に複雑です。例えば、データが不完全だと **表現バイアス（representation bias）** が生じる可能性があります。これは、ある集団が学習データに十分に含まれていない場合に起こります。例として、顔認識システムを訓練する際、訓練データの多くが 肌の色が明るい人 ばかりだった場合、肌の色が濃い人 に対してはうまく機能しなくなります。さらに、訓練データから生じる第三のバイアスとして **測定バイアス（measurement bias）** もあります。これについては後で説明します。

また、不公平なMLアプリケーションの原因は データのバイアスだけではありません。

* MLモデルの 定義の仕方
* モデルを 他のモデルと比較する方法
* 一般ユーザーが 最終結果を解釈する方法

これらによってもバイアスは生じます。つまり、MLのプロセスのあらゆる段階で害が生じる可能性があるのです。

### 6種類のバイアス
さまざまなバイアスの種類を理解すると、MLプロジェクトの中でそれらを見つけやすくなります。また、共通の用語を持つことで、バイアスをどのように軽減（mitigate）するか について建設的な議論ができます。

ここでは、2020年初頭の[研究論文](https://arxiv.org/pdf/1901.10002)に基づき、6種類のバイアスを紹介します。

---

#### 1. 歴史的バイアス（Historical bias）
**歴史的バイアス**とは、データが生成された社会の状態そのものに問題がある場合に生じます。
> 2020年時点で、Fortune 500企業のCEOのうち女性は [7.4%](https://edition.cnn.com/2020/05/20/us/fortune-500-women-ceos-trnd) に過ぎません。研究によると、女性のCEOやCFOがいる企業は、男性が同じ役職にいる企業より 一般的に[利益率が高い](https://edition.cnn.com/2019/10/16/success/women-ceos-and-cfos-outperform/index.html) ことが示されています。これは、女性が男性より 厳しい採用基準を満たす必要がある可能性 を示唆しています。この問題を解決するために、人間の判断を排除して AIで採用を公平にしよう と考えるかもしれません。
しかし、過去の採用データ を使ってモデルを訓練すると、そのモデルは **同じバイアスを学習してしまう可能性が高い** のです。

#### 2. 表現バイアス（Representation bias）
**表現バイアス**とは、モデルの訓練データが、実際にサービスを受ける人々を十分に代表していない場合に生じます。
> 例えば、スマートフォンアプリから収集したデータは、スマートフォンを所有していない可能性が高い人々 を過小に表します。
[アメリカのデータ](https://www.pewresearch.org/internet/fact-sheet/mobile/#:~:text=The%20vast%20majority%20of%20Americans,range%20of%20other%20information%20devices)を収集する場合、65歳以上の人々 は十分に含まれない可能性があります。もしこのデータを使って 都市の交通システム を設計すると、大きな問題になります。なぜなら、高齢者には アクセシビリティを確保するための重要な[ニーズ](https://www.bloomberg.com/news/articles/2017-08-04/why-aging-americans-need-better-transit) があるからです。

#### 3. 測定バイアス（Measurement bias）
**測定バイアス**とは、データの正確さが集団によって異なる場合に生じます。これは 代理変数（proxy variable） を使う場合によく起こります。代理変数とは、直接測定できない変数の代わりに使う変数 のことです。
> 例えば、ある病院が 重症化リスクの高い患者 を予測するモデルを使用しているとします。このモデルは、過去の診断、薬の情報、人口統計データ などを利用し、医療費の予測 を行います。医療費が高い患者ほどリスクが高いだろう、という考えです。しかし、人種を入力から除外しているにもかかわらず、人種差別的な結果 が見られました。黒人患者が 対象患者として選ばれにくかった のです。その理由は、医療費をリスクの代理変数として使ったことにあります。黒人患者は 医療へのアクセス障壁が大きい、医療システムへの[信頼が低い](https://www.science.org/doi/10.1126/science.aax2342) といった理由から、同じ健康状態でも医療費が低くなる傾向があります。
そのため、リスクは同じでも医療費が低く見えてしまう のです。

#### 4. 集約バイアス（Aggregation bias）
**集約バイアス**とは、異なる集団を不適切にまとめてしまうことで発生します。その結果、どの集団に対しても十分に機能しないモデル、または 多数派の集団にしかうまく機能しないモデル ができてしまいます。(これは多くの場合問題にはなりませんが、これは医療分野で特によく問題になります。)
> 例えば、ヒスパニック系の人々は、非ヒスパニックの白人より 糖尿病や合併症の[発症率が高い](https://diabetesjournals.org/care/article-abstract/31/2/240/25187/Disparities-in-A1C-Levels-Between-Hispanic-and-Non?redirectedFrom=fulltext) とされています。もし糖尿病の診断や管理を行うAIを作る場合、民族を特徴量として含める、民族ごとに別のモデルを作る などして、こうした違いに対応する必要があります。

#### 5. 評価バイアス（Evaluation bias）

**評価バイアス**とは、モデル評価に使用するベンチマークデータが、実際の利用者を代表していない場合に生じます。
> 「[Gender Shades](http://proceedings.mlr.press/v81/buolamwini18a/buolamwini18a.pdf)」という論文では、広く使われている顔分析のベンチマークデータセット IJB-A、Adience の被写体の多くが 肌の色が明るい人（79.6% と 86.2%） であることが明らかになりました。商用の性別分類AIは、このベンチマークでは 最先端の性能 を示しましたが、有色人種に対しては[非常に高いエラー率](http://gendershades.org/overview.html) を示しました。

#### 6. デプロイメントバイアス（Deployment bias）
**デプロイメントバイアス**とは、モデルが想定された使い方と、実際の使われ方が異なる場合に生じます。エンドユーザーが設計者の想定通りに使わなければ、モデルがうまく機能する保証はありません。
> 例えば、刑事司法制度では、犯罪者が再犯する可能性 を予測する[ツール](https://www.technologyreview.com/2019/01/21/137783/algorithms-criminal-justice-ai/)が使われています。しかし、この予測は 裁判官が[刑罰を決定するためのものとして設計](https://onlinelibrary.wiley.com/doi/full/10.1002/bsl.2456)されたものではありません。

ML ワークフローのさまざまな段階で発生するこれらの異なるタイプのバイアスを視覚的に表現:

[![](https://storage.googleapis.com/zenn-user-upload/ab60bfe59681-20260307.png)](https://arxiv.org/pdf/1901.10002)

これらは相互に排他的ではないことに注意してください。つまり、1つのMLシステムが 複数のバイアスを同時に持つ可能性があります。例えば、研究者 Rachel Thomas が[最新の研究公演](https://www.youtube.com/watch?v=1Uyc9SPeYkA&list=PLe6zdIMe5B7IR0oDOobXBDBlYY1eqLYPx&index=11&t=41s)で指摘しているように、ウェアラブルフィットネス機器のMLは次のバイアスを同時に受ける可能性があります。

* **表現バイアス(Representation bias)**
  （訓練データに肌の色が濃い人が含まれていない）

* **測定バイアス(Measurement bias)**
  （測定装置が濃い肌色で性能が低下する）

* **評価バイアス(Evaluation bias)**
  （ベンチマークデータに濃い肌色の人が含まれていない）

## 実践編
チュートリアルでは、6種類の異なるバイアスについて学びました。この演習では、実際のデータを使ってモデルを学習し、バイアスを特定する練習を行います。コーディングに慣れていなくても心配はいりません。この演習は問題なく完了できます！

### 導入(Introduction)
2017年の終わりに、[Civil Comments](https://medium.com/@aja_15265/saying-goodbye-to-civil-comments-41859d3a2b1d) プラットフォームはサービスを終了し、約200万件の公開コメントを長期保存のためのオープンアーカイブとして公開しました。
Jigsaw がこの取り組みを支援し、データに対して包括的なアノテーション（注釈付け）を行いました。2019年には、Kaggle で [Jigsaw Unintended Bias in Toxicity Classification](https://www.kaggle.com/c/jigsaw-unintended-bias-in-toxicity-classification/overview) コンペティションが開催され、世界中のデータサイエンティストが協力して、バイアスを軽減する方法を研究しました。

下のコードセルでは、このコンペティションのデータの一部を読み込みます。私たちは数千件のコメントを扱いますが、toxic（有害）か、not toxic（有害ではない）のどちらかのラベルが付いています。

次のコードセルを実行して開始してください。コードの実行には約30秒かかります。
実行が終了すると、データが正常に読み込まれたことを示すメッセージと、2つのコメント例（1つは有害で、もう1つはそうでない）が出力されます。
```python
# Set up feedback system
from learntools.core import binder
binder.bind(globals())
from learntools.ethics.ex3 import *

import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import CountVectorizer

# Get the same results each time
np.random.seed(0)

# Load the training data
data = pd.read_csv("../input/jigsaw-snapshot/data.csv")
comments = data["comment_text"]
target = (data["target"]>0.7).astype(int)

# Break into training and test sets
comments_train, comments_test, y_train, y_test = train_test_split(comments, target, test_size=0.30, stratify=target)

# Get vocabulary from training data
vectorizer = CountVectorizer()
vectorizer.fit(comments_train)

# Get word counts for training and test sets
X_train = vectorizer.transform(comments_train)
X_test = vectorizer.transform(comments_test)

# Preview the dataset
print("Data successfully loaded!\n")
print("Sample toxic comment:", comments_train.iloc[22])
print("Sample not-toxic comment:", comments_train.iloc[17])
```
Data successfully loaded!
Sample toxic comment: Too dumb to even answer.
Sample not-toxic comment: No they aren't.

---

次のコードセルを変更せずに実行してください。データを使って単純なモデルを学習します。出力には、テストデータに対するモデルの正解率（accuracy）が表示されます。

```python
from sklearn.linear_model import LogisticRegression

# Train a model and evaluate performance on test dataset
classifier = LogisticRegression(max_iter=2000)
classifier.fit(X_train, y_train)
score = classifier.score(X_test, y_test)
print("Accuracy:", score)

# Function to classify any string
def classify_string(string, investigate=False):
    prediction = classifier.predict(vectorizer.transform([string]))[0]
    if prediction == 0:
        print("NOT TOXIC:", string)
    else:
        print("TOXIC:", string)
```
Accuracy: 0.9304755967877966

テストデータのコメントの約93%が正しく分類されています！

#### 1) モデルを試してみる

次のコードセルでは、自分でコメントを書いてモデルに入力できます。そのコメントは toxic（有害） と分類されるでしょうか？

1. コードをそのまま実行して `"I love apples"` というコメントを分類してください。結果は "NOT TOXIC" になるはずです。
2. 次に、別のコメント`"Apples are stupid"`を試してください。このように、
my_comment = "Apples are stupid" の部分だけを変更し、その他のコードは変更しないようにします。
3. リンゴに関係なくてもよいので、いくつかコメントを試してみてください。モデルの挙動は予想通りでしょうか？

![](https://storage.googleapis.com/zenn-user-upload/24bc70459629-20260307.png)
![](https://storage.googleapis.com/zenn-user-upload/3df054895b5f-20260307.png)

コメントのテストが終わったら、次は モデルがどのように判断しているのか を見ていきます。次のコードセルを変更せずに実行してください。

モデルは、約 58,000語それぞれに 係数（coefficient） を割り当てています。係数が高いほどモデルはその単語を より有害（toxic）だと判断する傾向があります。
このコードセルでは 最も有害だと判断されている10個の単語 と それぞれの係数 を表示します。

```python
coefficients = pd.DataFrame({"word": sorted(list(vectorizer.vocabulary_.keys())), "coeff": classifier.coef_[0]})
coefficients.sort_values(by=['coeff']).tail(10)
```
|| word | coeff |
| :--- | :--- | :--- |
|20745| fools | 6.27937434211 |
|34211| moron | 6.33271616844 |
|16844| dumb | 6.35977412907 |
|12907| crap | 6.49023038317 |
|38317| pathetic | 6.55483725850 |
|25850| idiotic | 7.00569249802 |
|49802| stupidity | 7.55392125858 |
|25858| idiots | 8.60266725847 |
|25847| idiot | 8.60585149789 |
|49789| stupid | 9.278305 |

#### 2) 最も有害と判断された単語

上のコードセルに表示された「最も有害と判断された単語」を確認してください。意外な単語はありましたか？本来リストに入るべきではないと思う単語はありましたか？

```python
# Check your answer (Run this code cell to get credit!)
q_2.check()
```
**Solution:** None of the words are surprising. They are all clearly toxic.
どの言葉も驚くようなものではありません。どれも明らかに有害です。

#### 3) さらに詳しく調べる
モデルがコメントをどのように分類しているか、さらに詳しく見ていきます。
1. コードセルをそのまま実行して「私にはクリスチャンの友達がいます」というコメントを分類します。「NOT TOXIC」に分類されたことがわかるはずです。さらに、個々の単語に割り当てられたスコアも確認できます。コメント内のすべての単語が表示されるわけではないことにご注意ください。

2. 次に、「私にはイスラム教徒の友達がいます」というコメントを試してみましょう。これを行うには、「私にはキリスト教徒の友達がいます」という部分だけを変更し、残りのコードはそのままにしておきます。コメントは、以下のように引用符で囲むようにしてください。
```python
new_comment = "I have a muslim friend"
```
3. さらに 2 つのコメントを試してみてください:「私には白人の友達がいます」と「私には黒人の友達がいます」(どちらの場合も、コメントに句読点を追加しないでください)。
4. ぜひさらに多くのコメントを試してみて、モデルがコメントをどのように分類するかを確認してください。
![](https://storage.googleapis.com/zenn-user-upload/1c9f01ee70d9-20260307.png)

![](https://storage.googleapis.com/zenn-user-upload/ea806cc28d91-20260307.png)

![](https://storage.googleapis.com/zenn-user-upload/ab7a3a61b21d-20260307.png)

![](https://storage.googleapis.com/zenn-user-upload/61beb812ee53-20260307.png)

#### 4) バイアスを特定する
モデルにバイアスの兆候は見られますか？上のコードセルで次の結果を確認してください。

* `"I have a christian friend"` と `"I have a muslim friend"` はどのように分類されましたか？

* `"I have a white friend"` と `"I have a black friend"` はどのように分類されましたか？

答えがわかったら、次のコードセルを実行してください。

![](https://storage.googleapis.com/zenn-user-upload/4917ae05ebe4-20260308.png)
「私にはイスラム教徒の友人がいます」というコメントは有害とマークされましたが、「私にはキリスト教徒の友人がいます」というコメントは有害とマークされませんでした。同様に、「私には黒人の友人がいます」というコメントは「有害」とマークされましたが、「私には白人の友人がいます」というコメントは「有害」とマークされませんでした。これらのコメントはどれも「有害」とマークされるべきではありませんが、モデルは一部のアイデンティティを誤って「有害」と関連付けているようです。
これは偏見の兆候です。モデルはキリスト教徒に有利でイスラム教徒に不利な偏見を持っているように思われ、また白人に有利で黒人に不利な偏見を持っているようにも思われます。

#### 5) 理解度テスト
ここでは Jigsaw データから離れて、似た（ただし仮想の）シナリオを考えます。

オンラインコミュニティがイスラム教嫌悪的なため、イスラム教に言及するコメントは他の宗教に言及するコメントよりも有害である可能性が高いことに気づきました。これは、モデルにどのようなバイアスをもたらす可能性がありますか？

質問に回答したら、次のコード セルを実行して正式な回答を確認します。
![](https://storage.googleapis.com/zenn-user-upload/fe5ffa69813c-20260308.png)
イスラム教に言及するコメントは、データ収集元のオンラインコミュニティの欠陥により、有害と分類される可能性が高くなります。これは歴史的なバイアスをもたらす可能性があります。

#### 6) 理解度テスト（その2）
同じシナリオを続けます。
オンラインコメントを toxic かどうか分類するモデルを作っています。

英語以外のコメントを別のツールで英語に翻訳します。そして、すべての投稿を元々英語で表現されたものとみなします。このモデルはどのようなバイアスの影響を受けるでしょうか?

質問に回答したら、次のコード セルを実行して正式な回答を確認します。
![](https://storage.googleapis.com/zenn-user-upload/c125debc0f1a-20260308.png)
コメントを英語に翻訳すると、英語以外のコメントを分類する際に追加の誤差が生じます。英語以外のコメントは完全に翻訳されていないことが多いため、測定バイアスが生じる可能性があります。また、集約バイアスも発生する可能性があります。異なる言語のコメントを異なる方法で処理した場合、モデルはすべての言語で表現されたコメントに対してより良いパフォーマンスを発揮する可能性があります。

#### 7) 理解度テスト（その3）
同じ仮説シナリオを続けて、オンラインコメントを有害であると分類するモデルをトレーニングします。

モデルのトレーニングに使用しているデータセットには、主に英国を拠点とするユーザーからのコメントが含まれています。

モデルをトレーニングした後、主に英国を拠点とするユーザーからのコメントの別のデータセットを使用してそのパフォーマンスを評価すると、素晴らしいパフォーマンスが得られます。オーストラリアに拠点を置く企業に導入したところ、イギリス英語とオーストラリア英語の違いにより、モデルのパフォーマンスが芳しくありませんでした。このモデルはどのようなバイアスの影響を受けますか？イギリス英語とオーストラリア英語の違いによるものです。モデルはどのような種類のバイアスの影響を受けますか？

質問に回答したら、次のコード セルを実行して正式な回答を確認します。
![](https://storage.googleapis.com/zenn-user-upload/503ec7e6516c-20260308.png)
モデルが英国のユーザーからのコメントに基づいて評価され、オーストラリアのユーザーに展開されると、**評価バイアス**と**展開バイアス**が生じます。このモデルはオーストラリアのユーザーにサービスを提供するために構築されましたが、英国に拠点を置くユーザーのデータを使用してトレーニングされたため、**表現バイアス**も生じます。

#### さらに学ぶには(Learn more)
バイアスについてさらに学ぶには、この演習で紹介された [毒性分類における意図しないバイアスのジグソーコンテスト](https://www.kaggle.com/c/jigsaw-unintended-bias-in-toxicity-classification/overview)をチェックしてください。

* Kagglerの[Dieter](https://www.kaggle.com/christofhenkel)が、コンペティションへの応募に必要なデータの前処理とニューラルネットワークのトレーニング方法を解説する2部構成の役立つシリーズを作成しました。[こちら](https://www.kaggle.com/code/christofhenkel/how-to-preprocessing-for-glove-part1-eda)から始めましょう。

* 多くのKagglerが、始めるのに役立つノートブックを作成しています。[コンペティションページ](https://www.kaggle.com/c/jigsaw-unintended-bias-in-toxicity-classification/notebooks?sortBy=voteCount&group=everyone&pageSize=20&competitionId=12500)でご確認ください。

バイアスについて学ぶのに利用できるもう一つのKaggleコンペティションは、[Inclusive Images Challenge](https://www.kaggle.com/c/inclusive-images-challenge)です。このコンペティションについては、[こちら](https://research.google/blog/introducing-the-inclusive-images-competition/)のブログ記事で詳しく読むことができます。このコンペティションは、コンピュータービジョンにおける**評価バイアス**に焦点を当てています。

次の章[4.[AIの公平性](https://zenn.dev/rg687076/articles/b3617b7cb2b949)]へ