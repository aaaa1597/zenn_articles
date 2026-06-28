---
title: "[無料][環境構築][生成AI]Stable Diffusionを動かしてみた。2024.02(ローカルPC)"
emoji: "⛳"
type: "tech"
topics:
  - "無料"
  - "画像生成"
  - "stablediffusion"
  - "生成ai"
published: true
published_at: "2024-02-24 19:41"
---

![](https://storage.googleapis.com/zenn-user-upload/7b46fe2f4088-20240224.png =250x)

# Abstract
意外と手間取ったので。備忘録として残しときます。

# 前提
- windows11 64bit
- Core i7-13700F 2.10 GHz(13th Gen)
- NVIDIA GeForce RTX 4060
- メモリ 32GB
- NVMe SSD(1TB)
- Python 3.10.6 ← 3.12.0をインストールしてたけど、動かんかった。
- gitインストール済

https://github.com/AUTOMATIC1111/stable-diffusion-webui

# 手順
## 1.git clone
SSDドライブの適当なフォルダに、Stable Diffusion WebUI一式をダウンロードする。
```shell:コマンドプロンプト
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
```
## 2.モデルを取得する。
[Civitai](https://civitai.com/)からモデルを取得する。
モデルは、出来上がりの画像のテイストを左右するものになる。アニメ風だったり、実写だったり。

モデルをクリック
![](https://storage.googleapis.com/zenn-user-upload/bc7d75ded902-20240224.png)
↓
初回はログイン関連であれこれする必要があったけど、覚えてない。。。
![](https://storage.googleapis.com/zenn-user-upload/61e466be0cfd-20240224.png)
今回は、実写風美少女を選んだ。6GBだって。デカっ！！。
↓
gitでcloneしたディレクトリ\stable-diffusion-webui\models\Stable-diffusion配下にコピーする。
![](https://storage.googleapis.com/zenn-user-upload/be87799f431c-20240224.png)

## 3.Stable Dffusion起動
gitでcloneしたディレクトリ\stable-diffusion-webui\配下にwebui-user.batがあるのでダブルクリック。初回起動は10分ぐらいかかった。
![](https://storage.googleapis.com/zenn-user-upload/7b92045a8f2f-20240224.png)
↓
![](https://storage.googleapis.com/zenn-user-upload/751fb32742ac-20240224.png)
起動成功

## 4.画像生成してみる。
2.で取得したモデルを選択する。
![](https://storage.googleapis.com/zenn-user-upload/137b206f165b-20240224.png)
↓
Stable Diffusion Web UIは日本語しか対応してないので、英語で入力する。
:::message
[参考翻訳サイト:DeepL翻訳](https://www.deepl.com/translator)
:::

「beautiful women」を入力して、Generate押下
![](https://storage.googleapis.com/zenn-user-upload/74adc164ac1f-20240224.png)
↓
![](https://storage.googleapis.com/zenn-user-upload/7b46fe2f4088-20240224.png)
出来た!!
分かれば意外と簡単。

# パラメータの説明
#### Sampling method
ノイズを除去する方法。Stable Diffusionの画像生成は、そもそも一旦ノイズまみれにしてから、ノイズを除去して画像生成するらしい。
#### Sampling steps
ノイズを除去する回数。回数を大きくするとより精細画像になるけど遅くなる。
#### Width・Height
生成画像の解像度。
#### CFG Scale
呪文と一致させるか否か。大きければ一致しやすく、小さければ異なるようになる。7が推奨値。
#### Seed
一度生成されると付与される番号みたいの。この番号を指定すると、同じ人が再現しやすくなる。

# 呪文の参考
:::message
[参考呪文サイト:PixAI](https://pixai.art/)
:::
