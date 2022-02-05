---
title: Entity-Component-System
type: introduction
layout: docs
order: 5
examples:
  - title: Community Components in Action
    src: https://glitch.com/edit/#!/aframe-registry?path=index.html
  - title: Animated Lights
    src: https://glitch.com/edit/#!/aframe-animated-lights?path=index.html
---

[ecs]: https://wikipedia.org/wiki/Entity_component_system

A-Frameは、[entity-component-system][ecs](ECS)アーキテクチャを採用したthree.jsのフレームワークです。
ECSアーキテクチャは、3Dやゲーム開発において一般的で望ましいパターンであり、**継承と階層より合成**という原則に従っています。

ECSのメリットは以下の通りです。

1. 再利用可能なパーツを組み合わせてオブジェクトを定義する際の柔軟性が高い。
2. 機能が複雑に絡み合った長い継承の連鎖の問題が解消される。
3. デカップリング、カプセル化、モジュール化、再利用性によるクリーンな設計を促進する。
4. 複雑さの観点から、VRアプリケーションを構築する上で最もスケーラブルな方法です。
5. 3DやVRの開発で実績のあるアーキテクチャ。
6. 新機能の拡張が可能（コミュニティコンポーネントとして共有できる可能性がある）。

2DのWebでは、決まった動作をする要素を階層的に並べます。
3DやVRはそれとは異なり、無限の振る舞いを持つオブジェクトが無数に存在する可能性があります。
ECSは、オブジェクトのタイプを構築するための管理しやすいパターンを提供します。

ECSアーキテクチャの入門に最適な資料をご紹介します。ざっと目を通して、その良さを理解することをお勧めします。ECSはVRの開発に適しており、A-Frameはこのパラダイムに完全に基づいています。

