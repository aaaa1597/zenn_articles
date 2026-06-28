---
title: "React+TypeScriptなWebアプリで、R3Fのtutorial1.(コンポーネント化)"
emoji: "💬"
type: "tech"
topics:
  - "react"
  - "typescript"
  - "threejs"
  - "r3f"
published: true
published_at: "2023-12-20 20:49"
---

![](https://storage.googleapis.com/zenn-user-upload/2f469cd9b41d-20231220.png =250x)

# Abstract
今回の参考は[ここ](https://sbcode.net/react-three-fiber/componentize/)。
React+TypeScript+React Three Fiberで、コンポーネント化を動かしてみた。

どうでもいいけど、
[React Three Fiber](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction)、便利って言うんで頑張ってるけど、[公式のExamples](https://docs.pmnd.rs/react-three-fiber/getting-started/examples)って動かんくね？

# 結論
今回の成果物はココ↓
https://github.com/aaaa1597/react-r3f-tutorial001

# 前提
- React+Typescriptの開発環境は構築済 [Ubuntu22.04+VSCode+React+TypeScriptの開発環境を構築してみた。](https://zenn.dev/rg687076/articles/5912e808924d28)
- 前回のプロジェクトから始める。[React+TypeScriptなWebアプリで、"React Three Fiber"の3D表示の最初の一歩。](https://github.com/aaaa1597/react-r3f-1ststep)

# 手順
## 1.プロジェクト生成 -> VSCodeで開く
めんどいから、プロジェクト雛形から始める。[React+TypeScriptなWebアプリで、"React Three Fiber"の3D表示の最初の一歩。](https://github.com/aaaa1597/react-r3f-1ststep)
で、下記コマンドでフォルダ名とか整備する。
```shell:フォルダリネームとか
$ NewProject=react-r3f-tutorial001
$ cd ~
$ git clone https://github.com/aaaa1597/react-r3f-1ststep.git
$ rm -rf react-r3f-1ststep/.git
$ rm -rf ${NewProject}
$ mv react-r3f-1ststep ${NewProject}
```

# App.tsx
App.tsxを修正する。
- Canvas要素に縦横サイズを設定するのかと思ったら、その上のdiv要素に設定する必要があるらしい。何のためのCanvas要素なんだろ？へんなの。
- meshBasicMaterial要素にwireframeをつけてやるとワイヤフレームになる。

```diff ts:App.tsx
 function App() {
   return (
-    <div id="canvas-container">
+    <div style={{ width: "75vw", height: "75vh" }}>
       <Canvas>
         <ambientLight />
         <directionalLight />
         <mesh>
           <boxGeometry args={[4, 4, 4]}/>
-          <meshStandardMaterial />
+          <meshBasicMaterial color={0x00ff00} wireframe />
         </mesh>
       </Canvas>
     </div>
  );
}

export default App;
```

# .eslintrc.js
このまま、npm startで動かしても、EsLintのエラーにつかまってしまう。
なので、設定追加。
```diff js:.eslintrc.js
     "rules": {
+        "react/no-unknown-property": ['error', { ignore: ['css', "args", 'wireframe'] }],
     }
 }
```

# 実行
npm start
![](https://storage.googleapis.com/zenn-user-upload/2f469cd9b41d-20231220.png)
出来た。
ワイヤフレームが表示された。

[React+TypeScriptなWebアプリで、R3Fのtutorial2.(Propsの使い方)](https://zenn.dev/rg687076/articles/52369828c0711e)