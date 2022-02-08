---
title: Building a Basic Scene
section_title: Guides
type: guides
layout: docs
parent_section: docs
order: 1
section_order: 2
nav_expand_sections: true
image:
  src: https://user-images.githubusercontent.com/674727/29746371-b7906dcc-8a8c-11e7-8ef4-3f7e4d224ce7.jpg
examples:
  - title: Basic Guide with Environment
    src: https://glitch.com/edit/#!/aframe-basic-guide-with-environment?path=index.html
---

[primitives]: ../primitives/
[position]: ../components/position.md
[rotation]: ../components/rotation.md
[scale]: ../components/scale.md

まずは基本的なA-Frameのシーンを作るところから始めましょう。このためには、HTMLの基本的な理解が必要です。ここでは、その方法を学びます。

- [プリミティブ][primitives]を使った3Dエンティティ（オブジェクト）の追加
- 3D空間内のエンティティを位置、回転、スケールで変形
- 環境の追加
- テクスチャーの追加
- アニメーションとイベントによる基本的なインタラクティブ機能の追加
- テキストの追加

[Remix the Basic Guide example on Glitch](https://glitch.com/~aframe-basic-guide-with-environment).

<!--toc-->

## Starting with HTML

まずは最小限のHTML構造からスタートします。

```html
<html>
  <head>
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
  </head>
  <body>
    <a-scene>
    </a-scene>
  </body>
</html>
```

CDNでホストされているA-Frameのビルドを指す、`<head>`内のscriptタグを用いて、A-Frameを読み込みます。A-FrameはカスタムHTML要素を登録するので、`<a-scene>`の前に定義しないと`<a-scene>`では何もおこらないため、`<a-scene>`の前に記述する必要があるのです。

[scene]: ../core/scene.md

次に、`<a-scene>`[scene]を`<body>`に含めます。`<a-scene>` には、シーンのすべてのエンティティが含まれます。WebGL の設定、キャンバス、カメラ、ライト、レンダラー、レンダリングループ、そして HTC Vive、Oculus Rift、Samsung GearVR、スマートフォン（Google Cardboard）などのプラットフォームですぐに使える WebVR サポートなど、3D に必要なすべての設定を `<a-scene>` が行っています。`<a-scene>` だけでも、私たちの負担はかなり軽減されます。

## Adding an Entity

[abox]: ../primitives/a-box.md

A-Frameの標準的なプリミティブである`<a-box>`を使用して、3Dエンティティを`<a-scene>`内にアタッチします。通常のHTML要素のように、タグを定義し、HTML属性を使ってカスタマイズすることで、`<a-box>`を使用することができます。A-Frameに付属するプリミティブの他の例としては、 `<a-cylinder>` 、 `<a-plane>` 、 `<a-sphere>` などがあります。

ここでは、 `<a-box>`に色を定義しています。その他の属性（例えば、`width`, `height`, `depth` ）については [`<a-box>` のドキュメント][abox] を参照してください。

[boximage]: https://cloud.githubusercontent.com/assets/674727/20326452/b7f94966-ab3d-11e6-95e1-47cabf425278.jpg
![boximage]
<small class="image-caption"><i>Image by Ruben Mueller from vrjump.de</i></small>

```html
<a-scene>
  <a-box color="red"></a-box>
</a-scene>
```

[entity]: ../core/entity.md
[geometry]: ../components/geometry.md
[material]: ../components/material.md

余談ですが、プリミティブとは、A-Frameの使いやすいHTML要素で、基本となるエンティティ・コンポーネントの組み合わせで構成されています。`<a-box>`は、[`<a-entity>`][entity]と[geometry]と[material]コンポーネントを用いて以下のように書くことも出来ます。

```html
<a-entity id="box" geometry="primitive: box" material="color: red"></a-entity>
```

[camera]: ../components/camera.md

デフォルトの[camera]とボックスの位置は`0 0 0`が原点なので、ボックスを移動させないとボックスが見えません。このため、[position][position]コンポーネントを使用して3D空間でボックスを移動させることでボックスを表示させることが出来ます。

## Transforming an Entity in 3D

まず、3次元空間について説明します。A-Frameは右手座標系を採用しています。デフォルトのカメラ方向では、正のX軸は*右*に、正のY軸は*上*に、正のZ軸は*画面の外*からこちらに向かって伸びています。

[righthandimage]: https://cloud.githubusercontent.com/assets/674727/20328731/326137a8-ab49-11e6-9b76-4e3a65f333d9.jpg
![righthandimage]
<small class="image-caption"><i>Image from what-when-how.com</i></small>

[webvr]: https://developer.mozilla.org/docs/Web/API/WebVR_API

[WebVR API][webvr] はメートル単位でポーズデータを返すため、A-Frame の距離単位は **メートル** となります。VR 用のシーンをデザインする場合、作成するエンティティの現実世界でのスケールを考慮することが重要です。`height="10"` の箱は、コンピュータの画面では普通に見えますが、VR では巨大な箱に見えます。

[right-hand rule]: https://wikipedia.org/wiki/Right-hand_rule

A-Frameの回転単位は**度**ですが、three.jsに渡すと内部でラジアンに変換されます。回転の正方向を決めるには、【右手の法則】を使います。親指を正の軸の方向に向け、指を丸める方向が回転の正の方向となります。

ボックスの平行移動、回転、拡大縮小は、[position] [rotation] [scale]成分を変更することで行います。まず、rotationとscaleのコンポーネントを適用してみましょう。

```html
<a-scene>
  <a-box color="red" rotation="0 45 45" scale="2 2 2"></a-box>
</a-scene>
```

これにより、ボックスが斜めに回転し、サイズが2倍になります。

### Parent and Child Transforms

[scene graph]: https://wikipedia.org/wiki/Scene_graph

A-Frame HTMLは、3Dの[シーングラフ][scene graph]を表現しています。シーングラフでは、エンティティは1つの親と複数の子を持つことが出来ます。子エンティティは、親エンティティから座標、回転、スケールなどを引き継ぎます。

例えば、ボックスの子として球を持つことが出来ます。

```html
<a-scene>
  <a-box position="0 2 0" rotation="0 45 45" scale="2 4 2">
    <a-sphere position="1 0 3"></a-sphere>
  </a-box>
</a-scene>
```

球体のワールド座標を計算すると、`1 2 3`となり、球体の親の座標と球体自身の座標を合成することで達成されます。同様に、回転とスケールに関しても、球体はボックスの回転とスケールを継承することになります。球体も親であるボックスと同じように回転やスケールをします。Box が座標、回転、スケールを変更すると、即座に球体に適用され、影響を受けます。

球体の子として円柱を追加する場合、円柱のトランスフォームは球体のトランスフォームとボックスのトランスフォームの両方の影響を受けることになります。three.js の内部では、これは *変換行列* を互いに乗算することで行われます。幸いなことに、私たちはそのことについて考える必要はありません。

### Placing our Box in Front of the Camera

では、最初からボックスがカメラに見えるように戻してみましょう。位置コンポーネントを使用して、負の Z 軸上でボックスを **5 メートル** 後方に移動できます。また、ボックスをスケーリングし、スケーリングは中心から行われるので、ボックスが地面と交差しないように、正の Y 軸で **2 メートル** 上に移動させる必要があります。

```html
<a-scene>
  <a-box color="red" position="0 2 -5" rotation="0 45 45" scale="2 2 2"></a-box>
</a-scene>
```

さて、私たちの箱が見えてきましたよ。

[boximage2]: https://cloud.githubusercontent.com/assets/674727/20327511/34a5e5f6-ab42-11e6-8107-66c42b80f846.png
![boximage2]

### Default Controls

フラットディスプレイ（ラップトップやデスクトップ）の場合、デフォルトのコントロール方法では、マウスをクリック・ドラッグすることで周囲を見渡し、`WASD`キーや矢印キーで移動することができます。モバイルでは、携帯電話をパンしてカメラを回転させることができます。A-FrameはWebVRに特化していますが、このデフォルトの操作体系により、ヘッドセット無しでシーンを見ることができます。

[mozvr]: http://mozvr.com/#start
[webvrimage]: https://cloud.githubusercontent.com/assets/674727/20328949/69e2c628-ab4a-11e6-97e9-6e25104d6673.png
![webvrimage]

> [Mozilla VRのホームページでWebVRの設定と入室方法を確認する][mozvr]。

VRヘッドセット（Oculus Rift、HTC Viveなど）を接続した状態でゴーグルのアイコンをクリックして[VRに入る][mozvr]と、没入型VRでシーンを体験することができる。ルームスケールがあれば、物理的にシーンの周りを歩くことができます!

## Adding an Environment

[@feiss]: https://github.com/feiss/
[environment]: https://github.com/feiss/aframe-environment-component/

A-Frameでは、開発者が再利用可能なコンポーネントを作成・共有し、他の人が簡単に利用できるようにすることができます。[@feiss] の [environment コンポーネント][environment] は、一行の HTML で様々な環境全体をプロシージャルに生成してくれます。環境コンポーネントは、VRアプリケーションを視覚的に起動するための簡単で素晴らしい方法で、多数のパラメータを持つ12以上の環境を提供します。

まず、A-Frameの後にscriptタグを使用して環境コンポーネントを読み込みます。

```html
<head>
  <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
  <script src="https://unpkg.com/aframe-environment-component/dist/aframe-environment-component.min.js"></script>
</head>
```

そして、シーン内に、環境コンポーネントを付けたエンティティを追加します。プリセット（例：`forest`）と、その他多くのパラメータ（例：200本の木）を指定することができます。

```html
<a-scene>
  <a-box color="red" position="0 2 -5" rotation="0 45 45" scale="2 2 2"></a-box>

  <!-- Out of the box environment! -->
  <a-entity environment="preset: forest; dressingAmount: 500"></a-entity>
</a-scene>
```

## Applying an Image Texture

> Make sure you're [serving your HTML using a local server](../introduction/getting-started.md#using-a-local-server)
> for textures to load properly.

通常の `<img>` 要素と同様に、 `src` 属性を使用して、画像、ビデオ、または `<canvas>` で画像テクスチャをボックスに適用することができます。また、テクスチャに色が混ざらないように、設定した `color="red"` を削除する必要があります。デフォルトのマテリアルカラーは 白色 ですので、color 属性を削除すれば十分です。

```html
<a-scene>
  <a-box src="https://i.imgur.com/mYmmbrp.jpg" position="0 2 -5" rotation="0 45 45"
    scale="2 2 2"></a-box>

  <a-sky color="#222"></a-sky>
</a-scene>
```

では、オンラインから引っ張ってきた画像テクスチャを使ったボックスをご覧ください。

[boxtextureimage]: https://cloud.githubusercontent.com/assets/674727/20329111/368aea3e-ab4b-11e6-89e1-d13c994290ce.png
![boxtextureimage]

### Using the Asset Management System

[asset management system]: ../core/asset-management-system.md

ただし、性能面では[アセット管理システム][asset management system]の利用を推奨しています。資産管理システムを使うと、ブラウザが資産（画像、動画、モデルなど）をキャッシュしやすくなり、A-Frameはレンダリング前にすべての資産を取得するようにします。

資産管理システムで `<img>` を定義しておけば、後のthree.jsは内部で `<img>` を作成する必要がありません。また、`<img>`を自分で作成することで、よりコントロールしやすくなり、複数のエンティティでテクスチャを再利用できるようになります。A-Frameは、必要に応じて`crossOrigin`などの属性を設定することも可能なほどスマートです。

画像テクスチャーの資産管理システムを利用するため。

- シーンに `<a-assets>` を追加します。
- テクスチャは `<a-assets>` の下に `<img>` として定義されます。
- `<img>` に HTML ID を与えます (例: `id="boxTexture"`).
- DOMセレクタ形式でIDを使用してアセットを参照します（`src="#boxTexture"`）。

```html
<a-scene>
  <a-assets>
    <img id="boxTexture" src="https://i.imgur.com/mYmmbrp.jpg">
  </a-assets>

  <a-box src="#boxTexture" position="0 2 -5" rotation="0 45 45" scale="2 2 2"></a-box>

  <a-sky color="#222"></a-sky>
</a-scene>
```

## Creating a Custom Environment (Optional)

前回は、環境コンポーネントが環境を生成してくれました。しかし、学習のために基本的な環境の作成について少し知っておくのは良いことです。

### Adding a Background to the Scene

[sky]: ../primitives/a-sky.md

シーンを囲む[`<a-sky>`][sky]で背景を追加することができます。大きな球体の内側に適用されるマテリアルである `<a-sky>` には、フラットカラー、360&deg; 画像、または 360&deg; ビデオを使用することができます。例えば、濃いグレーの背景の場合、次のようになります。

```html
<a-scene>
  <a-box color="red" position="0 2 -5" rotation="0 45 45" scale="2 2 2"></a-box>

  <a-sky color="#222"></a-sky>
</a-scene>
```

また、 `color` の代わりに `src` を使用することで、イメージテクスチャを使用して 360&deg; の背景画像を得ることができます。

```html
<a-scene>
  <a-assets>
    <img id="boxTexture" src="https://i.imgur.com/mYmmbrp.jpg">
    <img id="skyTexture"
      src="https://cdn.aframe.io/360-image-gallery-boilerplate/img/sechelt.jpg">
  </a-assets>

  <a-box src="#boxTexture" position="0 2 -5" rotation="0 45 45" scale="2 2 2"></a-box>

  <a-sky src="#skyTexture"></a-sky>
</a-scene>
```

### Adding a Ground

[aplane]: ../primitives/a-plane.md

地面を追加するには、[`<a-plane>`][aplane]を使用します。デフォルトでは、平面はXY軸に平行に配向しています。地面と平行にするには、XZ軸に沿った向きにする必要があります。そのためには、平面をX軸上でマイナス90度回転させます。

```html
<a-plane rotation="-90 0 0"></a-plane>
```

地面を大きくしたいので、`width`と`height`を大きくします。各方向30mにしよう。

```html
<a-plane rotation="-90 0 0" width="30" height="30"></a-plane>
```

次に、画像テクスチャを地面に適用することができます。

```html
  <a-assets>
    <!-- ... -->
    <img id="groundTexture" src="https://cdn.aframe.io/a-painter/images/floor.jpg">
    <!-- ... -->
  </a-assets>

  <!-- ... -->
  <a-plane src="#groundTexture" rotation="-90 0 0" width="30" height="30"></a-plane>
  <!-- ... -->
```

テクスチャを並べるには、`repeat` 属性を使用することができます。repeat` は2つの数値、X方向に何回繰り返すか、Y方向に何回繰り返すか（テクスチャでは一般的にUとVと呼ばれる）を受け取ります。

```html
<a-plane src="#groundTexture" rotation="-90 0 0" width="30" height="30"
  repeat="10 10"></a-plane>
```

### Tweaking Lighting

[light]: ../primitives/a-light.md

シーンの照明方法は、[`<a-light>s`][light]を使って変更することができます。デフォルトでは、何もライトを指定しないと、A-Frameは環境光と指向性光を追加します。もし、A-Frameがライトを追加してくれなかったら、シーンは真っ黒になってしまいます。しかし、独自に照明を追加すると、デフォルトの照明設定は削除され、独自に設定した照明に置き換わります。

ここでは、空と同じ青緑色をしたアンビエントライトを追加します。アンビエントライトは、シーン内のすべてのエンティティに適用されます（少なくともデフォルトのマテリアルが適用されている場合）。

また、ポイントライトも追加します。ポイントライトは電球のようなもので、シーンの周りに配置することができ、エンティティに対するポイントライトの効果は、エンティティとの距離によって決まります。

```html
<a-scene>
  <a-assets>
    <img id="boxTexture" src="https://i.imgur.com/mYmmbrp.jpg">
    <img id="skyTexture"
      src="https://cdn.aframe.io/360-image-gallery-boilerplate/img/sechelt.jpg">
    <img id="groundTexture" src="https://cdn.aframe.io/a-painter/images/floor.jpg">
  </a-assets>

  <a-box src="#boxTexture" position="0 2 -5" rotation="0 45 45" scale="2 2 2"></a-box>

  <a-sky src="#skyTexture"></a-sky>

  <a-light type="ambient" color="#445451"></a-light>
  <a-light type="point" intensity="2" position="2 4 4"></a-light>
</a-scene>
```

## Adding Animation

[animation]: ../components/animation.md

A-Frameの[内蔵アニメーションシステム][animation]を使って、ボックスにアニメーションを追加することができます。アニメーションは、時間の経過とともに値を補間したり、トゥイーンさせたりします。エンティティにアニメーションコンポーネントを設定することができます。ボックスが上下にホバリングして、シーンに動きを加えてみましょう。

```html
<a-scene>
  <a-assets>
    <img id="boxTexture" src="https://i.imgur.com/mYmmbrp.jpg">
  </a-assets>

  <a-box src="#boxTexture" position="0 2 -5" rotation="0 45 45" scale="2 2 2"
         animation="property: object3D.position.y; to: 2.2; dir: alternate; dur: 2000; loop: true"></a-box>
</a-scene>
```

[entity]: ../core/entity.md
[object3D]: ../core/entity.md#object3d

アニメーションコンポーネントに指示します。

- [entity][entity] [object3D's][object3D]  の位置のY軸をアニメートする。
- 20センチメートル高い位置にある `2.2` までアニメートさせる。
- アニメーションの繰り返しサイクルごとにアニメーションの *dir* (方向) を交互に変えて、行ったり来たりするようにします。
- 各サイクルでは、2000ミリ秒の*dur* (持続時間)を持続させます。
- *ループ*または永遠にアニメーションを繰り返す。

[animationgif]: https://cloud.githubusercontent.com/assets/674727/20375894/e8b3fa48-ac36-11e6-9ecd-5a5fe33cb9db.gif
![animationgif]

## Adding Interaction

[cursor component]: ../components/cursor.md

箱を見ると箱のサイズが大きくなり、箱を「クリック」すると箱が回転する、というインタラクションを追加してみましょう。

現在、多くの開発者がコントローラー付きの適切なVRハードウェアを持っていないことを考慮して、このセクションでは、内蔵の[カーソルコンポーネント][cursor component]を使用して基本的なモバイルおよびデスクトップ入力を使用することに焦点を当てます。カーソルコンポーネントはデフォルトで、モバイルではエンティティを見つめたり凝視したり、デスクトップではエンティティを見てマウスをクリックすることでエンティティを「クリック」する機能を提供します。しかし、カーソルコンポーネントはインタラクションを追加するための一つの手段に過ぎず、実際のコントローラにアクセスできるようになれば、物事は大きく進展します。

[parentchild]: #parent-and-child-transforms

[camera]に固定されたカーソルが見えるようにするには、上記の[親子変換][parentchild]で説明したように、カーソルをカメラの子として配置します。

[camera]: ../components/camera.md

特にカメラを定義しなかったので、A-Frameにはデフォルトのカメラが含まれています。しかし、カメラの子としてカーソルを追加する必要があるため、[`<a-camera>`][camera]に `<a-cursor>` を含むように定義する必要があります。

```html
<a-scene>
  <a-assets>
    <img id="boxTexture" src="https://i.imgur.com/mYmmbrp.jpg">
  </a-assets>

  <a-box src="#boxTexture" position="0 2 -5" rotation="0 45 45" scale="2 2 2"
         animation="property: object3D.position.y; to: 2.2; dir: alternate; dur: 2000; loop: true"></a-box>

  <a-camera>
    <a-cursor></a-cursor>
  </a-camera>
</a-scene>
```

`<a-cursor>` がラップしている [カーソルコンポーネントのドキュメント][cursor component] を確認すると、`click` だけでなく `mouseenter` や `mouseleave` などのホバーイベントを発生することがわかります。

### Event Listener Component (Intermediate)

[addeventlistener]: https://developer.mozilla.org/docs/Web/API/EventTarget/addEventListener
[animatingonevents]: #animating-on-events
[queryselector]: https://developer.mozilla.org/docs/Web/API/Document/querySelector
[setattribute]: ../core/entity.md#setattribute-componentname-value-propertyvalue-clobber

カーソルイベントを手動で処理する1つの方法は、通常のDOM要素と同じように、[JavaScriptでイベントリスナーを追加する][addeventlistener]ことです。JavaScriptに慣れていない方は、以下の[イベント時のアニメーション][animatingonevents]まで読み飛ばしていただいて結構です。

JavaScript では、[`querySelector`][queryselector] でボックスを取得し、[`addEventListener`][addeventlistener] を使い、さらに [`setAttribute`][setattribute] でカーソルを合わせたときにボックスが拡大されるように設定しています。なお、A-Frame では `setAttribute` にマルチプロパティコンポーネントで動作する機能が追加されています。第2引数には、完全な `{x, y, z}` オブジェクトを渡すことができます。

```js
<script>
  var boxEl = document.querySelector('a-box');
  boxEl.addEventListener('mouseenter', function () {
    boxEl.setAttribute('scale', {x: 2, y: 2, z: 2});
  });
</script>
```

しかし、もっと堅牢な方法は、このロジックをA-Frameコンポーネントにカプセル化することでしょう。この方法では、シーンの読み込みを待つ必要がなく、コンポーネントがコンテキストを与えてくれるため、クエリセレクタを実行する必要がありません。また、ページ上で制御不能なスクリプトを実行するのではなく、コンポーネントを再利用して設定することができます。さらに良い方法は、`.setAttribute`を呼び出すのをスキップして、`this.el.object3D.scale`に直接値を設定することでパフォーマンスを向上させることができます。

```js
<script>
  AFRAME.registerComponent('scale-on-mouseenter', {
    schema: {
      to: {default: '2.5 2.5 2.5', type: 'vec3'}
    },

    init: function () {
      var data = this.data;
      var el = this.el;
      this.el.addEventListener('mouseenter', function () {
        el.object3D.scale.copy(data.to);
      });
    }
  });
</script>
```

このコンポーネントは、HTMLから直接ボックスに取り付けることができます。

```html
<script>
  AFRAME.registerComponent('scale-on-mouseenter', {
    // ...
  });
</script>

<a-scene>
  <!-- ... -->
  <a-box src="#boxTexture" position="0 2 -5" rotation="0 45 45" scale="2 2 2"
         animation="property: object3D.position.y; to: 2.2; dir: alternate; dur: 2000; loop: true"
         scale-on-mouseenter></a-box>
  <!-- ... -->
</a-scene>
```

### Animating on Events

アニメーションコンポーネントには、エンティティがイベントを発すると、そのアニメーションを開始する機能があります。これは、カンマで区切られたイベント名のリストを受け取る `startEvents` 属性を通して行うことができます。

カーソルコンポーネントの `mouseenter` と `mouseleave` イベントでボックスのスケールを変更するためのアニメーションを2つ、`click` でボックスをY軸周りに回転させるためのアニメーションを1つ追加することができます。属性名のサフィックスを `__<ID>` にすることで、1つのエンティティに複数のアニメーションを持たせることができることに注意してください。

```html
<a-box
  src="#boxTexture"
  position="0 2 -5"
  rotation="0 45 45"
  scale="2 2 2"
  animation__position="property: object3D.position.y; to: 2.2; dir: alternate; dur: 2000; loop: true"
  animation__mouseenter="property: scale; to: 2.3 2.3 2.3; dur: 300; startEvents: mouseenter"
  animation__mouseleave="property: scale; to: 2 2 2; dur: 300; startEvents: mouseleave"></a-box>
```

## Adding Audio

VRで没入感や臨場感を与えるためには、音声が重要です。背景に簡単なホワイトノイズを加えるだけでも、大きな効果があります。各シーンに何らかのオーディオを用意することをお勧めします。一つの方法は、HTMLにオーディオファイルを再生するための `<audio>` 要素を追加することです（できれば `<a-assets>` の下に追加します）。

```html
<a-scene>
  <a-assets>
    <audio src="https://cdn.aframe.io/basic-guide/audio/backgroundnoise.wav" autoplay
      preload></audio>
  </a-assets>

  <!-- ... -->
</a-scene>
```

また、`<a-sound>` を使って、位置情報を含んだ音声を追加することもできます。この場合、音は近づくと大きくなり、離れると小さくなります。また、`position`を使ってシーン内にサウンドを配置することもできます。

```html
<a-scene>
  <!-- ... -->
  <a-sound src="https://cdn.aframe.io/basic-guide/audio/backgroundnoise.wav" autoplay="true"
    position="-3 1 -4"></a-sound>
  <!-- ... -->
</a-scene>
```

## Adding Text

[text]: ../components/text.md
[three-bmfont-text]: https://github.com/Jam3/three-bmfont-text

A-Frameには、[textコンポーネント][text]が付属しています。テキストをレンダリングする方法はいくつかあり、それぞれにメリットとデメリットがあります。A-Frame には [`three-bmfont-text`][three-bmfont-text] を使った SDF テキスト実装が付属しており、比較的シャープで高性能なものとなっています。

```html
<a-entity
  text="value: Hello, A-Frame!; color: #BBB"
  position="-0.9 0.2 -3"
  scale="1.5 1.5 1.5"></a-entity>
```

## Play With It!

そして、これが基本的な例です

[Remix the example on Glitch](https://glitch.com/~aframe-basic-guide-with-environment).

[View the example on Glitch](https://aframe-basic-guide-with-environment.glitch.me).
