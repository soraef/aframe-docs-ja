---
title: Interactions & Controllers
type: introduction
layout: docs
parent_section: introduction
order: 9
examples:
  - title: Basic Interaction
    src: https://glitch.com/edit/#!/aframe-basic-interaction?path=index.html:1:0
  - title: Escape the Room
    src: https://glitch.com/edit/#!/wide-cream?path=index.html:1:0
---

A-Frameが対応するプラットフォーム、デバイス、入力方式は多岐にわたるため、インタラクションを追加する方法はひとつではありません。その上、VRインタラクションはオープンエンドです。マウスやタッチ入力しか気にする必要のない2Dウェブや、1つのボタンしか気にする必要のないCardboardとは異なり、VRでは、つかむ、投げる、こする、ひっくり返す、突く、伸ばす、押すなど、何でも可能なのです。さらに進んで、複合現実感、トラッカー、カスタムコントローラーを使えば、インタラクションにさらに創造性を発揮することができます。


このセクションでできることは、一般的なインタラクションのための既存のコンポーネントを確認することです。そして、これらの入力ベースおよびインタラクションベースのコンポーネントがどのように構築されているかを示すことで、私たち自身のインタラクションを構築するための知識を得ることができるのです。ある意味、魚を与えられるのではなく、魚の釣り方を学ぶのです。


<!--toc-->

## Events

[events]: ./javascript-events-dom-apis.md#events-and-event-listeners
[addeventlistener]: https://developer.mozilla.org/docs/Web/API/EventTarget/addEventListener

2D Web では、入力とインタラクションは [ブラウザイベント][events] (例: `click`, `mouseenter`, `mouseleave`, `touchstart`, `touchend`) によって扱われます。入力ベースのイベントが発生すると、ブラウザはイベントを発行します。このイベントをリスニングし、[`Element.addEventListener`][addeventlistener] で処理することができます。


```js
// `click` event emitted by browser on mouse click.
document.querySelector('p').addEventListener('click', function (evt) {
  console.log('This 2D element was clicked!');
});
```

[synthetic]: https://developer.mozilla.org/docs/Web/Guide/Events/Creating_and_triggering_events

2Dウェブのように、A-Frameは、インタラクティブ性とダイナミズムのために、イベントとイベントリスナーに依存しています。しかし、A-FrameはJavaScriptのフレームワークであり、すべてがWebGLで行われるため、**A-Frameのイベントは[合成カスタムイベント][synthetic]**で、任意のイベントを記述するコンポーネントが発することが可能です。


```js
// `collide` event emitted by a component such as some collider or physics component.
document.querySelector('a-entity').addEventListener('collide', function (evt) {
  console.log('This A-Frame entity collided with another entity!');
});
```

[mousecursor]: https://github.com/mayognaise/aframe-mouse-cursor-component

よくある誤解は、A-Frame のエンティティに `click` イベントリスナーを追加すれば、マウスで直接エンティティをクリックすることで動作すると期待するものです。WebGL では、このような `click` イベントを提供する入力とインタラクションを提供する必要があります。例えば、A-Frameの `cursor` コンポーネントは、レイキャスターを使って、視線に対する `click` イベントを合成して生成します。また、別の例として、飛田真代さんの [mouse-cursor component][mousecursor] は、レイキャスターを使ってエンティティを直接クリックしたときに、合成の `click` イベントを作成します。


## Gaze-Based Interactions with cursor Component

