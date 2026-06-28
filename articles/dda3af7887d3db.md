---
title: "[環境構築]React+TypeScriptでWebAssembly004。WindowsでC++開発->Ubuntuでwasmビルド。"
emoji: "🔖"
type: "tech"
topics:
  - "cpp"
  - "ubuntu"
  - "opencv"
  - "wasm"
  - "windows"
published: true
published_at: "2024-01-21 18:36"
---

<- [React+TypeScriptでWebAssembly003](https://zenn.dev/rg687076/articles/49f1d420820500)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[React+TypeScriptでWebAssembly005](https://zenn.dev/rg687076/articles/b031e166604c87) ->

---

![](https://storage.googleapis.com/zenn-user-upload/c36ad93a42a6-20240120.png =250x)

# Abstract
![](https://storage.googleapis.com/zenn-user-upload/89b16255d277-20240127.png)
開発の主体はVMを使わないWindowsで、WebAssemblyビルドの時にVMのUbuntuを使う開発環境を構築してみた。
ホントは、Windowsだけで完結したかったとけど、OpenCVのWebAssemblyビルドができんかった～。

# Windows編
```shell:バージョン
$ opencv_version
4.9.0-dev
$ cmake --version
cmake version 3.28.1
$ emcc
emccは未インストール
```

# 結論
今回の成果物はココ↓
https://github.com/aaaa1597/cppwasm_template

## 前提
- gitインストール済。[[環境構築]Windowsに、Gitインストール。](https://zenn.dev/rg687076/articles/2bd360c30c389a)
- pythonインストール済。[Windows版Pythonのインストール](https://www.python.jp/install/windows/install.html)
- CMakeインストール済。バージョン問わず。[CMake 3.12.0 インストール手順](https://qiita.com/East_san/items/b8ebc34dad226865899a)
- VisualStudio2022インストール済。[Windows での Visual Studio 2022 Community のインストール方法](https://www602.math.ryukoku.ac.jp/Prog1/turtle-vs2022c.html)
- VSCodeインストール済。[Windows への Visual Studio Code のインストール方法](https://www602.math.ryukoku.ac.jp/Prog1/vscode-win.html)
	VSCodeには、以下の拡張機能を入れておく。
	- C/C++
	- CMake Tools
	- 日本語化もついでに。
- Webカメラを準備。(内臓カメラを使うならそれでもOK)

## Windows側のC++ビルド環境構築
Windowsで。
## OpenCVインストール
### 1.OpenCVのConfigure実行
- opencvインストールフォルダ作成
```shell:opencvフォルダ作成。
$ mkdir d:\apps\opencv && cd d:\apps\opencv
```

- opencvとopencv_contribをダウンロード

```shell:opencvとopencv_contribをダウンロード
$ curl -Lo opencv4.zip https://github.com/opencv/opencv/archive/4.x.zip
$ curl -Lo opencv_contrib4.zip https://github.com/opencv/opencv_contrib/archive/4.x.zip
```

- opencv4.zip,opencv_contrib4.zipを解凍。
```shell:opencvフォルダ確認
$ dir D:\apps\opencv
～略～
2024/01/19  22:53    <DIR>          opencv-4.x
2024/01/20  18:18        97,473,020 opencv4.zip
2024/01/20  05:13    <DIR>          opencv_contrib-4.x
2024/01/20  18:19        62,105,890 opencv_contrib4.zip
～略～
```

- CMake GUI起動
([ココ](https://zenn.dev/rg687076/articles/cea3573e723bc3)見とくと幸せになれます。)
Where is the source code: D:\apps\opencv\opencv-4.x
Where to build the binaries: D:\apps\opencv\opencv-4.x\build
→ Configure押下
Optilnal platform for generator: Win32
Finish押下
![](https://storage.googleapis.com/zenn-user-upload/b230ede11e46-20240115.png)
　　　　　　↓
![](https://storage.googleapis.com/zenn-user-upload/e41825916f1e-20240115.png)
　　　　　　↓
この中から、"OPENCV_EXTRA_MODULE_PATH"を頑張って探して設定。
OPENCV_EXTRA_MODULE_PATH : D:\apps\opencv\opencv_contrib-4.x\modules
// "BUILD_opencv_world"を頑張って探して設定。
// BUILD_opencv_world: レ点
→ Configureをも一回押下
Configuring done
　　　　　　↓
![](https://storage.googleapis.com/zenn-user-upload/0bbf2597dcb1-20240115.png)
Generate押下
　　　　　　↓
![](https://storage.googleapis.com/zenn-user-upload/d93ea5b102c4-20240115.png)
Generating done
　　　　　　↓
閉じる。

### 2. OpenCVをVisual Studio 2022でビルド
OpenCV.sln をダブルクリックで開く。
![](https://storage.googleapis.com/zenn-user-upload/1a90cbcdcfad-20240115.png)
　　　　　　↓
OpenCVの中をデバッグするつもりはなので、ReleaseとWin32を選んで、ALL_BUILDを選ぶ。
![](https://storage.googleapis.com/zenn-user-upload/93a409255b9b-20240115.png)
　　　　　　↓
ビルド。15分ぐらいかな～。
![](https://storage.googleapis.com/zenn-user-upload/6a8cf1726869-20240120.png)
ビルドエラーなし。OpenCVすばらしい～。
　　　　　　↓
INSTALL -> ビルドを選ぶ。
![](https://storage.googleapis.com/zenn-user-upload/08e4bda20c39-20240115.png)
　　　　　　↓
ビルド。
![](https://storage.googleapis.com/zenn-user-upload/d21341bb47f7-20240120.png)
ビルドエラーなし。さすがOpenCV。
　　　　　　↓
ビルド完了。閉じる。

### 3. 環境変数Pathに設定。
以下のパスを環境変数Pathに追加する。
- D:\apps\opencv\opencv-4.x\build\install
- D:\apps\opencv\opencv-4.x\build\install\include
- D:\apps\opencv\opencv-4.x\build\install\x86\vc17\bin
※D:\apps\opencv\opencv-4.xの部分は自身の環境に読み替えてね。

### 4. 再起動
環境変数をいじったのでシステムに反映させるために再起動する。

### 5. プロジェクトフォルダとソース一式を作成
- プロジェクトフォルダ作成
```shell:cppwasm_templateフォルダを作成。
mkdir cppwasm_template && cd cppwasm_template
```
- cppフォルダ作成
```shell:cppフォルダを作成。
mkdir cpp && cd cpp
```

- CMakeLists.txtを作成
```cpp:CMakeLists.txtの中身
cmake_minimum_required(VERSION 3.5)
project(OpenCVExample)

set(CMAKE_CXX_STANDARD 17)

# OpenCVのパッケージを見つける
find_package(OpenCV REQUIRED)

# #OpenCV関係のインクルードディレクトリのパスを設定
# include_directories( ${OpenCV_INCLUDE_DIRS} )

# ソースファイルを指定
set(SOURCES ../src/ifcpp.cpp ../src/MainProcess.cpp)

# 実行可能ファイルをビルド
add_executable(OpenCVExample ${SOURCES})

message('----------------s')
message(${SOURCES})
message(${OpenCV_LIBS})
message('----------------e')

# OpenCVをリンク
target_link_libraries(OpenCVExample ${OpenCV_LIBS})
```
- ifcpp.cppを作成
ifcpp.cppは、Windowsでのみ動作するコードを実装する。

|  |  |
| ---- | ---- |
| ![](https://storage.googleapis.com/zenn-user-upload/7a7a53c4ed91-20240127.png =250x) | ifcpp.cppから<br/>　MainProcess.cpを呼び出す構成 |

```cpp:ifcpp.cppの中身
#include <opencv2/opencv.hpp>
#include "../src/MainProcess.h"

int main(int argc, char *argv[]) {
  /* 画像の読み込み */
  cv::Mat inputImage = cv::imread("../../input.jpg");

  /* 画像が正しく読み込まれたかを確認 */
  if (inputImage.empty()) {
    std::cerr << "画像を読み込めませんでした" << std::endl;
    return -1;
  }

  /* 画像処理 */
  cv::Mat grayscaleImage;
  ConvertColor(inputImage, grayscaleImage);

  /* 処理画像を表示 */
  cv::imshow("Grayscale Image", grayscaleImage);

  /* キー入力待ち */
  cv::waitKey(0);

  /* ウィンドウを閉じる */
  cv::destroyAllWindows();
  return 0;
}
```

```shell:srcフォルダを作成。
cd ..
mkdir src && cd src
```

- MainProcess.hを作成
```cpp:MainProcess.hの中身
#include <opencv2/opencv.hpp>

void ConvertColor(const cv::Mat &inmat, cv::Mat &outmat);
```

- MainProcess.cppを作成
メイン処理をMainProcess.cppに書き、ifcpp.cpp/ifwasm.cppの両方から呼ばれる様に構成する。

|  |  |
| ---- | ---- |
| ![](https://storage.googleapis.com/zenn-user-upload/c26a8148fe16-20240127.png =250x) | MainProcess.cppはifcpp.cpp/ifwasm.cppの両方から呼ばれる。<br/>メイン処理をMainProcess.cppに書く。 |

```cpp:MainProcess.cppの中身
#include <opencv2/opencv.hpp>

void ConvertColor(const cv::Mat &inmat, cv::Mat &outmat) {
  cv::cvtColor(inmat, outmat, cv::COLOR_BGR2GRAY);
  return;
}
```

```dir:フォルダ構成
cppwasm_template
 ├ cpp
 ｜  ├ CMakeLists.txt   # windowsC++開発用のCMakeLists.txt,VSCodeで開く
 ｜  ├ input.jpg        # お試し画像
 ｜  └ ifcpp.cpp        # windows側で動くコード。MainProcess.cppのコードを呼ぶ。
 └ src
     ├ MainProcess.cpp  # メイン処理。windows/weasmの両方から呼ばれる。
     └ MainProcess.h    # メイン処理のヘッダ。
```

### 6. VSCodeでプロジェクトフォルダを開く
![](https://storage.googleapis.com/zenn-user-upload/22b4f5d6e02f-20240121.png)
VSCodeの拡張機能C/C++、CMake Toolsを入れてなければ入れておく。
　　　　　　↓
- C/C++: Edit Configurations(JSON)を選択
Ctrl + Shift + P -> "C/C++: Edi"まで入力と出てくる。
![](https://storage.googleapis.com/zenn-user-upload/ffc3f8959f05-20240114.png)
　　　　　　↓
c_cpp_properties.jsonが生成される。ついでに右下にビルド/デバッグ/実行ボタンも表示される。
![](https://storage.googleapis.com/zenn-user-upload/2ad01be0bb68-20240114.png)

:::message
右下にビルド/デバッグ/実行ボタンも表示されない場合、
手順7を実行すると表示されるので問題ない。
:::

### 7. CMakeの設定
Ctrl + Shift + P -> "CMake: Configure"を選択。
X86を選ぶ。(WebAssemblyは32bitなので。)
![](https://storage.googleapis.com/zenn-user-upload/34219c0f6578-20240115.png)

### 8. インクルードパスを追加
ifcpp.cppに戻ると、赤波線でエラーになっている。
![](https://storage.googleapis.com/zenn-user-upload/548cb647bdb3-20240121.png)
　　　　　　↓
手順3でビルドしたパスを設定。
D:\apps\opencv\opencv-4.x\build\install\include
![](https://storage.googleapis.com/zenn-user-upload/64e850a31763-20240121.png)
　　　　　　↓
赤波線が消えている。
![](https://storage.googleapis.com/zenn-user-upload/71b2f274f390-20240121.png)

### 9. 実行
ブレークポイントを設定して、カブト虫ボタンを押す。
![](https://storage.googleapis.com/zenn-user-upload/23de86e57ae9-20240121.png)
　　　　　　↓
いろいろ動き出す。
![](https://storage.googleapis.com/zenn-user-upload/aaa7c3c91c2c-20240114.png)
　　　　　　↓
ちょっと待つと、ブレークポイントで止まる。
![](https://storage.googleapis.com/zenn-user-upload/42e8153ccf3a-20240121.png)
　　　　　　↓
再開。
![](https://storage.googleapis.com/zenn-user-upload/05423a31982c-20240121.png)
出来た!!

---
---
---

# Ubuntu編
windows側で開発したコードをwasmビルドして、ブラウザで動作確認する。

```shell:バージョン
$ opencv_version
4.9.0
$ cmake --version
cmake version 3.28.20240119-g62ca955
$ emcc(=WebAssembly) --version
emcc ～略～ 3.1.52 
```

## 前提
- VirtualBoxインストール済。 [VirtualBoxインストール](https://qiita.com/HirMtsd/items/be1c8afe708e901e1100)
- VirtualBoxにUbuntu22.04インストール済。[VirtualBox上でUbuntu22.04を構築](https://zenn.dev/rg687076/articles/795f4307117d35)
- Ubuntuにgitインストール済。`sudo apt install -y git`
- Ubuntuにcmakeインストール済。
```shell:cmakeインストール
$ git clone https://github.com/Kitware/CMake.git
$ cd CMake && ./bootstrap && make && sudo make install
```

## Ubuntu側のwasmビルド
### 1.Emscripten のインストール
C++をEmscriptenにビルドするのにEmscriptenが必要。なのでインストールする。
参考 : [Emscripten Download and install](https://emscripten.org/docs/getting_started/downloads.html)
```shell:Emscripten のインストール
cd ~
git clone https://github.com/emscripten-core/emsdk.git # gitから取得
cd emsdk                                               # emsdk移動
git pull                                               # 最新化
./emsdk install latest                                 # インストール
./emsdk activate latest                                # make
# source ./emsdk_env.sh                                # 環境変数設定
echo 'source "/home/jun/emsdk/emsdk_env.sh"' >> $HOME/.bash_profile
 # 環境変数設定の永続化
```
一旦、ログアウト -> ログイン

### 2.OpenCVビルド
```shell:OpenCVビルド
$ mkdir ~/tmp-opencv && cd ~/tmp-opencv
$ mkdir build && cd build
$ git clone https://github.com/opencv/opencv.git --depth 1
### git clone https://github.com/opencv/opencv_contrib.git --depth 1
$ cd opencv
$ python3 ./platforms/js/build_js.py build_wasm --build_wasm --emscripten_dir=../../../emsdk/upstream/emscripten --config_only
### --cmake_option="-DOPENCV_EXTRA_MODULES_PATH=build/opencv_contrib/"
$ export OPENCV_JS_WHITELIST=~/tmp-opencv/build/opencv/platforms/js/opencv_js.config.py
$ cd build_wasm
$ emmake make -j
$ emmake sudo make install
#        ↑↑↑ sudo付けんと file cannot create directory:～のエラーになるけん。
```

### 3.テンプレートのソースコード一式を取得
```shell:git取得
$ cd ~
$ git clone https://github.com/aaaa1597/cppwasm_template.git
$ cd cppwasm_template/wasm
```

- ifwasm.cppを作成
ifwasm.cppは、WebAssembly依存部分を実装する。<br/> JavaScriptからカメラ画像を受け取って、MainProcess.cppに渡す役目とか。<br/> ログをconsole.logに出力する機能とか。<br/> WebAssemblyとしてビルドするのはこっちの構成になる。

|  |  |
| ---- | ---- |
| ![](https://storage.googleapis.com/zenn-user-upload/eb0a6b9b445a-20240127.png =250x) | ifwasm.cppから<br/>　MainProcess.cpを呼び出す構成 |


```shell:wasmフォルダを作成。
$ cd ~/cppwasm_template/
$ mkdir wasm
$ cd ~/cppwasm_template/wasm
```

```cpp:ifwasm.cppの中身
#include <stdlib.h>
#include <SDL/SDL.h>
#include <emscripten.h>
#include <emscripten/bind.h>
#include <opencv2/opencv.hpp>
#include <opencv2/opencv.hpp>
#include "../src/MainProcess.h"

namespace {

constexpr int WIDTH = 640;
constexpr int HEIGHT = 480;
SDL_Surface *screen = nullptr;

} // namespace

#define LOG_OUTPUT 0

#if LOG_OUTPUT
EM_JS(int, console_log, (const char *logstr), {
  console.log('aaaaa ' + UTF8ToString(logstr));
  return 0;
});
#else
#define console_log(logstr)
#endif

extern "C" int main(int argc, char **argv) {
  console_log(__PRETTY_FUNCTION__);
  SDL_Init(SDL_INIT_VIDEO);
  screen = SDL_SetVideoMode(WIDTH, HEIGHT, 32, SDL_SWSURFACE);

  return 0;
}

extern "C" {
size_t EMSCRIPTEN_KEEPALIVE creata_buffer(int size) {
  console_log(__PRETTY_FUNCTION__);
  return (size_t)malloc(size * sizeof(uint8_t));
}

void EMSCRIPTEN_KEEPALIVE destroy_buffer(size_t p) {
  console_log(__PRETTY_FUNCTION__);
  void *pbuf = (void*)p;
  free(pbuf);
}

void EMSCRIPTEN_KEEPALIVE Convert(size_t addr, int width, int height, int cnt) {
  console_log(__PRETTY_FUNCTION__);
  auto data = reinterpret_cast<void *>(addr);
  cv::Mat rgbaMat(height, width, CV_8UC4, data);
  cv::Mat rgbMat;
  cv::Mat rgbOutMat;
  cv::Mat outMat;
  ConvertColor(rgbaMat, rgbMat, cv::COLOR_RGBA2RGB);
  rgbMat.convertTo(rgbOutMat, -1, 1.0, cnt - 100.0);
  ConvertColor(rgbOutMat, outMat, cv::COLOR_RGB2RGBA);

  if (SDL_MUSTLOCK(screen))
    SDL_LockSurface(screen);
  cv::Mat dstRGBAImage(height, width, CV_8UC4, screen->pixels);
  outMat.copyTo(dstRGBAImage);
  if (SDL_MUSTLOCK(screen))
    SDL_UnlockSurface(screen);
  SDL_Flip(screen);
}
} /* extern "C" */

EMSCRIPTEN_BINDINGS(my_module) {
  emscripten::function("convert", &Convert);
  emscripten::function("creatabuffer", &creata_buffer);
  emscripten::function("destroybuffer", &destroy_buffer);
}
```

### 4.ソースコードをビルド
```shell:srcをビルド
$ cd ~/cppwasm_template/wasm
$ mkdir build && cd build
$ emcmake cmake ..
$ emmake make
$ mv cppmain.js ..
$ mv cppmain.wasm ..
```

```shell:ls -l
$ ls -l ~/cppwasm_template/wasm
-rw-rw-r-- 1 jun jun    618  1月 20 14:29 CMakeLists.txt
drwxrwxr-x 3 jun jun   4096  1月 20 15:52 build
-rw-rw-r-- 1 jun jun 182218  1月 20 15:52 cppmain.js   # ← これが出来た!!
-rwxrwxr-x 1 jun jun 587651  1月 20 15:52 cppmain.wasm # ← これが出来た!!
-rw-rw-r-- 1 jun jun   1936  1月 20 15:13 index.html
```
index.htmlと、出力されたcppmain.js、cppmain.wasmを、どこかのサーバに置けば動く!!
<br/>
で、動かす。
### 5.Webカメラの設定(内蔵カメラならこの手順は不要)
- Webカメラ挿して。
- Windowsの設定  [Windowsで設定](https://zenn.dev/rg687076/articles/8174f50cc2475a#3.windows%E3%81%A7%E8%A8%AD%E5%AE%9A)
- Virtual Box設定  [Virtual Box->デバイス->Webカメラ->HD Web Cameraにチェック](https://zenn.dev/rg687076/articles/8174f50cc2475a#4.vm%E3%81%A7%E3%80%81%E3%83%87%E3%83%90%E3%82%A4%E3%82%B9-%3Eweb%E3%82%AB%E3%83%A1%E3%83%A9-%3Ehd-web-camera%E3%81%AB%E3%83%81%E3%82%A7%E3%83%83%E3%82%AF)

### 6.サーバ起動
```shell: サーバ起動
$ cd ~/cppwasm_template/wasm
$ python3 -m http.server 8080
```

### 7.サーバにアクセス。
ブラウザから http://localhost:8080/ にアクセスする。

![](https://storage.googleapis.com/zenn-user-upload/c36ad93a42a6-20240120.png)
出来た!!
これで、Ubuntu側のwasmビルドも出来た!!

Windowsで開発して、Ubuntuでwasmビルドと動作確認の環境が出来た!!
ふぃ～。お疲れ様でした。
ハマったな～。

:::message
けど、この環境はJavaScriptなんだよな。React+TypeScriptで開発したい。これは僕の宿題。
:::

---
<- [React+TypeScriptでWebAssembly003](https://zenn.dev/rg687076/articles/49f1d420820500)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[React+TypeScriptでWebAssembly005](https://zenn.dev/rg687076/articles/b031e166604c87) ->