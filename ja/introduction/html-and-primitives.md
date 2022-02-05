---
title: "HTML & Primitives"
type: introduction
layout: docs
order: 4
examples: []
---

[component]: ../core/component.md
[entity]: ../core/entity.md

[dom]: https://developer.mozilla.org/docs/Web/API/Document_Object_Model
[html]: https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started

このセクションでは、A-Frameのプリミティブ要素の概念と、プリミティブ要素のエンティティコンポーネントフレームワークとの関係について説明します。HTMLとプリミティブの使い方については、[*Building a Basic Scene*ガイド](../guides/building-a-basic-scene.md)をご覧ください。

<!--toc-->

## HTML

A-Frameは、[HTML][html] と [DOM][dom] の上に、カスタム要素用のポリフィルを使用して構築されたものです。
HTMLはWebの構成要素であり、最もアクセスしやすいコンピューティング言語の一つです。
インストールやビルドの必要がなく、HTMLで作成するには、HTMLファイルにテキストを記述し、ブラウザでそのHTMLファイルを開くだけです。
WebのほとんどはHTMLの上に構築されているので、React, Vue.js, Angular, d3.js, jQueryなど、既存のツールやライブラリのほとんどがA-Frameに対応しています。

![HTML Scene](https://user-images.githubusercontent.com/674727/52090525-79b04d80-2566-11e9-993f-7a8b19ca25b1.png)

HTMLの経験があまりない方でも、問題ありません！
2DのHTMLページより簡単に理解できるかもしれません。
HTMLの一般的な構造や構文（開始タグ、属性、終了タグ）さえ覚えてしまえば、もう大丈夫です。[MDNでHTMLの入門を読む][html]。

![HTML](https://user-images.githubusercontent.com/6694476/27047689-94689672-4fc6-11e7-9cf5-828a508c6522.jpg)

## Primitives

HTML層はA-Frameの基礎のようなものに見えますが、HTMLとDOMはA一番外側の抽象化された層に過ぎません。
その下にあるA-Frameは、宣言的に公開されるthree.jsのためのエンティティコンポーネントフレームワークなのです。

A-Frameは、`<a-box>` や `<a-sky>` といった、初心者にもわかりやすいようにエンティティコンポーネントのパターンをラップする *プリミティブ* と呼ばれる要素を提供しています。このドキュメントのナビゲーションサイドバーの下部には、A-Frameが提供するすべてのプリミティブが表示されています。また、開発者が独自のプリミティブを作成することも可能です。


## Example

以下は、いくつかの基本的なプリミティブを使用した *Hello, WebVR* の例です。A-Frameは、メッシュの作成、360度コンテンツのレンダリング、環境のカスタマイズ、カメラの配置などのためのプリミティブを提供しています。


```html
<html>
  <head>
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
  </head>
  <body>
    <a-scene>
      <a-box position="-1 0.5 -3" rotation="0 45 0" color="#4CC3D9"></a-box>
      <a-sphere position="0 1.25 -5" radius="1.25" color="#EF2D5E"></a-sphere>
      <a-cylinder position="1 0.75 -3" radius="0.5" height="1.5" color="#FFC65D"></a-cylinder>
      <a-plane position="0 0 -4" rotation="-90 0 0" width="4" height="4" color="#7BC8A4"></a-plane>
      <a-sky color="#ECECEC"></a-sky>
    </a-scene>
  </body>
</html>
```

### Under the Hood

プリミティブは、主に初心者のための便利なレイヤー（=シンタックスシュガー）として機能します。プリミティブの中身は、 `<a-entity>` のであり、次のようなものだと覚えておいてください。

- セマンティックな名前（例：`<a-box>`）を持つ。
- デフォルト値を持つコンポーネントのプリセットバンドルを持っている
- HTMLの属性を[component][component]データにマッピングまたはプロキシする。

[assemblage]: http://vasir.net/blog/game-development/how-to-build-entity-component-system-in-javascript
[prefab]: http://docs.unity3d.com/Manual/Prefabs.html

プリミティブは[Unityのプレハブ][prefab]に似ています。
entity-component-system パターンに関する文献では、[assemblages][assemblage]と呼ばれもします。
これらは、コアのエンティティコンポーネントAPIを抽象化し、次のような機能を持っています。

- 便利なコンポーネントをデフォルトとしてあらかじめ構成する
- 複雑だが一般的なタイプのエンティティの略記法として機能する (例: `<a-sky>`)
- A-FrameはHTMLを新しい方向へ導くものなので、初心者に馴染みやすいインターフェースを提供する

この `<a-box>` の中身は、

```html
<a-box color="red" width="3"></a-box>
```

以下のエンティティ・コンポーネントを表します。

```html
<a-entity geometry="primitive: box; width: 3" material="color: red"></a-entity>
```

`<a-box>` defaults the `geometry.primitive` property to `box`. And the
primitive maps the HTML `width` attribute to the underlying `geometry.width`
property as well as the HTML `color` attribute to the underlying
`material.color` property.

`<a-box>` は `geometry.primitive` プロパティを `box` にデフォルトで設定します。
そして、プリミティブは HTML の `width` 属性を下層の `geometry.width` プロパティにマッピングし、同様に HTML の `color` 属性を下層の `material.color` プロパティにマッピングします。

## プリミティブへのコンポーネントのアタッチ

[animations]: ../core/animations.md
[mixins]: ../core/mixins.md

Primitives are just `<a-entity>`s under the hood. This means primitives have
the same API as entities such as positioning, rotating, scaling, and attaching
components.

プリミティブは、中身は単なる`<a-entity>`です。
つまり、プリミティブは、位置決め、回転、拡大縮小、コンポーネントのアタッチメントなど、エンティティと同じAPIを持っています。

### 例

Let's attach community physics components to primitives. We include the source
for [Don McCurdy's
`aframe-physics-system`](https://github.com/n5ro/aframe-physics-system) and attach
the physics components via HTML attributes:

プリミティブにコミュニティで作られた物理コンポーネントをアタッチして見ましょう。[Don McCurdy の `aframe-physics-system`](https://github.com/n5ro/aframe-physics-system) のソースを追加し、物理コンポーネントをHTML属性でアタッチします。

> :警告 **A-Frame 1.2.0 以降を使用している場合**。[`aframe-physics-system`](https://github.com/donmccurdy/aframe-physics-system) を使用していて、問題が発生した場合は、現在では非推奨となっている THREE.Geometry を使用していないことを確認してください。詳細は[このGitHubのissue](https://github.com/n5ro/aframe-physics-system/issues/187)を参照してください。

```html
<html>
  <head>
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
    <script src="https://unpkg.com/aframe-physics-system@1.4.0/dist/aframe-physics-system.min.js"></script>
  </head>
  <body>
    <a-scene physics>
      <a-box position="-1 4 -3" rotation="0 45 0" color="#4CC3D9" dynamic-body></a-box>
      <a-plane position="0 0 -4" rotation="-90 0 0" width="4" height="4" color="#7BC8A4" static-body></a-plane>
      <a-sky color="#ECECEC"></a-sky>
    </a-scene>
  </body>
</html>
```

## プリミティブを登録する

独自のプリミティブを登録する（要素を登録する）には `AFRAME.registerPrimitive(name, definition)` を用います。
`name` は文字列で、ダッシュを含む必要があります (例: `'a-foo'`). `definition` は、表のプロパティを定義する JavaScript オブジェクトです。


| プロパティ          | 説明                                                                                                                                                                                                                                                                               | 例                          |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------|
| defaultComponents | プリミティブのデフォルトコンポーネントを指定するオブジェクトです。キーはコンポーネントの名前、値はコンポーネントのデフォルトデータです。                                                                                                                                                | `{geometry: {primitive: 'box'}}`
| mappings          | HTMLの属性名とコンポーネントのプロパティ名の対応を指定するオブジェクトです。HTMLの属性名が更新されるたびに、プリミティブは対応するコンポーネントのプロパティを更新します。コンポーネントプロパティはドットシンタックス `${componentName}.${propertyName}` で定義されます。 | `{depth: 'geometry.depth', height: 'geometry.height', width: 'geometry.width'}`

### 例

例えば、以下はA-Frameの `<a-box>` プリミティブの登録です。

```js
var extendDeep = AFRAME.utils.extendDeep;

// メッシュミックスインは、メッシュベースのプリミティブを作成するための共通のマテリアルプロパティを提供します。
// This makes the material component a default component and maps all the base material properties.
// これにより、マテリアルコンポーネントはデフォルトコンポーネントとなり、すべてのベースマテリアルプロパティがマッピングされます。
var meshMixin = AFRAME.primitives.getMeshMixin();

AFRAME.registerPrimitive('a-box', extendDeep({}, meshMixin, {
  // Preset default components. These components and component properties will be attached to the entity out-of-the-box.
  // デフォルトのコンポーネントをプリセットしています。これらのコンポーネントとコンポーネントプロパティは、すぐにエンティティにアタッチされます。
  defaultComponents: {
    geometry: {primitive: 'box'}
  },

  // HTMLの属性からコンポーネントのプロパティへのマッピングの定義（ドットを区切り文字として使用）。
  // HTMLで `depth="5"` と設定すると、プリミティブでは自動的に `geometry="depth: 5"` が設定されます。
  mappings: {
    depth: 'geometry.depth',
    height: 'geometry.height',
    width: 'geometry.width'
  }
}));
```

[aframe-extras]: https://github.com/donmccurdy/aframe-extras

例えば、Don McCurdy 氏の [`aframe-extras`][aframe-extras] パッケージには `<a-ocean>` プリミティブが含まれており、彼の `ocean` コンポーネントをラップしています。
以下は、 `<a-ocean>` の定義です。


```js
AFRAME.registerPrimitive('a-ocean', {
  // デフォルトで `ocean` コンポーネントをアタッチします。
  // 海が地面と平行になるようにデフォルトで設定されています。
  defaultComponents: {
    ocean: {},
    rotation: {x: -90, y: 0, z: 0}
  },

  // HTMLの属性を `ocean` コンポーネントのプロパティにマップします。
  mappings: {
    width: 'ocean.width',
    depth: 'ocean.depth',
    density: 'ocean.density',
    color: 'ocean.color',
    opacity: 'ocean.opacity'
  }
});
```

`<a-ocean>` というプリミティブを登録すれば、従来のHTMLの一行で海を作ることができるようになります。

```html
<a-ocean color="aqua" depth="100" width="100"></a-ocean>
```
