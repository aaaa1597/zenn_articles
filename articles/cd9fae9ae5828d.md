---
title: "[Blender/tips/アニメーション]Mixamoで簡単アニメーション"
emoji: "👓"
type: "tech"
topics:
  - "blender"
  - "animation"
  - "mixamo"
published: true
published_at: "2023-10-21 09:39"
---

[Mixamo](https://www.mixamo.com/#/)ってサイトがあります。
無料で、キャラクターに動きをつかることができます。
今回は、この機能を使って、簡単にBlenderで、人物のアニメーションをしてしまいます。
※Adobeがやってんだ。へぇ～。

# Abstract
MixamoからDL → Blenderで加工 → 出来上がりの方法を書いてます。
どうせなので、複数のアニメーションをくっつけたいので、その方法も一緒に。

1. Mixamoからアニメーションつきモデル(複数)をダウンロード
1. Blenderで取り込む
2. アニメーションをくっつける。
3. 出来上がり。

# 1.Mixamoからアニメーションつきモデル(複数)をダウンロード
[Mixamo](https://www.mixamo.com/#/)のサイトからさっさとDLしてしまいましょう。
メンバ登録が必要ですので、メールアドレスの準備を忘れずに。
あまり、説明が要らないほど、分かりやすいサイトですが、一つだけ。
モデルを選んだら、モーションデータを選択して、ダウンロードします。
※モデルだけ選んでダウンロードしたら、Tポーズのままだった...
![](https://storage.googleapis.com/zenn-user-upload/42acc8ad058e-20221127.png)

# 2.Blenderで取り込む
#### 2.1. デフォルトの光源とカメラと立方体を全部選択 → 削除する。
#### 2.2. ファイル → インポート → FBX → ダウンロードしたモデルのうち取り込むものを全部選択してFBXをインポート押下。
![](https://storage.googleapis.com/zenn-user-upload/ae2a919b6b9e-20221127.png)

&emsp;&emsp;&emsp;&emsp; ↓

![](https://storage.googleapis.com/zenn-user-upload/2efe1417798d-20221127.png)
#### 2.3. 無事取り込まれた。モデルが小さい分は、適当に大きくしておく。

# 3. アニメーションを繋げる。
#### 3.1. モデル名とアニメーション名を変更しとく。
##### 3.1.1. マウスホイールと、shift+マウスmoveで、空間自体の大きさと位置を変更して、モデル全部を適当な大きさと位置にしておく。
![](https://storage.googleapis.com/zenn-user-upload/3b7e2774f510-20221127.png)

##### 3.1.2. スペースキー押下で、モデルが動き出すので、もう一回押下して適当なところで止める。
![](https://storage.googleapis.com/zenn-user-upload/06122b0eaf05-20221127.png)

##### 3.1.3. モデルから突き出た角みたいのをクリックして選択 → F2押下でモデル名とアニメーション名を変更しておく。
![](https://storage.googleapis.com/zenn-user-upload/abef9166f61e-20221127.png)

##### 3.1.4. 他のモデルも同様に変更しとく。
![](https://storage.googleapis.com/zenn-user-upload/70f5d75260da-20221127.png)

#### 3.2. タイムライン画面を見やすくするために、上に広げる。

#### 3.3. エディタタイプをノンリニアアニメーション(NLAエディタ)にする
![](https://storage.googleapis.com/zenn-user-upload/74cba243543f-20221127.png)

&emsp;&emsp;&emsp;&emsp; ↓

#### 3.4. この画面に変わる。
![](https://storage.googleapis.com/zenn-user-upload/6d006cdda57c-20221127.png)


#### 3.5. ストリップ化
下図のストリップ化ボタンを押下する。
※ストリップについて ... Blenderでは、動きをアニメーションと呼ぶみたいなんだけど、そのアニメを1まとまりにしたものをストリップって呼んでるらしい。NLAエディタでは、ストリップを操作するので、アニメーションのストリップ化が必要。
![](https://storage.googleapis.com/zenn-user-upload/bb8938b52cb1-20221127.png)

&emsp;&emsp;&emsp;&emsp; ↓

![](https://storage.googleapis.com/zenn-user-upload/08a695bcd540-20221127.png)
これで、ストリップを使ってアニメーションを繋げることが出来るようになる。

#### 3.6. 不要になった実体を削除
ストリップ化したら、実体を一つ残して、他を削除する。
ctrlを押下しながら、モデルと実体を選択 → deleteキーで削除
![](https://storage.googleapis.com/zenn-user-upload/485f0334a18e-20221127.png)

&emsp;&emsp;&emsp;&emsp; ↓ Deleteキー押下で削除

![](https://storage.googleapis.com/zenn-user-upload/a9d7fe9e4bdb-20221127.png)

#### 3.7. ストリップを追加する。
##### 3.7.1. NlaTrackを選択 → トラックを選択の上に追加を押下。
![](https://storage.googleapis.com/zenn-user-upload/b94fe9724dbe-20221127.png)

##### 3.7.2. shift+a押下 → でてきたリストの中から任意のストリップを選択。
![](https://storage.googleapis.com/zenn-user-upload/2834a5e6e8f5-20221127.png)

##### 3.7.3. こんな感じ。
この時点で、プレイヘッドを右左に動かすと、アニメーションするはずなんだけど...。
動かず、orz
気にせず、次へ。
![](https://storage.googleapis.com/zenn-user-upload/a4758aa9995d-20221127.png)

プレイヘッドってのは、↓これを言うらしい。
![](https://storage.googleapis.com/zenn-user-upload/61fe1afae42c-20221127.png)

#### 3.8. ストリップを滑らかに繋げる。
##### 3.8.1. 追加したストリップを後ろに移動。(既存のストリップに少し重なる様に)
![](https://storage.googleapis.com/zenn-user-upload/57000e470613-20221127.png)

##### 3.8.2. サイドバーを表示し、ストリップのブレンドを設定する。
###### 3.8.2.1. サイドバー([n]キー押下で表示されるメニューのこと)を表示させる。
![](https://storage.googleapis.com/zenn-user-upload/c33a2950e0bb-20221127.png)

###### 3.8.2.2. 新たに追加したストリップを選択 → ストリップタブを選択 → ブレンドインに値を設定
![](https://storage.googleapis.com/zenn-user-upload/c45ee0826e7d-20221127.png)
10ぐらい。

###### 3.8.3. この状態で、プレイヘッドを左右に動かすと、上下のストリップに沿って、3Dモデルがアニメーションするようになった。

#### 3.9. もう一方のストリップも同様に繋げる。
NlaTrack001を選択 → "追加" → "トラックを選択の上に追加"を押下
![](https://storage.googleapis.com/zenn-user-upload/e8cc50e6880d-20221127.png)

&emsp;&emsp;&emsp;&emsp; ↓

![](https://storage.googleapis.com/zenn-user-upload/5c3d30eae5fc-20221127.png)

&emsp;&emsp;&emsp;&emsp; ↓

こんな感じ。
![](https://storage.googleapis.com/zenn-user-upload/1498cb0bf44c-20221127.png)

#### 3.10. フレーム範囲が、デフォルトのままだと少ないので広げる。
タイムラインを表示させる → 右上の終了フレームを任意に設定。
![](https://storage.googleapis.com/zenn-user-upload/aac4179825e1-20221127.png)

&emsp;&emsp;&emsp;&emsp; ↓

![](https://storage.googleapis.com/zenn-user-upload/0f54a0496e5d-20221127.png)

# 4. 出来上がり。
スペースキーを押下して、アニメーションを実行させると、作った通りの動きをするようになった。


