---
title: "第6回 React+TypeScriptなWebアプリで、QRコードをARしてみた。(リリース編)"
emoji: "👻"
type: "tech"
topics:
  - "react"
  - "typescript"
  - "webアプリ"
  - "3dcg"
  - "qrcode"
published: true
published_at: "2024-02-12 19:37"
---

<- [第5回 React+TypeScriptなWebアプリで、QRコードをARしてみた。(完成編)](https://zenn.dev/rg687076/articles/541c12172d686a)
&emsp;[[第7回]React+TypeScriptなWebアプリで、QRコードをARしてみた。(スマホでデバッグ編)](xxxx) ->

---

![](https://storage.googleapis.com/zenn-user-upload/16c880314210-20240212.png =250x)
# Abstract
React+TypescriptのWebアプリで、ARを実装してみた。第6回。
端が切れるとか、トラッキングが弱すぎとかいろいろあるだろうけど、ひとまず完成した前回のWebアプリを公開する手順。
Github Pagesを使うんだけど、Githubって無償で公開ページを提供してくれてるのでそれを使う。便利な世の中になったもんだ。

# 結論
今回の成果物はココ↓
https://github.com/aaaa1597/ReactTs-QrAr006

# 前提
- React+Typescriptの開発環境は構築済 [[環境構築]WindowsにVSCode+React+TypeScriptの開発環境を構築してみた。](https://zenn.dev/rg687076/articles/491d01e35cbce7)
- 前回プロジェクトから。[ReactTs-QrAr005](https://github.com/aaaa1597/ReactTs-QrAr005)
- Webカメラは準備しておく。
- githubアカウントも作っておく。

# 手順
## 1.プロジェクト生成 -> VSCodeで開く
このテンプレートコードから始める。[ReactTs-QrAr005](https://github.com/aaaa1597/ReactTs-QrAr005)
```shell:git cloneとか、フォルダリネームとか
$ D:
$ cd .\Products\React.js\            # ご自身の適当なフォルダに読み替えてね。
$ rd /q /s D:\Products\React.js\ReactTs-QrAr005
$ git clone https://github.com/aaaa1597/ReactTs-QrAr005.git
$ rd /q /s "ReactTs-QrAr005/.git"
$ ren ReactTs-QrAr005 ReactTs-QrAr006
$ cd ReactTs-QrAr006
```

# 準備
```shell:コマンドプロンプト
$ npm install
```

# 実行してみる
```shell:コマンドプロンプト
$ npm start
```
![](https://storage.googleapis.com/zenn-user-upload/16c880314210-20240212.png =250x)
ここまでで、一旦動作するのを確認する。

# 手順
## 開発ソース側で。
gh-pagesをインストール
```shell:reactプロジェクトで。
$ npm install gh-pages
```

package.jsonを修正
```diff json:package.json
{
+ "name": "ReactTs-QrAr006",
+ "homepage": "https://aaaa1597.github.io/ReactTs-QrAr006",
～略～
  "scripts": {
+   "deploy": "npm run build && gh-pages -d build -m \"Updates --skip-ci\"",
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
～略～
```

package.jsonを修正
```shell:reactプロジェクトで。
$ npm run deploy
```
コミット完了まで待って、もちょっと待つ。

https://aaaa1597.github.io/ReactTs-QrAr006
上記にアクセスする。

![](https://storage.googleapis.com/zenn-user-upload/16c880314210-20240212.png)
出来た!!
QRコードの上に3Dモデル描画が出来ている。
トラッキングが弱いけど。。。

---

<- [第5回 React+TypeScriptなWebアプリで、QRコードをARしてみた。(完成編)](https://zenn.dev/rg687076/articles/541c12172d686a)
&emsp;[[第7回]React+TypeScriptなWebアプリで、QRコードをARしてみた。(スマホでデバッグ編)](xxxx) ->