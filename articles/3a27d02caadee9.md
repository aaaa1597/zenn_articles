---
title: "①[Android+Native Vuforia]VuforiaでARの初めの一歩"
emoji: "🦔"
type: "tech"
topics:
  - "android"
  - "ar"
  - "c"
published: true
published_at: "2022-10-21 17:28"
---

[第1回 VuforiaでARの初めの一歩](https://zenn.dev/rg687076/articles/3a27d02caadee9)
[第2回 Vuforiaのサンプルコード(Image Targetのみ)を動かしてみた。](https://zenn.dev/rg687076/articles/80f8ddd22f643f)
[第3回 動画再生を板ポリ上で実行するサンプルコードを作ってみた。](https://zenn.dev/rg687076/articles/dfb295182545ef)
[第4回 Vuforiaで動画再生ARを実装してみた。](https://zenn.dev/rg687076/articles/e382b1224a97dd)
[第5回 Vuforia11.4.4の公式サンプルコードを動かしてみた。](https://zenn.dev/rg687076/articles/1e46e1bd60b2fe)
お薦めしません: [第6回 VuforiaのVuMarkを動かしてみた。](https://zenn.dev/rg687076/articles/a9bbd5d39130a6)
お薦めしません: [第7回 Vuforiaで独自VuMarkを作ってみた。~~**←挑戦中**~~](https://zenn.dev/rg687076/articles/41eee19d340521) **←無理ゲーすぎた**
[第8回 Vuforia(Image Target)を独自マーカーで動かしてみた。](https://zenn.dev/rg687076/articles/13d30795ac5896)
[第9回 Vuforia(Image Target)を独自マーカーの複数検出させてみた。](https://zenn.dev/rg687076/articles/c91e2ac5304f3d)

# 第1回目 VuforiaでARの初めの一歩
# ARって？
今更ARの説明は不要な気がしますが、
簡単に言うと、カメラ画像に何かを表示する技術です。
![](https://storage.googleapis.com/zenn-user-upload/b2a45e2fce39-20220624.png)

# Vuforiaって?
ARを実現するライブラリの一つに、Vuforiaというのがあります。
Qualcomm社が開発したARのライブラリで、現在はPTCって会社に売却している様です。
現在も、ライブラリを使用する分には無料で出来るようです。
当時からいろいろ変わった部分もある様ですが、初めの一歩のとっかかりとしては、あんまり変わって無い様です。
ということで、Vuforiaのライブラリを使ってARを実装してみます。

※Vuforiaには3Dモデルを認識するModel Targetと、画像を認識するImage Targetってのがあって、Image Targetの話になります。

# 目標到達点
![](https://storage.googleapis.com/zenn-user-upload/fd0d9ec59b7e-20220624.png)
・android端末で、公式サンプルの3Dモデルを表示をすること。(Unityとかは使わない)

# 準備するもの
・android端末
・開発用PC(Android Studioのインストールは済ませておく。)
・android端末をつないで、デバッグができる様にしておく。
・[Vuforia SDK](https://developer.vuforia.com/downloads/sdk) ←から"Download for Android"を押下して、vuforia-sdk-android-xx-y-z.zip をダウンロードしておく
・[Vuforia Sample](https://developer.vuforia.com/downloads/samples) ←から"Download for Android"を押下して、vuforia-sample-android-xx-y-z.zip をダウンロードしておく

# 準備
## 準備1
サンプルアプリを動かすために、Vuforiaのアカウントが必要になります。
作成しましょう。

[アカウント作成画面](https://developer.vuforia.com/vui/auth/register)
![](https://storage.googleapis.com/zenn-user-upload/63d4f68b836b-20220624.png =500x)
First Name : 自分の名前
Last Name : 自分の苗字
Company : 個人の場合はparsonalで大丈夫です。
Select Country of Residence : Japan
Email Address : 自分のメールアドレス
Username : 適当に
PasswordとConfirm Password : 適当に
私はロボットではありませんにレ点。
I agree ... にレ点。
I acknowledge ... にレ点。
これで、Create accountボタンが押せるようになるので、押下。

## 準備2
公式のサンプルアプリを動かすために、ライセンスが必要になります。
取得しましょう。
![](https://storage.googleapis.com/zenn-user-upload/a2f541254a0c-20220624.png =500x)
Get Basicボタンを押下。
Basicライセンスは無料ですので、安心して押下してください。
↓
![](https://storage.googleapis.com/zenn-user-upload/84a0a8e3e233-20220624.png =500x)
License Name : 適当に
By checking ... にレ点。
これで、Confirmボタンが押せるようになるので、押下。
↓
![](https://storage.googleapis.com/zenn-user-upload/3add4a5ce43a-20220624.png =500x)
新たにライセンスが生成されました。
それをクリックします。
↓
![](https://storage.googleapis.com/zenn-user-upload/c467a53f2767-20220624.png =500x)
赤丸の部分がライセンスキーになります。これが、手順4で必要になります。
ひとまず、クリックしてコピーしておきます。
準備は以上です。

# 手順
## 1.Vuforia SDKを解凍して、適当なフォルダに配置する。
僕の場合は、下記に配置しました。
D:\VuforiaTest\vuforia-sdk-android-10-7-2

## 2.Vuforia Sampleを解凍して、適切なフォルダに配置する。
解凍すると vuforia-sample-xx-y-z のフォルダができます。
これをフォルダごと一式、D:\VuforiaTest\vuforia-sdk-android-10-7-2\samples配下に移動します。
フォルダ構成は以下の通り。
![](https://storage.googleapis.com/zenn-user-upload/814ec860e678-20220624.png =500x)

## 3.Android Studioでプロジェクトを開く。
![](https://storage.googleapis.com/zenn-user-upload/975f44ccec4e-20220624.png =500x)
開く押下

![](https://storage.googleapis.com/zenn-user-upload/045aff23fa3e-20220624.png =500x)
上図のAndroidプロジェクトを開く。
読み込みが完了するまで、待つ。

## 4.認証情報を設定した後、ビルドする。
[Vuforiaのライセンス画面](https://developer.vuforia.com/vui/develop/licenses)

![](https://storage.googleapis.com/zenn-user-upload/ca8fccbe4230-20220624.png =500x)
Android Studioで、"AppController.cpp"を開き、licenseKeyに、準備2でコピーしたライセンスを貼り付けます。
そして、ビルド。

もし、この手順を飛ばしたら、アプリ起動時に下記エラーが発生します。
![](https://storage.googleapis.com/zenn-user-upload/b71666998388-20220624.png =300x)


## 5.マーカを印刷する。
マーカは、D:\VuforiaTest\vuforia-sdk-android-10-7-2\samples\vuforia-sample-10-7-2\Media配下にあります。
target_stones_A4.pdfを印刷しましょう。

## 6.動かしてみる。
Android Studioをビルドして、スマホで実行します。
![](https://storage.googleapis.com/zenn-user-upload/f2eb1d320385-20220624.png =500x)
スマホの画面に、上の画面が表示されるので、Image Targetを押下しましょう。
印刷したマーカをカメラで映すと、"目標到達点"で表示している3Dオブジェクトのキャラクタが表示されます。

以上です。
VuforiaでARサンプルを動かす手順でした。
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;「誰かの役に立つと思って」