- [*Evolve Your Hierarchy* by Mick West](http://cowboyprogramming.com/2007/01/05/evolve-your-heirachy/)

- [Wikipediaの*Entity-component-system*](https://ja.wikipedia.org/wiki/エンティティ・コンポーネント・システム)
- [Adam Martinによる *「What is an Entity System?」*](http://t-machine.org/index.php/2007/11/11/entity-systems-are-the-future-of-mmog-development-part-2/)
- [*Decoupling Patterns &mdash; Component* on Game Programming Patterns](http://gameprogrammingpatterns.com/component.html)
- [Mick Westによる *「Evolve Your Hierarchy」*](http://cowboyprogramming.com/2007/01/05/evolve-your-heirachy/)

ECSを実装したゲームエンジンとしては、Unityが有名です。
けれどエンティティ間のコミュニケーションに難点があるため、A-FrameやDOM、宣言型HTMLがECSをいかに輝かせるか、その一端を見ることができます。

<!--toc-->

## Concept

[entity]: ../core/entity.md
[component]: ../core/component.md
[system]: ../core/systems.md

![ECS Minecraft](https://cloud.githubusercontent.com/assets/674727/20289898/71f7fea0-aa91-11e6-8307-d8dc68dff285.png)

ECSの基本的な定義は以下の通りです。

- **[エンティティ][entity]** は、コンポーネントをアタッチできるコンテナオブジェクトです。エンティティは、シーン内のすべてのオブジェクトのベースです。コンポーネントがないと、エンティティは空の `<div>`と同様に、何もせず、何もレンダリングしません。
- **[コンポーネント][component]** は、再利用可能なモジュールまたはデータコンテナで、外観、動作、および/または機能を提供するためにエンティティにアタッチすることができます。コンポーネントは、オブジェクトのプラグアンドプレイのようなものです。すべてのロジックはコンポーネントによって実装され、コンポーネントを組み合わせ、構成することによって、さまざまなタイプのオブジェクトを定義することができます。錬金術みたいですね！

- **[システム][system]** は、コンポーネントのクラスに対して、グローバルなスコープ、管理、サービスを提供します。システムは多くの場合オプションですが、ロジックとデータを分離するために使用できます。システムはロジックを処理し、コンポーネントはデータのコンテナとして機能します。


### 例

Some abstract examples of different types of entities built from composing together different components:

異なるコンポーネントを組み合わせて作られた、さまざまなタイプのエンティティの抽象的な例をいくつか紹介します。

- `ボックス = ポジション + ジオメトリ + マテリアル`
- `電球 = ポジション + 光源 + ジオメトリ + マテリアル + 影`
- `看板 = ポジション + ジオメトリ + マテリアル + テキスト`
- `VRコントローラー = ポジション + 回転 + インプット + モデル + 掴む + ジェスチャー`
- `ボール = ポジション + 速度 + 物理演算 + ジオメトリ + マテリアル`
- `プレイヤー = ポジション + カメラ + インプット + アバター + アイデンティティ`

別の抽象的な例として、コンポーネントを組み立てて自動車の実体を作る場合を考えてみましょう。

- 「色」や「輝き」など、車の外観に影響を与えるプロパティを持つ`マテリアル`コンポーネントをアタッチできます。
- 「馬力」や「重量」など、車の機能に影響を与えるプロパティを持つ`エンジン`コンポーネントをアタッチできます。
- 「タイヤの数」や「ステアリングの角度」など、車の挙動に影響を与えるプロパティを持つ`タイヤ`コンポーネントをアタッチできます。

つまり、`マテリアル`、`エンジン`、`タイヤ`コンポーネントのプロパティを変えることで、様々なタイプの自動車を作ることができるのです。
`マテリアル`、`エンジン`、`タイヤ`の各コンポーネントはお互いを知る必要はなく、単独で他のケースに使用することも可能です。
これらを組み合わせて、さらに異なるタイプの乗り物を作ることができます。

- *ボート*のエンティティの作成: `タイヤ`コンポーネントを削除します。
- *モーターサイクル*のエンティティの作成: `タイヤ`コンポーネントのタイヤの数を2つに変更し、`エンジン`コンポーネントをより小さく構成します。
- *飛行機*のエンティティの作成: `ウィング`コンポーネントと`ジェット`コンポーネントを追加します。

従来の継承では、あるオブジェクトを拡張しようとすると、すべてを扱おうとする大きなクラスを作る、または継承の連鎖を作らなければならなかったのとは対照的です。

## A-FrameのECS

A-Frameは、ECSの各パーツを表すAPIを持っています。

- **エンティティ** は `<a-entity>` 要素とprototypeで表現されます。
- **コンポーネント**は、`<a-entity>`のHTML属性で表現されます。その下に、スキーマ、ライフサイクルハンドラ、メソッドを含むオブジェクトがあります。コンポーネントは `AFRAME.registerComponent (name, definition)` API を用いて登録されます。
- **システム**は `<a-scene>` の HTML 属性で表現されます。システムはコンポーネントと似た定義を持っています。システムは `AFRAME.registerSystem (name, definition)` API を用いて登録されます。

### 文法

[style]: https://developer.mozilla.org/docs/Web/API/HTMLElement/style

`<a-entity>` を作成し、HTMLの属性としてコンポーネントをアタッチします。
ほとんどのコンポーネントは複数のプロパティを持ち、それらは [`HTMLElement.style` CSS][style]のような構文で表現されます。
この構文は、コロン (`:`) でプロパティ名とプロパティ値を区切り、セミコロン (`;`) で異なるプロパティの宣言を区切る形式をとります。


`<a-entity ${componentName}="${propertyName1}: ${propertyValue1}; ${propertyName2}: ${propertyValue2}">`

[geometry]: ../components/geometry.md
[material]: ../components/material.md
[light]: ../components/light.md
[position]: ../components/position.md

例えば、以下では`<a-entity>`を用意し、[geometry], [material], [light], [position]コンポーネントを様々なプロパティとプロパティ値でアタッチしています。

```html
<a-entity geometry="primitive: sphere; radius: 1.5"
          light="type: point; color: white; intensity: 2"
          material="color: white; shader: flat; src: glow.jpg"
          position="0 0 -5"></a-entity>
```

### 合成

そこから、さらにコンポーネントを追加して、外観、動作、機能（物理演算など）を追加することができます。
また、コンポーネントの値を更新してエンティティを構成することもできます（宣言的に、または `.setAttribute` を使って）。


[composegif]: https://cloud.githubusercontent.com/assets/674727/25463804/896c04c2-2aad-11e7-8015-2fc84118a01c.gif

![Composing an Entity][composegif]

複数のコンポーネントから構成されるエンティティとしてよく知られているのが、VRにおけるプレイヤーの手です。
プレイヤーの手は、外観、ジェスチャー、振る舞い、他のオブジェクトとのインタラクティビティなど、多くの構成要素を持つことがあります。

VRの超能力やオーグメントを装着するように、手のエンティティにコンポーネントをプラグインして振る舞いを提供します。
以下の各コンポーネントは、お互いに何の関係もありませんが、組み合わせることで複雑な実体を定義することができます。

```html
<a-entity
  tracked-controls  <!-- ポーズ用のゲームパッドAPIにフック -->
  vive-controls  <!-- Viveのボタンマッピング -->
  oculus-touch-controls  <!-- Oculusのボタンマッピング -->
  hand-controls  <!-- 外観（モデル）、ジェスチャー、イベント -->
  laser-controls <!-- メニューやUIを操作するためのレーザー -->
  sphere-collider  <!-- 手を物に接触しているかリッスンする -->
  grab  <!-- 物をつかむ能力を提供 -->
  throw <!-- 物を投げる機能を提供 -->
  event-set="_event: grabstart; visible: false"  <!-- 物をつかむときに手を隠す -->
  event-set="_event: grabend; visible: true"  <!-- オブジェクトを掴まなくなったときに手を表示する -->
>
```

### 宣言型DOMベースのECS

A-Frame takes ECS to another level by making it declarative and based on the
DOM. Traditionally, ECS-based engines would create entities, attach components,
update components, remove components all through code. But A-Frame has HTML and
the DOM which makes ECS ergonomic and resolves many of its weaknesses. Below
are abilities that the DOM provides for ECS:

A-Frameは、ECSを宣言型にし、DOMをベースにすることで、新たな次元に到達させました。
従来、ECSベースのエンジンは、エンティティの作成、コンポーネントのアタッチ、コンポーネントの更新、コンポーネントの削除をすべてコードで行っていました。
しかし、A-Frameでは、HTMLとDOMを利用することで、ECSを人間工学に基づいたものにし、その弱点の多くを解決しています。
以下は、DOMがECSに提供する能力です。

1. **クエリセレクターによる他のエンティティへの参照**: DOMには強力なクエリセレクターシステムがあり、シーングラフにクエリを行って、条件にマッチするエンティティを選択することができます。ID、クラス、データ属性によってエンティティへの参照を取得することができます。A-FrameはHTMLをベースにしているので、クエリーセレクタをそのまま使うことができます。`document.querySelector('#player')`

2. **疎結合な、イベント駆動のエンティティ間やりとり**: DOMはイベントを監視し、発出する機能を提供します。これは、エンティティ間のPub/Subコミュニケーションシステムを提供します。コンポーネントは互いのことを知る必要はなく、ただイベントを発することができ（バブルアップする可能性もある）、他のコンポーネントは互いを呼び出すことなくそれらのイベントを監視することができるのです。`ball.emit('collided')`
3. **DOM APIによるライフサイクル管理のためのAPI**: DOM は HTML の要素やツリーを更新するための API を提供しており、 `.setAttribute`、`.removeAttribute`、`.createElement`、`.removeChild` などがこれにあたります。これらは通常の Web 開発と同様に、そのまま使用することができます。

4. **属性セレクタを用いたエンティティフィルタリング**: DOM には属性セレクタがあり、特定の HTML 属性を持つエンティティや持たないエンティティをクエリすることができます。つまり、特定のコンポーネントを持つエンティティ、持たないエンティティを問い合わせることができるのです。
`document.querySelector('[enemy]:not([alive])')`

5. **宣言性**: 最後に、DOMはHTMLを提供します。A-Frameは、ECSとHTMLの橋渡しをし、すでにきれいなパターンを宣言的に、読みやすく、そしてコピー＆ペーストが可能です。


### 拡張性

A-Frame components can do anything. Developers are given permissionless
innovation to create components to extend any feature. Components have full
access to JavaScript, three.js, and Web APIs (e.g., WebRTC, Speech
Recognition).

[writecomponent]: ./writing-a-component.md

We will later go over in detail how to [write an A-Frame
component][writecomponent]. As a preview, the structure of a basic component
may look like:

```js
AFRAME.registerComponent('foo', {
  schema: {
    bar: {type: 'number'},
    baz: {type: 'string'}
  },

  init: function () {
    // Do something when component first attached.
  },

  update: function () {
    // Do something when component's data is updated.
  },

  remove: function () {
    // Do something the component or its entity is detached.
  },

  tick: function (time, timeDelta) {
    // Do something on every scene tick or frame.
  }
});
```

Declarative ECS grants us the ability to write a JavaScript module and abstract
it through HTML. Once the component is registered, we can declaratively plug
this module of code into an entity via an HTML attribute.  This code-to-HTML
abstraction makes ECS powerful and easy to reason. `foo` is the name of the
component we just registered, and the data contains `bar` and `baz` properties:

```html
<a-entity foo="bar: 5; baz: bazValue"></a-entity>
```

### Component-Based Development

**For building VR applications, we recommend placing all application code
within components (and systems).** An ideal A-Frame codebase consists purely of
modular, encapsulated, and decoupled components. These components can be unit
tested in isolation or alongside other components.

When an application is created solely with components, all parts of its
codebase become reusable! Components can be shared for other developers to use
or we can reuse them in our other projects. Or the components can be forked and
modified to adapt to other use cases.

A simple ECS codebase might be structured like:

```
index.html
components/
  ball.js
  collidable.js
  grabbable.js
  enemy.js
  scoreboard.js
  throwable.js
```

### Higher-Order Components

[cursor]: ../components/cursor.md
[hand-controls]: ../components/hand-controls.md
[oculus-touch-controls]: ../components/oculus-touch-controls.md
[raycaster]: ../components/raycaster.md
[tracked-controls]: ../components/tracked-controls.md
[vive-controls]: ../components/vive-controls.md

Components can set other components on the entity, making them a higher-order
or higher-level component in abstraction.

For example, the [cursor component][cursor] sets and builds on top of the
[raycaster component][raycaster]. Or the [hand-controls
component][hand-controls] sets and builds on top of the [vive-controls
component][vive-controls] and [oculus-touch-controls
component][oculus-touch-controls] which in turn build on top of the
[tracked-controls component][tracked-controls].

## Community Component Ecosystem

Components can be shared into the A-Frame ecosystem for the community to use.
The wonderful thing about A-Frame's ECS is extensibility. An experienced
developer can develop a physics system or graphics shader components, and an
novice developer can take those components and use them in their scene from
HTML just by dropping in a `<script>` tag. We can use powerful published
components without having to touch JavaScript.

### Where to Find Components

There are hundreds of components out in the wild. We try our best to make them
discoverable. If you develop a component, please submit it through these
channels to share!

#### npm

[search]: https://www.npmjs.com/search?q=aframe-component

Most A-Frame components are published on npm as well as GitHub. We can use
[npm's search to search for `aframe-components`][search]. npm lets us sort by
quality, popularity, and maintenance. This is a great place to look for a more
complete list of components.

#### GitHub Projects

[github]: https://github.com

Many A-Frame applications are developed purely from components, and many of
those A-Frame applications are open source on [GitHub]. Their codebases will
contain components that we can use directly, refer to, or copy from. Projects
to look at include:

- [BeatSaver Viewer](https://github.com/supermedium/beatsaver-viewer/)
- [Super Says](https://github.com/supermedium/supersays/)
- [A-Painter](https://github.com/aframevr/a-painter/)
- [A-Blast](https://github.com/aframevr/a-blast/)

#### A-Frame Blog

[blog]: https://aframe.io/blog/
[homepage]: https://aframe.io/

The A-Frame Blog archives include details of components as they were released or updated, and can be a good place to find links to components.

#### A-Frame Wiki

The [A-Frame Wiki](https://aframe.wiki/) is a useful community-driven initiative that collects information and tips about available A-Frame components. Everyone is encouraged to participate. It's very easy to add and edit information.

### Using a Community Component

[particlesystem]: https://www.npmjs.com/package/aframe-particle-system-component

Once we find a component that we want to use, we can include the component as a
`<script>` tag and use it from HTML.

[unpkg.com]: http://unpkg.com/

For example, let's use IdeaSpaceVR's [particle system component][particlesystem]:

#### Using unpkg

First, we have to grab a CDN link to the component JS file. The documentation
of the component will usually have a CDN link or usage information. But a way
to get the most up-to-date CDN link is to use [unpkg.com].

unpkg is a CDN that automatically hosts everything that is published to npm.
unpkg can resolve semantic versioning and provide us the version of the
component we want. A URL takes the form of:

```
https://unpkg.com/<npm package name>@<version>/<path to file>
```

If we want the latest version, we can exclude the `version`:

```
https://unpkg.com/<npm package name>/<path to file>
```

Rather than typing in the path to the built component JS file, we can exclude
`path to file` to be able to browse the directories of the component package.
The JS file will usually be in a folder called `dist/` or `build/` and end with
`.min.js`.

For the particle system component, we head to:

```
https://unpkg.com/aframe-particle-system-component/
```

Note the ending slash (`/`). Find the file we need, right click, and hit *Copy
Link to Address* to copy the CDN link into our clipboard.

![unpkg](https://cloud.githubusercontent.com/assets/674727/25502028/cbfd7b3a-2b49-11e7-914d-a8280aa47ace.jpg)

#### Including the Component JS File

Then head to our HTML. Under the `<head>`, *after* the A-Frame JS `<script>`
tag, and *before* `<a-scene>`, we will include our JS file with a `<script>`
tag.

For the particle system component, the CDN link we found earlier (at time of
writing) was:

```
https://unpkg.com/aframe-particle-system-component@1.0.9/dist/aframe-particle-system-component.min.js
```

Now we can include it into our HTML:

```html
<html>
  <head>
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
    <script src="https://unpkg.com/aframe-particle-system-component@1.0.9/dist/aframe-particle-system-component.min.js"></script>
  </head>
  <body>
    <a-scene>
    </a-scene>
  </body>
</html>
```

#### Using the Component

Follow the documentation of the component on how to use it in implementation.
But generally, the usage involves attaching the component to an entity and
configuring it. For the particle system component:

Now we can include it into our HTML:

```html
<html>
  <head>
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
    <script src="https://unpkg.com/aframe-particle-system-component@1.0.9/dist/aframe-particle-system-component.min.js"></script>
  </head>
  <body>
    <a-scene>
      <a-entity particle-system="preset: snow" position="0 0 -10"></a-entity>
    </a-scene>
  </body>
</html>
```

### Example

[glitch]: http://glitch.com/~aframe-registry

![Registry Example](https://cloud.githubusercontent.com/assets/674727/25502318/0f76ceec-2b4b-11e7-9829-cb3784b20dc1.gif)

Below is a complete example of using various community components from the
Registry and using the unpkg CDN. We can [remix or check out this example on
Glitch][glitch].

```html
<html>
  <head>
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
    <script src="https://unpkg.com/aframe-animation-component@3.2.1/dist/aframe-animation-component.min.js"></script>
    <script src="https://unpkg.com/aframe-particle-system-component@1.0.x/dist/aframe-particle-system-component.min.js"></script>
    <script src="https://unpkg.com/aframe-extras.ocean@%5E3.5.x/dist/aframe-extras.ocean.min.js"></script>
    <script src="https://unpkg.com/aframe-gradient-sky@1.2.0/dist/gradientsky.min.js"></script>
  </head>
  <body>
    <a-scene>
      <a-entity id="rain" particle-system="preset: rain; color: #24CAFF; particleCount: 5000"></a-entity>

      <a-entity id="sphere" geometry="primitive: sphere"
                material="color: #EFEFEF; shader: flat"
                position="0 0.15 -5"
                light="type: point; intensity: 5"
                animation="property: position; easing: easeInOutQuad; dir: alternate; dur: 1000; to: 0 -0.10 -5; loop: true"></a-entity>

      <a-entity id="ocean" ocean="density: 20; width: 50; depth: 50; speed: 4"
                material="color: #9CE3F9; opacity: 0.75; metalness: 0; roughness: 1"
                rotation="-90 0 0"></a-entity>

      <a-entity id="sky" geometry="primitive: sphere; radius: 5000"
                material="shader: gradient; topColor: 235 235 245; bottomColor: 185 185 210"
                scale="-1 1 1"></a-entity>

      <a-entity id="light" light="type: ambient; color: #888"></a-entity>
    </a-scene>
  </body>
</html>
```
