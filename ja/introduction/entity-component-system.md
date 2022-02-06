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

A-Frameのコンポーネントは何でもできます。
開発者は、あらゆる機能を拡張するコンポーネントを作成するための制約のないイノベーションを与えられます。
コンポーネントは、JavaScript、three.js、Web API（例：WebRTC、音声認識）にフルアクセスできます。


[writecomponent]: ./writing-a-component.md

[A-Frameコンポーネントの書き方][writecomponent]は、後ほど詳しく説明します。
プレビューとして、基本的なコンポーネントの構造は、次のようになります。

```js
AFRAME.registerComponent('foo', {
  schema: {
    bar: {type: 'number'},
    baz: {type: 'string'}
  },

  init: function () {
    // コンポーネントが最初にアタッチされたときの処理
  },

  update: function () {
    // コンポーネントのデータが更新されたときの処理
  },

  remove: function () {
    // コンポーネントまたはそのエンティティが切り離されたときの処理
  },

  tick: function (time, timeDelta) {
    // シーンのティックやフレームごとの処理
  }
});
```

宣言型ECSは、JavaScriptのモジュールを書き、それをHTMLで抽象化する機能を提供します。
コンポーネントが登録されると、このコードモジュールをHTML属性を通じて宣言的にエンティティにプラグインすることができます。
この「コードからHTMLへの抽象化」により、ECSは強力かつ簡単に推論可能になります。
`foo` は先ほど登録したコンポーネントの名前であり、データには `bar` と `baz` というプロパティが含まれます。

```html
<a-entity foo="bar: 5; baz: bazValue"></a-entity>
```

### コンポーネントベースの開発

**VRアプリケーションを構築する場合、すべてのアプリケーションコードをコンポーネント（およびシステム）内に配置することを推奨します。**
理想的なA-Frameのコードベースは、モジュール化され、カプセル化され、切り離されたコンポーネント（とシステム）のみで構成されています。
これらのコンポーネントは、単独で、あるいは他のコンポーネントと一緒にユニットテストすることができます。

アプリケーションをコンポーネントだけで作成すると、そのコードベースのすべての部分が再利用可能になります！
コンポーネントは、他の開発者が使えるように共有することもできますし、私たちの他のプロジェクトで再利用することもできます。
また、コンポーネントをフォークして、他のユースケースに適応するように修正することも可能です。

単純なECSのコードベースは、以下のような構造です。

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

### 高階コンポーネント

[cursor]: ../components/cursor.md
[hand-controls]: ../components/hand-controls.md
[oculus-touch-controls]: ../components/oculus-touch-controls.md
[raycaster]: ../components/raycaster.md
[tracked-controls]: ../components/tracked-controls.md
[vive-controls]: ../components/vive-controls.md

Components can set other components on the entity, making them a higher-order or higher-level component in abstraction.

コンポーネントは、エンティティに他のコンポーネントを設定することができ、抽象化された高階または上位のコンポーネントを作ります。

例えば、[cursorコンポーネント][cursor]は[raycaster コンポーネント][raycaster]の上に設定・構築されます。
また、[hand-controls コンポーネント][hand-controls] は [vive-controls
コンポーネント][vive-controls] と [oculus-touch-controls
コンポーネント][oculus-touch-controls] の上に設定・構築され、そしてそれらは [tracked-controls コンポーネント][tracked-controls] の上に設定・構築されます。

## コミュニティ・コンポーネント・エコシステム

コンポーネントは、A-Frameのエコシステムに共有され、コミュニティで使用することができます。
A-FrameのECSの素晴らしいところは、拡張性です。
経験豊富な開発者が物理システムやグラフィックシェーダーコンポーネントを開発し、初心者の開発者は、`<script>`タグを設置するだけで、HTMLからそれらのコンポーネントを取り出し、シーンで使用することができるのです。
JavaScriptに触れることなく、強力な公開コンポーネントを利用することができるのです。

### Where to Find Components

There are hundreds of components out in the wild. We try our best to make them
discoverable. If you develop a component, please submit it through these
channels to share!

#### npm

[search]: https://www.npmjs.com/search?q=aframe-component

A-Frameのコンポーネントの多くは、GitHubだけでなく、npmでも公開されています。
npmで[ `aframe-components` と検索する][search]ことができます。
npmでは品質、人気、メンテナンス性でソートすることができます。
これは、より完全なコンポーネントのリストを探すのに最適な場所です。


#### GitHubプロジェクト

[github]: https://github.com

