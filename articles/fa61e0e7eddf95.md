---
title: "React+TypeScriptでWebAssembly007。今回も純粋C++。OpenCV,ArUcoマーカの姿勢推定してみた。"
emoji: "🔖"
type: "tech"
topics:
  - "cpp"
  - "opencv"
  - "aruco"
published: true
published_at: "2024-01-28 13:33"
---

<- [React+TypeScriptでWebAssembly006](https://zenn.dev/rg687076/articles/ebbcedbd15a070)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[React+TypeScriptでWebAssembly008](https://zenn.dev/rg687076/articles/3908d08ea80871) ->

---

![](https://storage.googleapis.com/zenn-user-upload/b75606943bf2-20240128.png =250x)

# Abstract
[前回](https://zenn.dev/rg687076/articles/b031e166604c87)で、開発環境が構築できたので、さっそくOpenCVのアプリを作ってみた。
最終的にはWebAssemblyにするものの、ひとまず純粋C++アプリで。
作るのはArUcoライブラリで、ArUcoマーカの姿勢推定のサンプル。

# 結論
今回の成果物はココ↓
https://github.com/aaaa1597/WebAssembly007

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
$ ren reacttscppwasm_tmplate ReactTs-WebAsm007
$ cd ReactTs-WebAsm007/cpp
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
準備完了。

# ArUcoマーカの姿勢推定の実装
ここから、ArUcoマーカの姿勢推定を実装する。

## 参考(OpenCV公式のドキュメント)
下記、URLにサンプルコードがあるのでそのまま実行するんだけど、カメラパラメータだけは全然違ったので、[別サイトの情報](https://pongsuke.hatenadiary.jp/entry/2018/04/19/102146)を使わせてもらった。
Pose Estimation
https://docs.opencv.org/4.9.0/d5/dae/tutorial_aruco_detection.html

# 手順
まずArUcoマーカの準備。以下の画像をプリントアウトする。
![](https://storage.googleapis.com/zenn-user-upload/fb7749f22502-20240127.png)

ここから、ArUcoマーカの姿勢推定をコーディングする。
とはいえ、↓ここにサンプルコード、ほぼそのまんま。
https://docs.opencv.org/4.9.0/d5/dae/tutorial_aruco_detection.html

成果物は[ココ](https://github.com/aaaa1597/WebAssembly007)にコミットしたので、そっち参照してもらうということで。

で、実行。
![](https://storage.googleapis.com/zenn-user-upload/b75606943bf2-20240128.png)
出来た!!
けど、正しいのと正しくないのとある。
姿勢推定の性能がいまいちイケてないのが課題。

- デバッグ時のお助け情報1
Matの中身ってそのままだと確認しづらいけど、下記方法で確認しやすくなる。
https://qiita.com/h2suzuki/items/6b40f69bb287799d8bf6

---
<- [React+TypeScriptでWebAssembly006](https://zenn.dev/rg687076/articles/ebbcedbd15a070)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[React+TypeScriptでWebAssembly008](https://zenn.dev/rg687076/articles/3908d08ea80871) ->
