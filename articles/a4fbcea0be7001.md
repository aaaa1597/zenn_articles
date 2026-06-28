---
title: "①[Blender5.0]3DCGアルファベットアニメ制作をスクリプト化するまでの道のり(Blender基礎知識編)"
emoji: "😊"
type: "tech"
topics:
  - "blender5"
published: true
published_at: "2026-01-03 10:24"
---

目次
①[Blender基礎知識編](https://zenn.dev/rg687076/articles/a4fbcea0be7001)
②[3Dモデル(文字"T")生成編](https://zenn.dev/rg687076/articles/1e79cd7474040f)
③[リギング編](https://zenn.dev/rg687076/articles/3c9f88f6aa0b20)
④[IK編](https://zenn.dev/rg687076/articles/f90524cf42b912)

# 3DCGアルファベットアニメ制作をスクリプト化するまでの道のり
アルファベットアニメってそんな言葉ないんだけど...。Pixerのロゴアニメみたいなやつのこと。

# ①基礎知識編

## Abstract
- 何をしたいのかの説明
- Blernder5.0の基礎知識まとめ

## 背景(何をしたいのか)
Pixerのロゴアニメみたいな、アルファベットアニメをあまり手をかけずにサクッと作れるようになりたい。そうだChatGPTという味方がいるじゃん。って思ってコマンド投入するんだけどできるアニメはどうにも単調というかなんか有機的な動きになってくれんのよね～。Copilot君ならと思って試したんだけどやっぱり変わらず。。。
求める出力は、Blenderのpythonスクリプト。だけどそのコードがいまいち理解できなくって、やっぱBlederの操作手順を理解せんとだめみたい。
ということで、まずはBleder操作でアルファベットアニメを作成する手順をまとめていきます。

## 前提
- Blender5.0のインストール

## 画面構成
標準の画面レイアウト
![](https://storage.googleapis.com/zenn-user-upload/9be964bbed8c-20251231.png)
- トップバー
- ステータスバー
- 3Dビューポート
- タイムライン
- アウトライナー
- プロパティ

##### ※初期レイアウトに戻したくなった時
ファイル → デフォルト →初期設定を読込む
![](https://storage.googleapis.com/zenn-user-upload/0723d9af8259-20251231.png =500x)

### ◇ワークスペース
![](https://storage.googleapis.com/zenn-user-upload/a6a5e5f277b7-20251231.png)

### ◇プロパティパネル
![](https://storage.googleapis.com/zenn-user-upload/02aa12837dcf-20251231.png =300x)

### ◇3Dビューポートのシェーディング表示切り替え
![](https://storage.googleapis.com/zenn-user-upload/eb3d98b6ce97-20251231.png)

### ◇3DビューポートでNキー押下
サイドバーが表示/非表示になる。
![](https://storage.googleapis.com/zenn-user-upload/4ef66c959c5e-20251231.png)

### ◇Blenderのシーン
"舞台"の単位。つまりはクリエータの考えで分割される。
シーンには、だいたい次のものが入る。
- オブジェクト（Mesh / Armature / Camera / Light など）
- コレクション
- ワールド（背景色・HDRI）
- タイムライン（フレーム範囲）
- レンダー設定
- アニメーション（キー）

※「この空間ををどう見せるか」の全部セットと思ってOK。
こんな感じで追加可能
![](https://storage.googleapis.com/zenn-user-upload/70c93e0bed52-20251231.png =400x)