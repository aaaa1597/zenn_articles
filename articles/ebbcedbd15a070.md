---
title: "React+TypeScriptでWebAssembly006。今回は純粋C++。OpenCV,ArUcoマーカを生成(と検出も)してみた。"
emoji: "💡"
type: "tech"
topics:
  - "cpp"
  - "opencv"
  - "windows"
  - "cmake"
  - "aruco"
published: true
published_at: "2024-01-27 17:19"
---

<- [React+TypeScriptでWebAssembly005](https://zenn.dev/rg687076/articles/b031e166604c87)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[React+TypeScriptでWebAssembly007](https://zenn.dev/rg687076/articles/fa61e0e7eddf95) ->

---

![](https://storage.googleapis.com/zenn-user-upload/955c6baf9be6-20240127.png =250x)

# Abstract
[前回](https://zenn.dev/rg687076/articles/b031e166604c87)で、開発環境が構築できたので、さっそくOpenCVのアプリを作ってみた。
最終的にはWebAssemblyにするものの、ひとまず純粋C++アプリで。
作るのはArUcoライブラリを使ったHelloWorld的なもの。
じゃ、はじめ!!

# 結論
今回の成果物はココ↓
https://github.com/aaaa1597/WebAssembly006

# 前提
- windowsでOpenCV,C++の開発環境構築済み。 [[環境構築]React+TypeScriptでWebAssembly002。WindowsでOpenCV,C++,VSCode,CMake。](https://zenn.dev/rg687076/articles/85b32eb3d86473)
- WebAssemblyのビルド環境も構築済み。[[環境構築]Ubuntu22.04で、WebAssemblyのOpenCV,C++,CMakeの開発環境を構築してみた。](https://zenn.dev/rg687076/articles/49f1d420820500)
- React+Typescriptの開発環境は構築済 [[環境構築]WindowsにVSCode+React+TypeScriptの開発環境を構築してみた。](https://zenn.dev/rg687076/articles/491d01e35cbce7)
- ベースはこれ→[reacttscppwasm_tmplate](https://github.com/aaaa1597/reacttscppwasm_tmplate)。

# WindowsでC++開発。

# 準備
## 1.テンプレートのソースコード一式を取得
```shell: github取得
$ cd D:\Products\React.js
$ git chttps://github.com/aaaa1597/reacttscppwasm_tmplate.git
$ ren reacttscppwasm_tmplate ReactTs-WebAsm006
$ cd ReactTs-WebAsm006/cpp
```

## 2. VSCodeでプロジェクトフォルダを開く
![](https://storage.googleapis.com/zenn-user-upload/a322799dc019-20240127.png)
VSCodeの拡張機能C/C++、CMake Toolsを入れてなければ入れておく。
　　　　　　↓
- C/C++: Edit Configurations(JSON)を選択
Ctrl + Shift + P -> "C/C++: Edi"まで入力と出てくる。
![](https://storage.googleapis.com/zenn-user-upload/6c56d113ac78-20240127.png)
　　　　　　↓
c_cpp_properties.jsonが生成される。ついでに右下にビルド/デバッグ/実行ボタンも表示される。
![](https://storage.googleapis.com/zenn-user-upload/3423f0227f0c-20240127.png)

:::message
右下にビルド/デバッグ/実行ボタンも表示されない場合、
次の手順を実行すると表示されるので問題ない。
:::

## 3. CMakeの設定
Ctrl + Shift + P -> "CMake: Configure"を選択。
X86を選ぶ。(WebAssemblyは32bitなので。)
![](https://storage.googleapis.com/zenn-user-upload/0cf17ea44dc6-20240127.png)
	     ↓
![](https://storage.googleapis.com/zenn-user-upload/34219c0f6578-20240115.png)

## 4. インクルードパスを追加
ifcpp.cppに戻ると、赤波線でエラーになっている。
![](https://storage.googleapis.com/zenn-user-upload/871ecd0ee807-20240127.png)
	     ↓
OpenCVのインストールパスを設定。
D:\apps\opencv\opencv-4.x\build\install\include
![](https://storage.googleapis.com/zenn-user-upload/33c246cd4087-20240127.png)
	     ↓
赤波線が消えている。
![](https://storage.googleapis.com/zenn-user-upload/582ff4d5b003-20240127.png)

## 5. 実行
ブレークポイントを設定して、カブト虫ボタンを押す。
![](https://storage.googleapis.com/zenn-user-upload/51d70202248f-20240127.png)
	     ↓
ちょっと待つと、ブレークポイントで止まる。
![](https://storage.googleapis.com/zenn-user-upload/151198287667-20240127.png)
	     ↓
再開
![](https://storage.googleapis.com/zenn-user-upload/23338ce099b8-20240127.png)
出来た!!
準備完了。

# 手順
ここから、ArUcoマーカを生成をコーディングする。
とはいえ、↓ここにサンプルコードがあるので、ほぼそのまんま。
https://docs.opencv.org/4.x/d5/dae/tutorial_aruco_detection.html

成果物は[ココ](https://github.com/aaaa1597/WebAssembly006)にコミットしたので、そっち参照してもらうということで。

で、実行。
![](https://storage.googleapis.com/zenn-user-upload/955c6baf9be6-20240127.png)
出来た!!
ちょっと分かりにくいけど、生成と検出の処理が動作している。


Pose Estimation
https://docs.opencv.org/4.9.0/d5/dae/tutorial_aruco_detection.html
D:\apps\opencv\opencv_contrib-4.x\modules\aruco\samples\tutorial_camera_params.yml


---
<- [React+TypeScriptでWebAssembly005](https://zenn.dev/rg687076/articles/b031e166604c87)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[React+TypeScriptでWebAssembly007](https://zenn.dev/rg687076/articles/fa61e0e7eddf95) ->