[Remix this cursor example on Glitch](https://glitch.com/~aframe-school-cursor-handler/).

まず、視線を使ったインタラクションについて説明します。視線ベースのインタラクションは、頭を回転させ、オブジェクトを見て、インタラクションを行うことに依存します。このタイプのインタラクションは、コントローラーのないヘッドセット向けです。回転のみのコントローラー（Daydream、GearVR、Oculus Go）であっても、インタラクションは似ています。A-Frameはデフォルトでマウスドラッグのコントロールを提供するので、視線ベースはデスクトップでカメラの回転をドラッグすることでインタラクションをプレビューするために使うことができるようなものです。


[configureraycaster]: ../components/cursor.md#configuring-the-cursor-through-the-raycaster-component
[cursor]: ../components/cursor.md

注視型インタラクションを追加するには、コンポーネントを追加または実装する必要があります。A-Frameには[cursorコンポーネント][cursor]が付属しており、カメラに取り付けた場合、視線ベースのインタラクションを提供します。


1. [`<a-camera>`](../components/camera.md) エンティティを明示的に定義するようにしました。以前は、A-Frameがデフォルトのカメラを提供していました。
2. [`<a-cursor>`][cursor] エンティティを、カメラエンティティの下に子要素として追加します。
3. 任意で、[カーソルが使用するレイキャスターの設定][configureraycaster]を行います。

```html
<a-scene>
  <a-camera>
    <a-cursor></a-cursor>
    <!-- Or <a-entity cursor></a-entity> -->
  </a-camera>
</a-scene>
```

### Handling Events with event-set Component

[Remix this event set component example on Glitch](https://glitch.com/~aframe-event-set-component).

[event-set]: https://github.com/supermedium/superframe/tree/master/components/event-set

では、カーソルコンポーネントが提供するイベントを処理してみましょう。カーソルコンポーネントは、 `click`、`mouseenter`、`mouseleave`、`mousedown`、`mouseup`、`fusing` といった合成イベントを発生させます。これらのイベントはブラウザのネイティブイベントと同じような名前にして、初めての方にもわかりやすいようにしましたが、やはり合成イベントであることに注意してください。


イベントをリスニングし、それに応じてプロパティを設定するような基本的なイベントハンドラには、[event-set component][event-set] を使用することができます。event-set コンポーネントは、基本的なイベントハンドラを宣言的にするものです。event-setコンポーネントのAPIは以下のようなものです。


```html
<a-entity event-set__${id}="_event: ${eventName}; ${someProperty}: ${toValue}">
```

`__${id}`の部分によって、同じエンティティに複数のイベントセットコンポーネントをアタッチすることができます。インスタンスがどのイベントを処理するかは `${eventName}` で指定します。そして、イベントが発生したときに設定したいプロパティ名と値をエンティティに渡します。


例えば、エンティティにカーソルを置いたり、見たりしたときに、そのエンティティが見えるようにすることができます。カーソルコンポーネントは `mouseenter` イベントを提供します。


```html
<a-entity event-set__makevisible="_event: mouseenter; visible: true">
```

ホバー時にボックスの色を変更し、ホバーしなくなったら元に戻したい場合。


```html
<script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
<script src="https://unpkg.com/aframe-event-set-component@3.0.3/dist/aframe-event-set-component.min.js"></script>
<body>
  <a-scene>
    <a-box position="-1 0.5 -3" rotation="0 45 0" color="#4CC3D9"
           event-set__enter="_event: mouseenter; color: #8FF7FF"
           event-set__leave="_event: mouseleave; color: #4CC3D9"></a-box>

    <a-camera>
      <a-cursor></a-cursor>
    </a-camera>
  </a-scene>
</body>
```

イベントセットコンポーネントは、`_target: ${selector}` を使用して、他のエンティティをターゲットにすることもできます。あるエンティティにカーソルを合わせたときに、テキストラベルを表示させたい場合。


```html
<script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
<script src="https://unpkg.com/aframe-event-set-component@3.0.3/dist/aframe-event-set-component.min.js"></script>
<body>
  <a-scene>
    <a-cylinder position="1 0.75 -3" radius="0.5" height="1.5" color="#FFC65D"
                event-set__enter="_event: mouseenter; _target: #cylinderText; visible: true"
                event-set__leave="_event: mouseleave; _target: #cylinderText; visible: false">
      <a-text id="cylinderText" value="This is a cylinder" align="center" color="#FFF" visible="false" position="0 -0.55 0.55"
              geometry="primitive: plane; width: 1.75" material="color: #333"></a-text>
    </a-cylinder>

    <a-camera>
      <a-cursor></a-cursor>
    </a-camera>
  </a-scene>
</body>
```

イベントセットコンポーネントは、A-Frameコンポーネントのドットシンタックス（すなわち、`${componentName}.${propertyName}`）を用いて複数のプロパティを持つコンポーネントに対しても機能します。


```html
<script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
<script src="https://unpkg.com/aframe-event-set-component@3.0.3/dist/aframe-event-set-component.min.js"></script>
<body>
  <a-scene>
    <a-plane position="0 0 -4" rotation="-90 0 0" width="4" height="4" color="#7BC8A4"
             event-set__down="_event: mousedown; material.wireframe: true"
             event-set__up="_event: mouseup; material.wireframe: false"
             event-set__leave="_event: mouseleave; material.wireframe: false"></a-plane>

    <a-camera>
      <a-cursor></a-cursor>
    </a-camera>
  </a-scene>
</body>
```

### Handling Events With JavaScript

[Remix this cursor handler example on Glitch](https://glitch.com/~aframe-school-cursor-handler/)

[writingcomponent]: ./writing-a-component.md

イベントセットコンポーネントは、基本的な設定操作に適していますが、JavaScriptでイベントを処理する方法を知っておくことが重要です。イベントに応じて、より複雑な操作（APIの呼び出し、データの保存、アプリケーションの状態への影響など）を行いたいと思うかもしれません。そのような場合には、JavaScriptを使う必要がありますが、A-Frameでは、そのコードを[A-Frame components][writingcomponent] の中に置くように定めています。


イベントセットコンポーネントがどのような働きをするのか、JavaScriptを使って、ホバーしたときとホバーから離れたときにボックスの色を変えるようにしてみましょう。


```html
<script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
<script>
  AFRAME.registerComponent('change-color-on-hover', {
    schema: {
      color: {default: 'red'}
    },

    init: function () {
      var data = this.data;
      var el = this.el;  // <a-box>
      var defaultColor = el.getAttribute('material').color;

      el.addEventListener('mouseenter', function () {
        el.setAttribute('color', data.color);
      });

      el.addEventListener('mouseleave', function () {
        el.setAttribute('color', defaultColor);
      });
    }
  });
</script>
<body>
  <a-scene>
    <a-box color="#EF2D5E" position="0 1 -4" change-color-on-hover="color: blue"></a-box>

    <a-camera><a-cursor></a-cursor></a-camera>
  </a-scene>
</body>
```

ここでは単純な `.setAttribute` を行っていますが、JavaScript、three.js、Web API に完全にアクセスできるため、技術的にはイベントに応答してコンポーネント内で何でも行うことができます。

この後、VRコントローラのインタラクティブ機能の説明と実装に移りますが、イベントとイベントリスナーのコンセプトはそのまま適用されます。


## VR Controllers

VRアプリケーションに没入してもらうためには、コントローラーが不可欠です。VRのポテンシャルは、それら、すなわち6自由度（6DoF）を提供するコントローラーなしには満たされないのです。コントローラーがあれば、人は手を伸ばしてシーンの周りを回り、手でオブジェクトとインタラクションすることができます。


[gamepad]: https://developer.mozilla.org/docs/Web/API/Gamepad_API/Using_the_Gamepad_API

A-Frameは、[Gamepad Web API][gamepad]を通じて、それぞれのWebVRブラウザがサポートする、あらゆる分野のコントローラ用のコンポーネントを提供しています。Vive、Oculus Touch、Daydream、GearVR、Oculus Goの各コントローラのためのコンポーネントがあります。

Gamepadオブジェクトを覗いてみたり、Gamepad IDを取得したりするには、ブラウザコンソールで `navigator.getGamepads()` を呼び出すことができます。これは接続されている全てのコントローラを含む `GamepadList` 配列を返します。

高度なアプリケーションの場合、コントローラはアプリケーションに合わせて構築されます（カスタム3Dモデル、アニメーション、マッピング、ジェスチャーなど）。例えば、中世の騎士が金属のガントレットを持っていたり、ロボットがレーザーを撃ったり、手首に情報を表示したりするロボットハンドを持っていたりするのです。

A-Frameが提供するコントローラコンポーネントは、主に、デフォルト、スターターコンポーネント、または、よりカスタムなコントローラコンポーネントを派生させるためのベースとして機能します。

### tracked-controls Component

[trackedcontrols]: ../components/tracked-controls.md
[vivecontrols]: ../components/vive-controls.md

[tracked-controlsコンポーネント][trackedcontrols]は、A-Frameの全てのコントローラコンポーネントの土台となる、A-Frameのベースコントローラコンポーネントです。tracked-controlsコンポーネントは:


- ID またはプレフィックスを指定して、Gamepad API から Gamepad オブジェクトを取得します。
- ゲームパッドAPIのポーズ（位置と向き）を適用して、コントローラの動きを読み取る。
Gamepad オブジェクトのボタンの値の変化を探し、ボタンが押されたりタッチされた時や軸やタッチパッドが変更された時にイベントを提供します (例: `axischanged`, `buttonchanged`, `buttondown`, `buttonup`, `touchstart`, `touchend`)．

A-Frameのすべてのコントローラコンポーネントは、tracked-controlsコンポーネントの上に構築されています。

- 適切なゲームパッド ID (例: `Oculus Touch (Right)`) を持つエンティティに tracked-controls コンポーネントを設定します。例えば、[vive-controls component][vivecontrols] は `el.setAttribute('tracked-controls', {idPrefix: 'OpenVR'})`. tracked-controls はその後、適切な Gamepad オブジェクトに接続し、エンティティに対して姿勢とイベントを提供するようにします。
- tracked-controlsが提供するイベントの抽象化 tracked-controlsのイベントは低レベルなので、そのイベントだけでどのボタンが押されたかを判断するのは難しく、あらかじめボタンのマッピングを知る必要があります。コントローラコンポーネントは、それぞれのコントローラのマッピングを事前に知ることができ、 `triggerdown` や `xbuttonup` のようなよりセマンティックなイベントを提供することができます。
- モデルの提供。tracked-controls だけでは、外観を提供しない。コントローラコンポーネントは、ボタンが押されたりタッチされたりしたときに、視覚的なフィードバックやジェスチャー、アニメーションを表示するモデルを提供することができます。


以下のコントローラのコンポーネントは、ゲームパッドAPIでコントローラが検出され、接続されていることが確認された場合のみ起動されます。


### Adding 3DoF Controllers (daydream-controls, gearvr-controls, oculus-go-controls)

[dof]: http://www.roadtovr.com/introduction-positional-tracking-degrees-freedom-dof/

3自由度（3DoF）のコントローラは、回転方向のトラッキングに限定されています。つまり、手を伸ばしたり、前後や上下に動かしたりすることができないのです。3自由度しかないコントローラーは、腕のない手と手首のようなものです。[VRの自由度についてもっと読む][dof]。

3DoFコントローラ・コンポーネントは、回転トラッキング、現実のハードウェアにマッチしたデフォルト・モデル、ボタン・マッピングを抽象化するためのイベントを提供します。Google Daydream、Samsung GearVR、Oculus Goのコントローラは3DoFを搭載しており、いずれも片手用のコントローラ1つだけをサポートしています。


[daydreamcomponent]: ../components/daydream-controls.md

Google Daydream用のコントローラを追加するには、[daydream-controlsコンポーネント][daydreamcomponent]を使用します。そして、Daydreamスマートフォン上のAndroid用Chromeで試してみてください。

```html
<a-entity daydream-controls></a-entity>
```

[gearvrcomponent]: ../components/gearvr-controls.md

Samsung GearVR用のコントローラを追加するには、[gearvr-controls component][gearvrcomponent]を使用します。その後、GearVRを搭載したスマートフォンでOculus CarmelまたはSamsung Internetで試してみてください。

```html
<a-entity gearvr-controls></a-entity>
```

[oculusgocomponent]: ../components/oculus-go-controls.md

Oculus Go用のコントローラを追加するには、[oculus-go-controlsコンポーネント][oculusgocomponent]を使用します。その後、Oculus GoスタンドアロンヘッドセットでOculus BrowserまたはSamsung Internetで試してみてください。

```html
<a-entity oculus-go-controls></a-entity>
```

### Adding 6DoF Controllers (vive-controls, oculus-touch-controls)

6自由度※のコントローラーは、回転と位置の両方のトラッキングが可能です。向きに制約のある3DoFのコントローラーとは異なり、6DoFのコントローラーは、3次元空間を自由に動くことができるのです。6DoFでは、前方や背中に手を伸ばしたり、手を体全体に動かしたり、顔の近くに持っていったりすることが可能です。6DoFを持つことは、両手と両腕がある現実と同じです。6DoFは、ヘッドセットや追加のトラッカー（例：足、小道具）にも適用されます。6DoFを持つことは、真に没入感のあるVR体験を提供するための最低条件なのです。

6DoFコントローラ・コンポーネントは、完全なトラッキング、現実のハードウェアにマッチしたデフォルト・モデル、ボタン・マッピングを抽象化するためのイベントを提供します。HTC ViveとOculus Rift with Touchは、6DoFと両手用のコントローラを提供します。HTC Viveは、現実世界のオブジェクトをVRに追加してトラッキングするためのトラッカーも提供しています。

[rocks]: https://webvr.rocks
[vivecomponent]: ../components/vive-controls.md

HTC Vive用のコントローラを追加するには、両手用の[vive-controlsコンポーネント][vivecomponent]を使用します。その後、[WebVR対応デスクトップブラウザー][rocks]で試してみてください。

```html
<a-entity vive-controls="hand: left"></a-entity>
<a-entity vive-controls="hand: right"></a-entity>
```

[riftcomponent]: ../components/oculus-touch-controls.md

Oculus Touch用のコントローラを追加するには、両手用の[oculus-touch-controlsコンポーネント][riftcomponent]を使用します。その後、[WebVR対応デスクトップブラウザー][rocks]で試してみてください。


```html
<a-entity oculus-touch-controls="hand: left"></a-entity>
<a-entity oculus-touch-controls="hand: right"></a-entity>
```

## Supporting Multiple Types of Controllers

Webはマルチプラットフォームに対応できるというメリットがあります。しかし、3DoFプラットフォームと6DoFプラットフォームではインタラクションが異なり、異なるユーザーエクスペリエンス処理が必要なため、VRではマルチプラットフォームのサポートが何を意味するのかが明確ではありません。Web上のVRにとって「レスポンシブ」が何を意味するかは、アプリケーション次第でしょう。私たちが提示できるのは、いくつかの異なる方法ですが、本当に万能なものはありません。

### hand-controls Component

[handcontrols]: ../components/hand-controls.md
[lasercontrols]: ../components/laser-controls.md

A-Frameでは、[hand-controlsコンポーネント][handcontrols]を介して、複数の種類の6DoFコントローラ（Vive、Oculus Touch）に対応するための実装を提供しています。hand-controlsコンポーネントは、オブジェクトを掴むなどのルームスケールインタラクション向けなので、主に6DoFコントローラ向けです。hand-controlsコンポーネントは、ViveとOculus Touchの両方のコントローラの上で、以下の方法で動作します。


- vive-controlsとoculus-touch-controlsの両方のコンポーネントを設定します。
- シンプルなハンドモデルによるコントローラモデルのオーバーライド
- Vive 固有のイベントと Oculus Touch 固有のイベントをハンドイベントとジェスチャーにマッピング（例：`gripdown` と `triggerdown` を `thumbup` にマッピング）


hand-controlsコンポーネントを追加する。

```html
<a-entity hand-controls="left"></a-entity>
<a-entity hand-controls="right"></a-entity>
```

残念ながら、すべての種類の3DoFコントローラ（Daydream、GearVRなど）をうまく抽象化した3DoFコントローラコンポーネントはまだ存在しません。両方のコントローラーで動作するカスタムコントローラーを作ればいいのですが。3DoFコントローラはインタラクションの可能性をあまり提供しないので（つまり、タッチパッドによる回転トラッキングのみ）、かなりカバーしやすいと思います。

ただし、現在A-Frameが対応している6DoFおよび3DoFのコントローラをすべてカバーするコントローラコンポーネントがあります。[laser-controls][lasercontrols]です。


### Creating Custom Controllers

[handcontrolssource]: https://github.com/aframevr/aframe/blob/master/src/components/hand-controls.js

前述したように、アプリケーションは、体験に合わせてコントローラをカスタマイズするのが最適です。ほとんどのVRアプリケーションは、それぞれ独自のコントローラを備えています。これは、異なるモデル、アニメーション、ジェスチャー、ビジュアルフィードバック、そしてステートを意味します。

[hand-controlsソースコード][handcontrolssource]を見ることは、ゼロからすべてを行うことなく、カスタムコントローラを行う方法を理解するための適切な方法です。

- tracked-controlsコンポーネントは、ポーズとイベントを提供します。
- vive-controls、oculus-touch-controls、daydream-controls、またはgearvr-controlsコンポーネントは、ボタンマッピングのコントローラ固有のイベントを提供します。
- カスタムコントローラコンポーネントは、上記のすべてに加え、モデル、アニメーション、ビジュアルフィードバック、ステートなどをオーバーライドして構築されます。


最初のピースは、どのコントローラコンポーネントをサポートするかを設定することです。コントローラコンポーネントはトラッキングコントロールコンポーネントもインジェクトします。例えば、全てのコントローラをサポートするには、手を提供し、モデルをオーバーライドしながら、全てのコントローラコンポーネントを設定します。

```js
AFRAME.registerComponent('custom-controls', {
  schema: {
    hand: {default: ''},
    model: {default: 'customControllerModel.gltf'}
  },

  update: function () {
    var hand = this.data.hand;
    var el = this.el;
    var controlConfiguration = {
      hand: hand,
      model: false,
      orientationOffset: {x: 0, y: 0, z: hand === 'left' ? 90 : -90}
    };

    // Build on top of controller components.
    el.setAttribute('vive-controls', controlConfiguration);
    el.setAttribute('oculus-touch-controls', controlConfiguration);
    el.setAttribute('daydream-controls', controlConfiguration);
    el.setAttribute('gearvr-controls', controlConfiguration);
    el.setAttribute('windows-motion-controls', controlConfiguration);

    // Set a model.
    el.setAttribute('gltf-model', this.data.model);
  }
});
```

[a-blast]: https://github.com/aframevr/a-blast/blob/master/src/components/shoot-controls.js
[a-painter]: https://github.com/aframevr/a-painter/blob/master/src/components/paint-controls.js

実際のアプリケーションに関する高度な例は、[A-Painter用paint-controlsコンポーネント][a-painter]や[A-Blast用shoot-controlsコンポーネント][a-blast]をご覧ください。

## Listening for Button and Axis Events

コントローラにはたくさんのボタンがあり、たくさんのイベントを発します。それぞれのボタンに対して、ボタンが押されたり、離されたり、場合によってはタッチされたりするたびにです。また、各軸（トラックパッドやサムスティックなど）については、タッチされるたびにイベントが発生します。ボタンを処理するには、それぞれのコントローラコンポーネントのドキュメントページで、イベントテーブルからイベント名を探し、好きなようにイベントハンドラを登録します。

- [daydream-controls events](../components/daydream-controls.md#events)
- [gearvr-controls events](../components/gearvr-controls.md#events)
- [hand-controls events](../components/hand-controls.md#events)
- [oculus-touch-controls events](../components/oculus-touch-controls.md#events)
- [vive-controls events](../components/vive-controls.md#events)
- [windows-motion-controls events](../components/windows-motion-controls.md#events)

例えば、Oculus Touch の X ボタンが押されたことを聞き、エンティティの可視性をトグルすることができます。コンポーネント形式では

```js
AFRAME.registerComponent('x-button-listener', {
  init: function () {
    var el = this.el;
    el.addEventListener('xbuttondown', function (evt) {
      el.setAttribute('visible', !el.getAttribute('visible'));
    });
  }
});
```

その後、コンポーネントを取り付けます。

```html
<a-entity oculus-touch-controls x-button-listener></a-entity>
```

## Adding Laser Interactions for Controllers

[laser-controls]: ../components/laser-controls.md

レーザーインタラクションとは、コントローラから可視のレイキャスター（線）を射出することを指します。線と線が交差するとき、交差中にコントローラーのボタンが変化するとき、線と線が交差しなくなったときに、インタラクションが発生します。このインタラクションは、レイキャスターがヘッドセットではなくコントローラに固定されていることを除けば、視線ベースのインタラクションに非常に似ています。

[laser-controls component][laser-controls] コンポーネントは、コントローラ用のレーザーインタラクションを提供します。使い方はカーソルコンポーネントとほぼ同じですが、コンポーネントをカメラの下ではなくコントローラーに貼り付けます。

```html
<a-entity laser-controls="hand: right"></a-entity>
```

[raycasterfar]: ../components/raycaster.html#properties_far

そして、レーザーのデフォルトの長さは、[レイキャスターの長さを調整する][レイキャスターファー]で設定されます。レーザーがエンティティに交差する場合、レーザーの長さは切り捨てられます。

```html
<a-entity hand-controls laser-controls raycaster="far: 2"></a-entity>
```

[gaze]: #gaze-based-interactions-with-cursor-component

そうすると、イベントやインタラクションの処理は、[カーソルコンポーネントによる注視型インタラクション][gaze]と全く同じです。上記のセクションを参照してください

## Adding Room Scale Interactions for Controllers

ルームスケールのインタラクションはもっと難しい。3次元空間での相互作用や、つかむ、投げる、伸ばす、打つ、回す、引く、押すなどの両手での相互作用が含まれる。ルームスケールインタラクションの数や複雑さは、私たちが完全にカバーできるものではありません。マウスとタッチスクリーンだけの2Dウェブや、コントローラを振るだけの3DoF VRとは異なります。しかし、そのまま、あるいはリファレンスとして使えるような様々な実装を示すことはできます。

ルームスケールや3Dのインタラクションでは、レイキャスターを使ってオブジェクトとの交わりを検出するのではなく、*コライダー*を使用します。レイキャスターが2Dのラインであるのに対し、コライダーは3Dのボリュームです。コライダーには、オブジェクトを包み込むさまざまな形状（AABB/ボックス、球、メッシュ）があり、それらの形状が交差すると、衝突が検出されます。

### super-hands Component

[superhands]: https://github.com/wmurphyrd/aframe-super-hands-component
[superhandsdocs]: https://github.com/wmurphyrd/aframe-super-hands-component#description
[superhandsex]: https://wmurphyrd.github.io/aframe-super-hands-component/examples/

William Murphy氏による[super-handsコンポーネント][superhands]は、オールインワンで自然なハンドコントローラとのインタラクションを提供します。このコンポーネントは、追跡されたコントローラや衝突検出コンポーネントからの入力をインタラクションジェスチャーに解釈し、それらのジェスチャーをターゲットエンティティに伝達し、応答させることができます。

現在実装されているジェスチャーは以下の通りです。

- Hover: コントローラをエンティティの衝突空間に保持する。
- Grab: ホバリング中にボタンを押し、移動させる。
- Stretch: 両手でエンティティをつかみ、サイズを変更する。
- Drag-drop: エンティティを他のエンティティにドラッグする

super-handsのジェスチャーに反応するためには、ジェスチャーをアクションに変換するコンポーネントが必要です。super-handsには、hoverable, grabbable, stretchable, drag-droppableという、実装されているジェスチャーに対する典型的な反応をするコンポーネントが含まれています。

super-hands のドキュメント][superhandsdocs] と
[例][superhandsex]は、入門に最適です。私たちはそれらに従います。

### Other Examples

[aframe-extras]: https://github.com/donmccurdy/aframe-extras
[aframe-physics]: https://github.com/donmccurdy/aframe-physics-system

Other examples to look at include:

- [tracked-controls](https://github.com/aframevr/aframe/tree/master/examples/showcase/tracked-controls) - Interaction through sphere-collider and grab components.
- [ball-throw](https://github.com/bryik/aframe-ball-throw) - Grab and throw using [aframe-extras] and [aframe-physics].
- [vr-editor](https://github.com/caseyyee/aframe-vreditor-component) - Interaction through a single vr-editor component for cloning, moving, deleting, placing, and scaling.