A-Frameのアプリケーションは、純粋にコンポーネントのみで開発されているものが多く、それらのA-Frameアプリケーションの多くは、[GitHub]でオープンソース化されています。
それらのコードベースには、私たちが直接使用したり、参照したり、コピーしたりすることができるコンポーネントが含まれていることでしょう。注目すべきプロジェクトは以下の通りです。

- [BeatSaver Viewer](https://github.com/supermedium/beatsaver-viewer/)
- [Super Says](https://github.com/supermedium/supersays/)
- [A-Painter](https://github.com/aframevr/a-painter/)
- [A-Blast](https://github.com/aframevr/a-blast/)

#### A-Frameブログ

[blog]: https://aframe.io/blog/
[homepage]: https://aframe.io/

A-Frameブログのアーカイブには、公開・更新されたコンポーネントの詳細が掲載されており、コンポーネントへのリンクを探すのに適しています。

#### A-Frame Wiki

[A-Frame Wiki](https://aframe.wiki/)は、利用可能なA-Frameコンポーネントに関する情報やTipsを収集する、コミュニティ主導の便利な取り組みです。
誰でも参加することができます。
情報の追加や編集はとても簡単です。

### コミュニティ・コンポーネントを使う

[particlesystem]: https://www.npmjs.com/package/aframe-particle-system-component

使いたいコンポーネントが見つかったら、そのコンポーネントを `<script>` タグとしてインクルードし、HTMLから使用することができます。

[unpkg.com]: http://unpkg.com/

例えば、IdeaSpaceVRの[パーティクルシステムコンポーネント][particlesystem]を使ってみましょう。

#### unpkgを使う

まず、コンポーネントのJSファイルへのCDNリンクを取得する必要があります。
コンポーネントのドキュメントには、通常、CDNリンクや使用法が記載されています。
しかし、最新のCDNリンクを取得する方法として、[unpkg.com]を使用する方法があります。

unpkgは、npmに公開されたすべてのものを自動的にホストするCDNです。
unpkgは、セマンティックバージョン管理を解決して、私たちが望むコンポーネントのバージョンを提供します。
URLは以下のような形式をとります。

```
https://unpkg.com/<npm package name>@<version>/<path to file>
```

最新版が欲しい場合は、`version`を消します。

```
https://unpkg.com/<npm package name>/<path to file>
```

ビルドされたコンポーネントのJSファイルへのパスを入力する代わりに、`path to file`を消すことで、コンポーネントパッケージのディレクトリをブラウズできるようになります。
JSファイルは通常、 `dist/` または `build/` というフォルダにあり、末尾が `.min.js` になっているはずです。


パーティクルシステムコンポーネントは、次のリンクになります。

```
https://unpkg.com/aframe-particle-system-component/
```

最後のスラッシュ（`/`）に注意してください。必要なファイルを見つけて右クリックし、*Copy Link to Address* を押して CDN リンクをクリップボードにコピーします。

![unpkg](https://cloud.githubusercontent.com/assets/674727/25502028/cbfd7b3a-2b49-11e7-914d-a8280aa47ace.jpg)

#### コンポーネントJSファイルを読み込む

そして、HTMLに向かいます。`<head>` の下、A-Frame JS の `<script>` タグの *後*、そして `<a-scene>` の *前* に、JS ファイルを `<script>` タグで読み込んでください。

パーティクルシステムコンポーネントの、先ほど（執筆時）見つけたCDNリンクは次です。

```
https://unpkg.com/aframe-particle-system-component@1.0.9/dist/aframe-particle-system-component.min.js
```

これをHTMLに読み込みましょう。

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

#### コンポーネントを使う

どのように実装して使うのかは、コンポーネントのドキュメントに従ってください。
ですが一般的には、コンポーネントをエンティティにアタッチして設定する使い方が多いです。
パーティクルシステムコンポーネントの場合は次のようになります。

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

### 例

[glitch]: http://glitch.com/~aframe-registry

![Registry Example](https://cloud.githubusercontent.com/assets/674727/25502318/0f76ceec-2b4b-11e7-9829-cb3784b20dc1.gif)

Below is a complete example of using various community components from the Registry and using the unpkg CDN. We can [remix or check out this example on Glitch][glitch].

以下は、レジストリにあるさまざまなコミュニティコンポーネントを使用し、unpkg CDNを使用した完全な例です。
私たちは、この例を[リミックスしたり、Glitchでチェックアウトする][glitch]ことができます。

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
