---
title: "[AI]Chat-GPTでOpenCVのコードを書かせてみた。(C++編)"
emoji: "💡"
type: "tech"
topics:
  - "ai"
  - "cpp"
  - "opencv"
  - "chatgpt"
published: true
published_at: "2023-10-21 23:01"
---

最近話題のChat-GPT、
ソースコードも書かせることができるらしい。
無料版の、Chat-GPT3.5でもかなり使えるらしいので、手っ取り早くコードを生成してみる。

# Abstract
この記事では、
Chat-GPTで、コード生成 -> CMakeLists.txt生成 -> VSCodeで、実行 の手順を書いています。

# 1. 前準備
- Chat-GPTは3.5。
- Chat-GPTはログインしておく。
- C++のビルド環境はこのサイトを参考に。 -> [[環境構築]VirtualBox+Ubuntu20.04+VSCodeで、C++の開発環境を構築する。](https://zenn.dev/rg687076/articles/64c07d8a0750d0)

# 2. コードを生成させる。
- Chat-GPTに下記の文章を打ち込む

``` Chat-GPT入力
OpenCVの実装例を書いてみて。
言語はC++で、コーディングスタイルはIPAので。
処理の説明のコメント分も追加してみて。
画像表示は OpenCV の imshow を使ってね。
```
↑"コメント分"って。コメント文の間違いなのに、ちゃんと追加してくれて...
Chat-GPTすごいね。

　　　Chat-GPTの回答
![](https://storage.googleapis.com/zenn-user-upload/f1072535c587-20231021.png)

すごいね。ちゃんとコードになっているし。
説明のコメントもあってそうだし。

# 3. ビルドの前にプロジェクト生成のためのCMakeLists.txtを生成させる。
- Chat-GPTに下記の文章を打ち込む

``` Chat-GPT入力
実装例を動かしてみたいです。
CMakeLists.txtを作って。
```
　　　Chat-GPTの回答
![](https://storage.googleapis.com/zenn-user-upload/693e6cdebbbd-20231021.png)

すごー。
ビルドの仕方まで教えてくれて。Chat-GPT様々だ。

# 4. ビルド
- もう、Chat-GPTが教えてくれてる手順で実施するだけ。
```sh ビルド
mkdir build
cd build
cmake ..
make
```

で、実行。
```sh 実行
./OpenCVExample
```

実行結果。
![](https://storage.googleapis.com/zenn-user-upload/33e26d8f2a13-20231021.png)
そりゃそうだ。input.jpgってどこにもないから。

# 5. input.jpgを準備する。
↓の画像を、ubuntuの同じフォルダに置く。
![](https://storage.googleapis.com/zenn-user-upload/9d37c415e4f3-20231021.png "=10px")

# 6. 実行
```sh
./OpenCVExample
```

できた。
![](https://storage.googleapis.com/zenn-user-upload/a874dd1f04da-20231021.png)
Chat-GPT、すごー。
ちゃんと動くコードを出してくれとるばい。
