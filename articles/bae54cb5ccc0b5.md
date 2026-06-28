---
title: "[無料]React+TypeScriptなWebアプリを公開してみた。(Github Pagesの初め方)"
emoji: "💬"
type: "tech"
topics:
  - "github"
  - "react"
  - "githubpages"
published: true
published_at: "2023-12-16 20:58"
---

# Abstract
[ココ](https://zenn.dev/rg687076/articles/8806a2e2055c36)で、Githubの"Github Pages"に公開する手順を描いたけど、reactプロジェクトで実施してもうまくできなくって困った。
で、調べたらGithub Pagesって、reactライブラリが簡単だったので、そのまとめ。

# 前提
Reactプロジェクトはgithubにコミットしててね。[ココ](https://github.com/aaaa1597/react-threejs-1ststep)とか参考に。
今回は、フォルダ名(=プロジェクト名)、react-threejs-1ststepに。

# 手順
## 開発ソース側で。
gh-pagesをインストール
```shell:reactプロジェクトで。
$ npm install gh-pages
```

package.jsonを修正
```diff json:package.json
{
- "name": "aaaa",
+ "name": "react-threejs-1ststep",
+ "homepage": "https://aaaa1597.github.io/react-threejs-1ststep",
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
で、出来た!!