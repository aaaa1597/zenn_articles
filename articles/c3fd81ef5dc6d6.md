---
title: "[環境構築]CodeSandboxにVSCode+React+TypeScriptの開発環境を構築してみた。"
emoji: "📚"
type: "tech"
topics:
  - "react"
  - "typescript"
  - "codesandbox"
published: true
published_at: "2024-01-02 21:44"
---

# Abstract
CodeSandboxというサービスがあって、Web上でReactの開発環境が作れるらしい。
で、zennに埋め込めれるらしい。
なるだけ簡素に書いてみた。

# 注意点
![](https://storage.googleapis.com/zenn-user-upload/336d9c201e4c-20240102.png)
今回はGithubリポジトリから始めたけど、この場合、zennに埋込みできないというのが残念。
zennに埋込みする場合は、一番右の"Create a Sandbox"を選ぶ必要がある。でもSandboxを選ぶと機能制限が激しい。

- "Import reppository", "Create a Devbox", "Create a Sandbox"の違い。

| | 開始方法 | Terminal | モード移行 | zenn埋込み |
| ---- | ---- | ---- | ---- | ---- |
| Import reppository | リポジトリ読込みから開始 | 使える | 出来ない | 出来ない |
| Create a Devbox | メニューから | 使える | 出来ない | 出来ない |
| Create a Sandbox | メニューから | 使えない | Devboxモードに移行できる | 出来る |

# 前提
- githubのアカウントは取得しとくこと。
- 開始するプロジェクトのgithubリポジトリを作っておく。
※今回は[ココ](https://github.com/aaaa1597/react-r3f-tutorial020.git)のgithubから開始する。


# 手順1.CodeSandbox公式にアクセスする。 
[ここ](https://codesandbox.io/)
![](https://storage.googleapis.com/zenn-user-upload/9f832d29ffbb-20240102.png)

# 手順2.githubアカウントでログイン。 
サインイン。
![](https://storage.googleapis.com/zenn-user-upload/7dad2b09e526-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/597d2a25e8cc-20240102.png)
　↓
適当に入力。
Username: aaaa1597_aaaa ※aaaa1597だと怒られた。だれか使ってるらしい。
Displayname: aaaa1597
What best describes your role?: Full-Stack Developer
How do you plan to use CodeSandbox?: Personal
で、Create accountを押下
![](https://storage.googleapis.com/zenn-user-upload/2a4097db7ac1-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/0e27aebc159d-20240102.png)
ログイン出来た。

# 手順3.githubから開始する。
![](https://storage.googleapis.com/zenn-user-upload/83e3ab07a35b-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/da5a738cde20-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/67185d74fa7e-20240102.png)
　↓
Githubのパスワードを入力 -> Confirm押下
![](https://storage.googleapis.com/zenn-user-upload/bbc5ee6ca1fb-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/2fe40ebed21e-20240102.png)
　↓
読み込み中
![](https://storage.googleapis.com/zenn-user-upload/0f19e26b095c-20240102.png)
　↓
Configure microVM environmentを下図のように。Featuresは無視。
![](https://storage.googleapis.com/zenn-user-upload/f9ff6e9d5bae-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/d5329193da88-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/813901e43b78-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/f8bfc6115ffc-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/2bb106dc4e13-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/e3a66d8756ad-20240102.png)
　↓
セットアップが動き出す。
![](https://storage.googleapis.com/zenn-user-upload/7060c623106e-20240102.png)
　↓
依存ライブラリもインストール。
![](https://storage.googleapis.com/zenn-user-upload/5169d29b8208-20240102.png)
　↓
依存ライブラリもインストール。
![](https://storage.googleapis.com/zenn-user-upload/7edd18d55f93-20240102.png)
　↓
![](https://storage.googleapis.com/zenn-user-upload/10fb08cb1d4d-20240102.png)
出来た。

# 手順4.zennに埋め込む
出来なかった。
理由は、Githubリポジトリの取込みで開始したから。
埋め込みは、"https://codesandbox.io/embed/"　から始まるURLじゃないとだめらしい。
だけど、githubから取込むと"https://codesandbox.io/p/github/" から始まるURLになるので、だめだった。
zennの仕様ならどうしようもないね。orz.