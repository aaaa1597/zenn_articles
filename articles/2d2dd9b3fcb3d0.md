---
title: "[ロボット][STEM教育]オープンソースの犬型ロボット「Bittle X」を動かしてみた。2(キャリブレーション編)"
emoji: "👏"
type: "tech"
topics:
  - "ロボット"
  - "stem教育"
  - "bittlex"
  - "pettoi"
published: true
published_at: "2025-08-21 21:55"
---

![](https://storage.googleapis.com/zenn-user-upload/4cdc1e2a57ba-20250802.jpg =300x)

**<<** https://zenn.dev/rg687076/articles/a4d52086255024

# 前ページの続き
組み立て終わったけど、実際に動かすと残念な動きをしていたのをキャリブレーション(校正)して正常動作するようにする。
このページを参考[Introduction | Petoi Doc Center](https://docs.petoi.com/mobile-app/introduction)に設定していくのだけど、また英語orz。
**日本語で**残します。役に立つか知らんけど。

![](https://storage.googleapis.com/zenn-user-upload/5ac723ba3ea8-20250810.png)

# 1.準備
https://docs.petoi.com/desktop-app/introduction
## 1.1.用意するもの
- Windows PCを準備しておく。Macとスマホでも出来るらしいけど、やり方不明。
- [USB Driver](https://www.silabs.com/software-and-tools/usb-to-uart-bridge-vcp-drivers?tab=downloads) ... 下記手順でダウンロードしておく

:::message
**充電の方法**
↓の通りなんだけど、USB挿すところを完全に間違えてて、全然充電できなかったという!!
※基盤の方のUSBに挿したら充電するものを勘違いしてた orz
:::

![](https://docs.petoi.com/~gitbook/image?url=https%3A%2F%2F1565080149-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-MQ6a951Q6Jn1Zzt5Ajr-887967055%252Fuploads%252FkZXI8FMn0fSI8UGJXZEQ%252FBattery%25E5%2585%2585%25E7%2594%25B5.jpg%3Falt%3Dmedia%26token%3D130430a2-a163-4b08-a25a-5f33e52b3e74&width=400&dpr=3&quality=100&sign=5d64334e&sv=2 =300x)

### 1.1.1 USB Driverインストール
1. [Download and Install VCP Drivers](https://www.silabs.com/software-and-tools/usb-to-uart-bridge-vcp-drivers?tab=downloads)にアクセス。

2. 下記赤線クリックしてUSB Driverダウンロード
![](https://storage.googleapis.com/zenn-user-upload/bc27974736de-20250819.png =500x)

3. CP210x_Universal_Windows_Driver.zipがダウンロードされる。→ 適当な場所で解凍
![](https://storage.googleapis.com/zenn-user-upload/98fefd5cb0f5-20250819.png =400x)
*解凍したところ*

4. USBで接続する。
付属のUSBケーブルで「Bittle X」とPCとを繋ぐ。
![](https://storage.googleapis.com/zenn-user-upload/a24b4066adb8-20250819.png =400x)

5. デバイスマネージャ起動
認識に失敗した状態で繋がってる。
![](https://storage.googleapis.com/zenn-user-upload/abcbb2c65ead-20250819.png =400x)

6. 上記図の赤線のデバイスを選択 → 右クリック → ドライバーの更新
![](https://storage.googleapis.com/zenn-user-upload/55096757ea6b-20250819.png =400x)

7. コンピューターを参照してドライバーを検索
![](https://storage.googleapis.com/zenn-user-upload/7d564df9e7bb-20250819.png =400x)

8. 3で解凍したフォルダを選択 → その下のサブフォルダーも検索するにチェック → 次へ
![](https://storage.googleapis.com/zenn-user-upload/4e38dd0f2a64-20250819.png =400x)

9. 正常終了 → 閉じる
![](https://storage.googleapis.com/zenn-user-upload/c700e4dfbb48-20250819.png =400x)

10. 正常に認識するようになった。COM3で認識してる。
![](https://storage.googleapis.com/zenn-user-upload/761afb3d5746-20250819.png =400x)

## 1.2.Petoi Desktop APPのインストール(解凍するだけインストール不要)
[PetoiDesktopApp](https://github.com/PetoiCamp/OpenCat-Quadruped-Robot/releases)にアクセスしてPetoi.Desktop.App1.2.5_Win.zipをダウンロードして適当な場所で解凍(外国のソフトなのでパスに日本語は入れない方が無難)。
![](https://storage.googleapis.com/zenn-user-upload/5d4ba12f3658-20250810.png)

### 1.2.1. 解凍
![](https://storage.googleapis.com/zenn-user-upload/4dd6053e02a6-20250810.png)
*パスには日本語文字を設定しない方がいいと思われ*

![](https://storage.googleapis.com/zenn-user-upload/44622c7d827f-20250810.png)
解凍したフォルダ配下にUI.exe がある。

# 2. キャリブレーション(校正)
校正: 計器類の狂い・精度を、標準器と比べて正すこと。
ま、ゼロ位置を調整すると思ってもらえれば。

公式(英語)のURL:
https://docs.petoi.com/desktop-app/joint-calibrator

## 2.1. Bettle X本体側のバッテリの電源を入れる。
![](https://storage.googleapis.com/zenn-user-upload/8c13194a6e0b-20250810.jpg =300x)

## 2.2. Bettle X本体側にUSBを挿す
![](https://storage.googleapis.com/zenn-user-upload/7cb86e6e414b-20250810.png =300x)

## 2.3. PCにもUSBを挿す
とくにコメントなし

## 2.4. UI.exeを起動
![](https://storage.googleapis.com/zenn-user-upload/44622c7d827f-20250810.png)
解凍したフォルダ配下にUI.exe がある。

## 2.5. Bittle Xを選択
![](https://storage.googleapis.com/zenn-user-upload/aa0394cf644a-20250810.png =300x)
Model → Bittle Xを選択

## 2.6. Joint Calibratorを選択
![](https://storage.googleapis.com/zenn-user-upload/1d7e7fceefb5-20250810.png =300x)

## 2.7. 警告画面が表示される。
Comfirt押下
![](https://storage.googleapis.com/zenn-user-upload/577881de56c1-20250810.png =300x)

※ちなみに本文の内容は、Copilotに聞いたら翻訳してくれた。
「このロボットは関節を回転させるためにバッテリーで電力を供給する必要があります。関節の動きを制御するために非常に重要です！
メインボードの黄色のLEDが点灯するはずです。そうでない場合は、バッテリーを接続し、ロボットの電源を入れるためにボタンを3秒間長押ししてください。」

## 2.8. しばらく待つ(10秒ぐらい)
![](https://storage.googleapis.com/zenn-user-upload/69044e209674-20250810.png)
無事起動した。

:::message
**何かの拍子にCOMポートを認識できない時がある!**
下の画像の様にカウントダウンが始まることがあって、その時は下記手順に沿ってManualでCOMポートを設定する。

## 2.9. Comfirt押下
下記画面が表示された場合は、COMポートを認識できてないので手動で設定する。
![](https://storage.googleapis.com/zenn-user-upload/65b20d3dc271-20250810.png =500x)
Copilotに聞いたら翻訳してくれた。
![](https://storage.googleapis.com/zenn-user-upload/16c1297b168c-20250810.png =500x)

## 2.10. もっかい、Comfirt押下
カウントダウンが始まる。
![](https://storage.googleapis.com/zenn-user-upload/b2bc860d2d8e-20250810.png =300x)

## 2.11. COMポート接続要求をOK
カウントダウン完了後、下記画面がでるのでCOM3を選んでOK。
画像ではCOM1しか出てきてないけど、USB Driverインストールの手順10で、COM3にインストールしたので、COM3を選択する。
※COM3が表示されない場合は、再度、USB Driverのインストールからやり直す。
![](https://storage.googleapis.com/zenn-user-upload/baf3baa35ac5-20250810.png =300x)
※Manual modeのpopupが表示された場合、OK押下→COM3を選んでOKする。
![](https://storage.googleapis.com/zenn-user-upload/35bf92acd218-20250810.png =300x)
![](https://storage.googleapis.com/zenn-user-upload/175e0b387781-20250810.png =300x)

## 1.12. 出た
この画面からキャリブレーションを実施する。
![](https://storage.googleapis.com/zenn-user-upload/69044e209674-20250810.png)
:::

## 3.キャリブレーション状態に入る
Calibrateボタン押下
![](https://storage.googleapis.com/zenn-user-upload/522beb0e5c2a-20250823.png =500x)

下記画像が期待の手脚の位置となる。
![](https://storage.googleapis.com/zenn-user-upload/bb680a50a473-20250823.png =200x)
※大きく位置が異なる場合は、一旦外して付け直す。

あとは、スライドボタンを上下にズラして上記画像の体制に近づける。
(角度15°単位でしか変わらんらしいから、ぴったりにはできない)
![](https://storage.googleapis.com/zenn-user-upload/4691692a4ce9-20250823.png =300x)

## 4. Saveする。
![](https://storage.googleapis.com/zenn-user-upload/f8328d3f4901-20250823.png =300x)
Saveボタン押下

## 5. exe終了。
繋いでたUSBもはずす。
![](https://storage.googleapis.com/zenn-user-upload/753ea1b5bd53-20250823.png =300x)

## 6. キャリブレーション完了。
付属のリモコンを押すと動くはず。
