---
title: "React+TypeScriptなWebアプリで、R3Fのtutorial9。(OrbitControlsの使い方)"
emoji: "🐈"
type: "tech"
topics:
  - "react"
  - "typescript"
  - "threejs"
  - "drei"
  - "d3f"
published: true
published_at: "2023-12-24 09:54"
---

![](https://storage.googleapis.com/zenn-user-upload/cb4914f52301-20231224.png =250x)

# Abstract
今回の参考は[ここ(OrbitControls)](https://sbcode.net/react-three-fiber/orbit-controls/)。

# 結論
今回の成果物はココ↓
https://github.com/aaaa1597/react-r3f-tutorial009

# 前提
- React+Typescriptの開発環境は構築済 [Ubuntu22.04+VSCode+React+TypeScriptの開発環境を構築してみた。](https://zenn.dev/rg687076/articles/5912e808924d28)
- このスケルトンコードから始める。[react-r3f-tutorial004](https://github.com/aaaa1597/react-r3f-tutorial004)
 
# 手順
## 1.プロジェクト生成 -> VSCodeで開く
めんどいから、このスケルトンコードから始める。[react-r3f-tutorial004](https://github.com/aaaa1597/react-r3f-tutorial004)
で、下記コマンドでフォルダ名とか整備する。
```shell:フォルダリネームとか
$ BaseProject=react-r3f-tutorial006
$ NewProject=react-r3f-tutorial009
$ cd ~
$ rm -rf ${BaseProject}
$ git clone https://github.com/aaaa1597/${BaseProject}.git
$ rm -rf ${BaseProject}/.git
$ mv ${BaseProject} ${NewProject}
$ cd ${NewProject}
```

# 準備
```shell
$ npm install --save three
$ npm install --save @types/three
$ npm install --save @react-three/fiber
$ npm install --save @react-three/drei
```

# App.tsx
まず全体。
```diff ts:App.tsx
import React, {useRef} from 'react';
import './App.css';
import { Canvas, useFrame, MeshProps  } from '@react-three/fiber'
import * as THREE from 'three'
+import { OrbitControls } from "@react-three/drei";

const Box = (props: MeshProps) => {
  const ref = useRef<THREE.Mesh>(null!)

  useFrame((_, delta) => {
    if( !ref.current) return
    ref.current.rotation.x += 1 * delta
    ref.current.rotation.y += 0.5 * delta
  })

  return (
    <mesh {...props} ref={ref}>
      <boxGeometry />
      <meshBasicMaterial color={0x00ff00} wireframe />
    </mesh>
  )
}

const App = () => {

  return (
    <div style={{ width: "75vw", height: "75vh" }}>
      <Canvas camera={{ position: [3, 1, 2] }}>
+       <OrbitControls />
        <ambientLight />
        <directionalLight />
        <Box position={[-0.75, 0, 0]} name="A" />
        <Box position={[0.75, 0, 0]} name="B" />
      </Canvas>
    </div>
  );
}

export default App;
```
こんだけ。簡単!!

![](https://storage.googleapis.com/zenn-user-upload/cb4914f52301-20231224.png)
出来た!!
画像じゃわからんけど、マウス操作で回転/拡縮するのが分かる。
ちなみに、移動は右ドラッグで。

# おさらい
## 1.dreiのインストール
OrbitControlsは、dreiのライブラリなので、インストールしと
```shell
$ npm install --save @react-three/drei
```
## 2.OrbitControlsのimport
OrbitControlsをimport。
```ts
import { OrbitControls } from "@react-three/drei";
```
## 3.OrbitControlsを使う
<Canvas></Canvas>の範囲内で、使う。
```ts
    <OrbitControls />
```

簡単～。

---
[React+TypeScriptなWebアプリで、R3Fのtutorial8。(統計情報の表示2)](https://zenn.dev/rg687076/articles/10eca151fba819)

---
[React+TypeScriptなWebアプリで、R3Fのtutorial10。(gridHelper、axesHelper)](https://zenn.dev/rg687076/articles/911598b6df3d27)


