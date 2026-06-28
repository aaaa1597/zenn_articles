---
title: "React+TypeScriptで、WebAssemblyなjsモジュールを実行してみた。"
emoji: "🙆‍♀️"
type: "tech"
topics:
  - "cpp"
  - "react"
  - "typescript"
  - "webassembly"
published: true
published_at: "2024-01-13 13:59"
---

![](https://storage.googleapis.com/zenn-user-upload/074ab1af46b7-20240113.png =250x)

# Abstract
[前回](https://zenn.dev/rg687076/articles/ed36df1d8037e6)ので、jsモジュールの読込みができる様になったので、今回は、WebAssemblyのjsモジュールの読込みを実装してみた。

# 結論
今回の成果物はココ↓
https://github.com/aaaa1597/ReactTs-WebAsm002

# 前提
- windowsだよ。
- React+Typescriptの開発環境は構築済 [[環境構築]WindowsにVSCode+React+TypeScriptの開発環境を構築してみた。](https://zenn.dev/rg687076/articles/491d01e35cbce7)
- WebAssemblyのビルド環境も構築済み。[[環境構築]Windowsで、WebAssemblyを初めてみた。](https://zenn.dev/rg687076/articles/c02e6e6909b7cf)
- 前回プロジェクト[ReactTs-WebAsm001](https://github.com/aaaa1597/ReactTs-WebAsm001)から。

# ざっくり手順
1. cppファイル生成 -> ビルド
2. ビルドでできたwasmファイルをpublic配下にコピー
3. インターフェースとなるjsモジュールと、対となるd.tsファイルを作成
4. App.tsxでimportして呼び出す。
5. 実行

# 手順詳細
1. cppファイル生成 -> ビルド
	```shell:
	cd ReactTs-WebAsm001/src     # プロジェクトフォルダに移動
	rmdir libs                   # libsフォルダは削除
	mkdir asm-cpp                # jsモジュール置き場を作成
	type nul > asm-cpp/hello.cpp # hello.cppを自分で生成
	```

	```cpp:hello.cppの中身
	#include <stdio.h>
	#ifdef __EMSCRIPTEN__
	#include <emscripten.h>
	#else
	#define EMSCRIPTEN_KEEPALIVE
	#endif

	extern "C" {
	int EMSCRIPTEN_KEEPALIVE add(int a, int b){
	  return a + b;
	}

	int EMSCRIPTEN_KEEPALIVE sub(int a, int b){
	  return a - b;
	}
	} /* extern "C" */
	```
		

	で、ビルド
	```shell:コマントプロンプト
	<emsdkをインストールしたフォルダ>emsdk\emsdk_env.bat  # <- コマントプロンプト起動の度に毎回する。
	emcc asem-cpp/hello.cpp -o asem-cpp/hello.js -sWASM=1 -sEXIT_RUNTIME=1 -sEXPORTED_RUNTIME_METHODS=ccall,cwrap
	```

	成功したら、フォルダ構成は以下の様になる。
	```shell:コマントプロンプト
	$ プロジェクトフォルダ
	～略～
	└─src
	    │  ～略～
	    └─asm-cpp
		    hello.cpp
		    hello.js      # <- ビルドで生成される
		    hello.wasm    # <- ビルドで生成される
	```

2. ビルドでできたwasmファイルをpublic配下にコピー
	```shell:コマントプロンプト
	$ プロジェクトフォルダ
	～略～
	├─public
	│  └─wasm
	│     hello.wasm     # <- ここにコピー
	│  ～略～
	│   └─asm-cpp
	│      hello.wasm    # <- これを、
	```

3. インターフェースとなるjsモジュールと、対となるd.tsファイルを作成
	```shell:
	cd ReactTs-WebAsm001/src
	type nul > asm-cpp/helloif.js   # 自分で生成
	type nul > asm-cpp/helloif.d.ts # 自分で生成
	```

	helloif.jsは以下の様に。
	```ts:helloif.js(wasmを呼び出すためのインターフェース)
	export function helloif(argnum1, argnum2, setter) {
		WebAssembly.instantiateStreaming(fetch("wasm/hello.wasm")).then(
		(obj) => {
			const ret1 = obj.instance.exports._Z3addii(argnum1, argnum2);
			console.log('retretret=', ret1);
			setter(ret1);
			const ret2 = obj.instance.exports._Z3subii(argnum1, argnum2); # <- こっちはお試し。使ってない
			console.log('retretret=', ret2);                              # <- こっちはお試し。使ってない
		}
	  );
		return 100; # <- 戻り値は使わないので適当に。
	}
	```

	helloif.d.tsは以下の様に。
	```ts:helloif.d.ts
	declare export function helloif(argnum1: number, argnum2: number, setter:React.Dispatch<React.SetStateAction<number>>): number;
	```
4. App.tsxでimportして呼び出す。
	```diff ts:helloif.js(wasmを呼び出すためのインターフェース)
	-import React from 'react';
	+import React, { useState } from 'react';
	import './App.css';
	-import { aaa00, aaa01, aaa02 } from './libs/aaaaa';
	+import { helloif } from './asm-cpp/helloif';

	function App() {
	- console.log('---- ret00 = ', aaa00('aaa000', 0))
	- const persons = aaa01(['aaa001', 'bbb002'], 1)
	- console.log('---- ret01 = ', ...persons)
	- const argbuf = new Uint8Array([11, 22, 33])
	- const ret02 = aaa02(argbuf, 2)
	- console.log('---- ret02 = ', ret02)
	- const [count, setCount] = useState(0)
	+ const [count, setCount] = useState(0)
	+ console.log('---------------- asmret=', helloif(11, 22, setCount))

	  return (
	    <div className="App">
	      hello world!!
	+     <div>count= {count}</div>
	  </div>
	  );
	}

	export default App;
	```

5. 実行
![](https://storage.googleapis.com/zenn-user-upload/074ab1af46b7-20240113.png)
出来た!!
~~合計4回呼ばれてるのが腑に落ちないけど、ひとまず、WebAssemblyのコードが呼ばれたので良しとする。~~
修正した。