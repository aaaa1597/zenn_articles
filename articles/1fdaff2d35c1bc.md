---
title: "git pushでエラー!!Support for password authentication was removed on Au..."
emoji: "🎃"
type: "tech"
topics:
  - "git"
  - "auth"
  - "gitpush"
published: true
published_at: "2023-12-03 15:05"
---

# Abstract(git,ubuntu22.04)
タイトル通りなんだけど、VMに新規でubuntu22.04を設定して、git pushを実行すると下記エラーで失敗してた。
Support for password authentication was removed on August 13, 2021.
WindowsのC:\Users配下の.gitconfig探したり、TortoiseGitの設定探したり、もう小一時間も彷徨って... orz

# 解決方法
## 結論 : githubのアクセストークンを取得して、パスワードに入力する。
## 1. 自分のgithubアカウントにアクセスする。
[https://github.com/xxxx/](https://github.com/xxxx/)
↓
アイコンクリック
![](https://storage.googleapis.com/zenn-user-upload/e1f4de0c6688-20231203.png)
↓
Settings
![](https://storage.googleapis.com/zenn-user-upload/6f4128001c9b-20231203.png)
↓
Developer settings
![](https://storage.googleapis.com/zenn-user-upload/ff45d914b43f-20231203.png)
↓
Personal access tokens
![](https://storage.googleapis.com/zenn-user-upload/6a43ac52991d-20231203.png)
↓
![](https://storage.googleapis.com/zenn-user-upload/e748a4e577dd-20231203.png)
Note: 適当に。わかりやすい名前をつける。
Expiration: 適当に。有効期限を設定する。
チェックにはrepo,admin:repo_hook,delete_repoを選んどく。
Generate tokenを押下
↓
## 2. アクセストークン取得!!
赤丸を押下でコピー
![](https://storage.googleapis.com/zenn-user-upload/c035909e4442-20231203.png)
↓
どっかに保存しとく。
これをgit pushの時のPasswordに設定すれば、pushできた。ふぃ～。

## 毎回ログインIDとパスフレーズ聞かれてめんどい時
```shell
$ git config credential.helper store
```
