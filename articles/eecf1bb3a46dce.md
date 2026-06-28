---
title: "ESP32-Azure IoT Kitを動かしてみる。"
emoji: "🐙"
type: "tech"
topics:
  - "c"
  - "入門"
  - "組込み"
  - "esp"
published: true
published_at: "2022-10-04 08:59"
---

[前回の記事](https://zenn.dev/articles/d2e8264e8483b9/edit)で、ESP32のサンプルコードをビルドして実行したんですけども、
ESP32-Azure IoT Kitを動かすことが、本命でした。
なので、動かしてみます。

~~英語が分かる方は、下記の公式ページを参考すればいいかと。~~
~~[ESP32-Azure IoT Kit ソフト編](https://matsujirushi.hatenablog.jp/entry/2020/01/31/212406)~~
~~[Easily Connect Espressif Devices to Azure IoT](https://techcommunity.microsoft.com/t5/internet-of-things-blog/easily-connect-espressif-devices-to-azure-iot/ba-p/1121262)~~
~~[ESP-Samples
](https://github.com/Azure-Samples/ESP-Samples/blob/master/README.md)~~
↑これらを参考にやってみたけど、ビルド通らず。

https://github.com/espressif/esp-azure.git
↑これが参考になった。あざっす。

## 準備
- ESP-IDFの開発環境は、構築済。まだの方は、[前回](https://zenn.dev/articles/d2e8264e8483b9/edit)を参考に構築してください。
- AuzreIoTでデバイスID発行済
- EPS32のボード
- micro USBケーブル
- windows開発マシン

## 手順1 ESD-IDFの起動
前回のインストールで、デスクトップにショートカットが出来ているので、それをダブルクリック。
このショートカットの実体は、下記になっていたので、コマンドプロンプトから実行するのでも問題ないはず。(試してないです。)
cmd.exe /k "D:\esp32\.espressif\idf_cmd_init.bat" esp-idf-ffc6ca2653456527240114458875c165"
※esp-idf-ffc6ca2653456527240114458875c165の部分は、esp_idf.jsonの中身の部分と同じなので、それと同じ文字列を設定すればいいみたいです。
![](https://storage.googleapis.com/zenn-user-upload/10d244665e06-20211208.png)
※esp_idf.jsonの場所は、D:\esp32\\.espressif\esp_idf.json

## 手順2 サンプルコードを取得
Espressif Systems社のサンプルコードを使います。
```上記のコマンドプロンプトで。
d:
mkdir D:\esp32\ph3
cd D:\esp32\ph3
git clone https://github.com/espressif/esp-azure.git
```

## 手順3 サンプルコードの場所に移動
```コマンドプロンプト
cd D:\esp32\ph3\esp-azure\examples\iothub_client_sample_mqtt
```

## 手順4 設定情報を入力
```コマンドプロンプト
idf.py menuconfig
```
上記コマンド実行後、下記を入力します。
- Wi-Fi SSID
- Wi-Fi パスワード
- Azure IoT Hubのデバイス接続文字列 ※接続文字列は、Azure IoTで設定して下さい。
![](https://storage.googleapis.com/zenn-user-upload/06b2afd88213-20211209.png)
※SSIDは、802.11b/g/nのいづれかで繋がるLANを設定しましょう。802.11a/acには繋がりませんでした。
設定したら、Qボタンで抜けましょう。

## 手順5 プロジェクトのビルド
ビルドします。
```
idf.py build
```

## 手順6 ESP32へ書き込む
下記コマンドで書き込みます。
※COMポートは、デバイスマネージャで確認。
```
idf.py -p COM3 flash
```
コマンド実行すると、下記の様に"Connecting........_____."が、伸びてくる。
```
[1/2] cmd.exe /C "cd /D D:\esp32\esp\esp-idf\components\es...2/esp/esp-idf/components/esptool_py/run_serial_tool.cmake"
esptool.py esp32 -p COM3 -b 460800 --before=default_reset --after=hard_reset write_flash --flash_mode dio --flash_freq 40m --flash_size 2MB 0x8000 partition_table/partition-table.bin 0x1000 bootloader/bootloader.bin 0x10000 iothub_client_sample_mqtt.bin
esptool.py v3.1-dev
Serial port COM3
Connecting........_____.
```
で、下図のボタンを書込みが始まるまで、ずっと長押し。
![](https://storage.googleapis.com/zenn-user-upload/1e63ae06525c-20211209.png)

これで、書き込み成功。
できた。

## 動作確認
コマンドプロンプトから下記コマンドを実行する。
```
idf.py -p COM3 monitor
```
↓何かしら、表示されるのが確認できた。
![](https://storage.googleapis.com/zenn-user-upload/f06ca2b2866b-20211209.png)

ソースの詳細は、次の機会に。
