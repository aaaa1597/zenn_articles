---
title: "4-①[AI][Kaggle][python]Kaggle入門(中級機械学習 1.初めに)"
emoji: "🌊"
type: "tech"
topics:
  - "ai"
  - "python"
  - "機械学習"
  - "kaggle"
published: true
published_at: "2026-01-19 20:38"
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
Kaggle「地理空間データ分析の[はじめに](https://www.kaggle.com/code/alexisbcook/introduction)」の翻訳と実行方法の解説

## はじめに
このコースに必要な内容を確認してください。

もしあなたに機械学習の基礎知識があり、モデルの精度を素早く向上させる方法を学びたいと考えているなら、まさにこのコースが最適です！このコースでは、以下の手法を習得することで、機械学習の専門性を一気に高めることができます。

## 理論編
### 0. はじめに


- 実世界のデータセットによく見られるデータ型への対処（欠損値、カテゴリ変数）
- 機械学習コードの品質を向上させるためのパイプラインの設計
- モデル検証のための高度なテクニック（交差検証：クロスバリデーション）の利用
- Kaggleコンペティションの入賞で広く使われている最先端モデル（XGBoost）の構築
- データサイエンスにおける一般的かつ重大なミス（リーケージ）の回避

学習を進める中で、新しいトピックごとに実データを用いた実践的な演習を行い、知識を応用していきます。演習では「Housing Prices Competition for Kaggle Learn Users（Kaggle学習者向け住宅価格予測コンペ）」のデータを使用します。屋根の種類、寝室の数、浴室の数など、79種類もの説明変数を使って住宅価格を予測します。このコンペに予測結果を提出し、リーダーボード（順位表）で自分の順位が上がっていくのを確認することで、自身の成長を実感できるはずです。

### 事前条件
これまでに機械学習モデルを構築したことがあり、モデルの検証、未学習（Underfitting）と過学習（Overfitting）、ランダムフォレストといったトピックに馴染みがある方なら、このコースを受ける準備は万端です。

もし機械学習が完全に初めてという方は、まず「[Intro to Machine Learning（機械学習入門）](https://zenn.dev/rg687076/articles/0386269e85da8f)」コースをチェックしてください。このコースに必要な準備がすべて網羅されています。

## 実践編
ウォームアップとして、機械学習の基礎をいくつか確認し、最初の結果を Kaggle コンテストに提出します。
### Setup
以下の質問は、あなたの作業に対するフィードバックを提供します。次のセルを実行してフィードバックシステムを設定してください。
![](https://storage.googleapis.com/zenn-user-upload/09ad1247b49a-20260119.png)

Kaggle Learn ユーザー向けの住宅価格コンペティションのデータを使用して、住宅の (ほぼ) すべての側面を説明する 79 個の説明変数を使用してアイオワ州の住宅価格を予測します。

![Ames Housing dataset image](https://storage.googleapis.com/kaggle-media/learn/images/lTJVG4e.png)

次のコード セルを変更せずに実行して、X_train と X_valid のトレーニング機能と検証機能、および y_train と y_valid の予測ターゲットを読み込みます。y_trainとy_validには予測ターゲットがロードされます。テスト特徴量はX_testにロードされます。(機能と予測ターゲットを確認する必要がある場合は、この短い[チュートリアル](https://zenn.dev/rg687076/articles/6ad3407fece568)をご覧ください。モデル検証については[こちら](https://zenn.dev/rg687076/articles/290ea78580f2b2)をご覧ください。また、これらのトピックをすべて網羅したコース全体をご覧になりたい場合は、[こちら](https://zenn.dev/rg687076/articles/0386269e85da8f)から始めてください。

![](https://storage.googleapis.com/zenn-user-upload/0dd07961162b-20260119.png)
次のセルを使用して、データの最初の数行を出力します。これは、価格予測モデルで使用するデータの概要を把握するのに便利です。

![](https://storage.googleapis.com/zenn-user-upload/9fc4f2fb23d1-20260119.png)
次のコードセルは、5つの異なるランダムフォレストモデルを定義しています。このコードセルを変更せずに実行してください。（ランダムフォレストの詳細については、[こちら](https://zenn.dev/rg687076/articles/30b3d16e6086c9)をご覧ください。）

![](https://storage.googleapis.com/zenn-user-upload/08d81b282b09-20260119.png)
5つのモデルから最適なモデルを選択するために、以下の関数 score_model() を定義します。この関数は、検証セットからの平均絶対誤差（MAE）を返します。最良のモデルは最も低い MAE を実現することを思い出してください。（平均絶対誤差を確認するには、[こちら](https://zenn.dev/rg687076/articles/290ea78580f2b2)をご覧ください。）

コードセルを変更せずに実行します。
![](https://storage.googleapis.com/zenn-user-upload/043af88e85ed-20260119.png)

### ステップ1: 複数のモデルを評価する
上記の結果を基に、以下の行に記入してください。最適なモデルはどれですか？ 回答はmodel_1、model_2、model_3、model_4、model_5のいずれかになります。
![](https://storage.googleapis.com/zenn-user-upload/c1d524813e96-20260119.png)

ヒントと答えは下記です。
![](https://storage.googleapis.com/zenn-user-upload/1853bcc5c1d5-20260119.png)

### ステップ2: テスト予測を生成する
素晴らしいですね。正確なモデルを構成する要素を評価する方法がわかりました。次は、モデリングプロセスを実行し、予測を行う番です。以下の行で、変数名が my_model であるランダムフォレストモデルを作成してください。
![](https://storage.googleapis.com/zenn-user-upload/698cfce29152-20260119.png)

ヒントと答えは下記です。
![](https://storage.googleapis.com/zenn-user-upload/70d0409c8026-20260119.png)

次のコードセルを変更せずに実行してください。このコードはモデルをトレーニングデータと検証データに適合させ、テスト予測を生成してCSVファイルに保存します。これらのテスト予測は、コンテストに直接提出できます。

### 結果を提出する
ステップ2を完了したら、リーダーボードに結果を提出する準備が整いました！まず、まだコンテストに参加していない場合は参加する必要があります。この[リンク](https://www.kaggle.com/c/home-data-for-ml-course)をクリックして新しいウィンドウを開き、「Submit Prediction」ボタンをクリックしてください。(「Submit Prediction」ボタンの代わりに「予想を送信」ボタンが表示される場合は、すでにコンテストに参加しているため、再度参加する必要はありません。)

次に、以下の手順に従います。
1. まず、画面右上の Save Version ボタンをクリックしてください。ポップアップウィンドウが表示されます。
2. Save and Run All が選択されていることを確認し、Save ボタンをクリックします。
3. すると、ノートブックの左下にウィンドウが表示されます。実行が完了したら、Save Version ボタンの右側にある「数字」をクリックしてください。画面の右側にバージョン一覧が表示されます。 最新バージョンの右側にある三点リーダー (...) をクリックし、Open in Viewer を選択します。これで同じページのビューアーモード（閲覧画面）に切り替わります。※この説明箇所に戻るには、下にスクロールする必要があります。
4. 画面上部にある Data タブをクリックします。次に、提出したいファイルを選択し、Submit ボタンをクリックして結果をリーダーボード（順位表）に送信してください。

これでコンペティションへの提出は完了です！

さらにスコアを伸ばしたい場合は、画面右上の Edit ボタンを押してコードを修正し、同じ手順を繰り返してください。改善の余地はたくさんあります。取り組むほどリーダーボードの順位を上げていくことができますよ。

次の章(2.[欠損値](https://zenn.dev/rg687076/articles/c11c4bbbee6db0))へ