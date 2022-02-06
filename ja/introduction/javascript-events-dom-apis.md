---
title: JavaScript, Events, DOM APIs
type: introduction
layout: docs
parent_section: introduction
order: 6
examples:
  - title: A-Frame School &mdash; Getting Entities
    src: https://glitch.com/edit/#!/aframe-school-js?path=solution.html
  - title: A-Frame School &mdash; Modifying Entities
    src: https://glitch.com/edit/#!/aframe-school-js?path=solution2.html
  - title: A-Frame School &mdash; Creating Entities
    src: https://glitch.com/edit/#!/aframe-school-js?path=solution3.html
  - title: A-Frame School &mdash; Handling Events
    src: https://glitch.com/edit/#!/aframe-school-js?path=solution4.html
  - title: Animated Lights
    src: https://glitch.com/edit/#!/aframe-animated-lights?path=index.html
---

[geometry]: ../components/geometry.md
[DOM]: https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction
[object3d]: https://threejs.org/docs/#api/core/Object3D

A-Frameは単なるHTMLなので、通常のWeb開発と同様にJavaScriptと[DOM]APIを使ってシーンとその実体を制御することができます。

[vrjump]: http://vrjump.de

[jsimage]: https://cloud.githubusercontent.com/assets/674727/20290105/e1573210-aa92-11e6-8f1a-8a31fb6dad52.jpg
![With JavaScript][jsimage]
<small class="image-caption"><i>Ruben Mueller によるイメージ図、[The VR Jump][vrjump] から</i></small>

[entity]: ../core/entity.md

シーン内のすべての要素は、`<a-box>` や `<a-sky>` などの要素であっても、エンティティ（`<a-entity>` として表現されます）です。
A-FrameはHTML要素のプロトタイプを修正し、特定のDOM APIに対して、A-Frameに合わせた特別な動作を追加しています。
以下に説明するほとんどのAPIに関するリファレンスは、[Entity API documentation][entity]を参照してください。

<!--toc-->

## A-FrameのJavaScriptコードを配置する場所

[A-Frame components]: ../core/component.md

**重要:** JavaScriptとDOM APIのさまざまな使い方を説明する前に、JavaScriptのコードを[A-Frameコンポーネント]でカプセル化を推奨することを記しておきます。
コンポーネントは、コードをモジュール化し、ロジックや動作をHTMLから見えるようにし、コードが正しいタイミングで実行されるようにします（例えば、シーンやエンティティがアタッチされ初期化された後など）。
最も基本的な例として、`<a-scene>` の*前*に `console.log` コンポーネントを登録する場合です。

```js
AFRAME.registerComponent('log', {
  schema: {type: 'string'},

  init: function () {
    var stringToLog = this.data;
    console.log(stringToLog);
  }
});
```

そして、登録の*後*、HTMLからコンポーネントを使用します。

```html
<a-scene log="Hello, Scene!">
  <a-box log="Hello, Box!"></a-box>
</a-scene>
```

コンポーネントは、すべてのコードをカプセル化し、再利用可能、宣言的、かつ共有可能なものにします。
ただし、実行時にちょっと調べるだけなら、ブラウザのDeveloper Tools Consoleを使って、シーン上でJavaScriptを実行することができます。

[contentscripts]: ../core/scene.md#running-content-scripts-on-the-scene

従来の 2D スクリプトのように、A-Frame 関連の JavaScript を `<a-scene>` の後に生の `<script>` タグで記述しようとは**しないで**ください。
もしそうすると、コードが適切なタイミングで実行されるように特別な対策を講じなければならなくなります (詳しくは [コンテンツスクリプトをシーンで実行する][contentscripts] を参照してください)。


## クエリやトラバースによるエンティティの取得

[queryselector]: https://developer.mozilla.org/docs/Web/API/Document/querySelector
[jqueryselector]: https://api.jquery.com/category/selectors/

シーングラフとしてのDOMの素晴らしいところは、標準のDOMが `.querySelector()` と `.querySelectorAll()` を通じて探索、問い合わせ、検索、選択のためのユーティリティを提供することです。
元々は [jQuery selectors][jqueryselector] に触発されたもので、[MDNでクエリセレクタについて学ぶことができます][queryselector].

クエリセレクタの例をいくつか実行してみましょう。たとえば、以下のようなシーンを見てみましょう。

```html
<html>
  <a-scene>
    <a-box id="redBox" class="clickable" color="red"></a-box>
    <a-sphere class="clickable" color="blue"></a-sphere>
    <a-box color="green"></a-box>
    <a-entity light="type: ambient"></a-entity>
    <a-entity light="type: directional"></a-entity>
  </a-scene>
</html>
```

### `.querySelector()` の使い方

もし、1つの要素だけを取得したい場合は、1つの要素を返す `.querySelector()` を使用します。
それでは、sceneの要素を取得してみましょう。

```js
var sceneEl = document.querySelector('a-scene');
```

コンポーネント内で作業している場合は、クエリを実行しなくても、すでにシーンエレメントへの参照を持っていることに注意してください。
すべてのエンティティは、そのシーンエレメントへの参照を持っています。

```js
AFRAME.registerComponent('foo', {
  init: function () {
    console.log(this.el.sceneEl);  // シーンエレメントへの参照
  }
});
```

要素にIDがある場合は、IDセレクタ（例：`#<ID>`）を使用することができます。
IDを持つ赤のボックスを取得してみましょう。
以前はドキュメント全体に対してクエリセレクタを実行しました。
ここでは、シーンの範囲内だけのクエリセレクタを実行します。
クエリセレクタを使うと、クエリの範囲を任意の要素内に限定することができます。


```js
var sceneEl = document.querySelector('a-scene');
console.log(sceneEl.querySelector('#redBox'));
// <a-box id="redBox" class="clickable" color="red"></a-box>
```

### `.querySelectorAll()` の使い方

要素のグループを取得したい場合は、要素の配列を返す `.querySelectorAll()` を使用します。
要素名をまたいで問い合わせることができます。

```js
console.log(sceneEl.querySelectorAll('a-box'));
// [
//  <a-box id="redBox" class="clickable" color="red"></a-box>,
//  <a-box color="green"></a-box>
// ]
```

クラスを持つ要素はクラスセレクタ（`.<CLASS_NAME>`）で問い合わせることができます。
`clickable`クラスを持つすべてのエンティティを取得してみましょう。

```js
console.log(sceneEl.querySelectorAll('.clickable'));
// [
//  <a-box id="redBox" class="clickable" color="red"></a-box>
//  <a-sphere class="clickable" color="blue"></a-sphere>
// ]
```

属性（この場合はコンポーネント）を含む要素は、属性セレクタ（すなわち `[<ATTRIBUTE_NAME>]`）で問い合わせることができます。
lightを持っているエンティティをすべて取得してみましょう。

```js
console.log(sceneEl.querySelectorAll('[light]'));
// [
//  <a-entity light="type: ambient"></a-entity>
// <a-entity light="type: directional"></a-entity>
// ]
```

### `.querySelectorAll()` で全てのエンティティをループ処理

`.querySelectorAll()` を使ってエンティティーのグループを取得したら、 `for` ループでループさせることができます。
シーン内のすべての要素に対して `*` でループしてみましょう。

```js
var els = sceneEl.querySelectorAll('*');
for (var i = 0; i < els.length; i++) {
  console.log(els[i]);
}
```

#### パフォーマンスに関する注意点

フレームごとに呼び出される `tick` や `tock` 関数で `.querySelector` や `.querySelectorAll` を使用しないようにしましょう。エンティティを取得するためにDOMをループするのに時間がかかります。
代わりに、あらかじめクエリセレクタを呼び出したエンティティのリストをキャッシュしておき、その上でループしましょう。


```js
AFRAME.registerComponent('query-selector-example', {
  init: function () {
    this.entities = document.querySelectorAll('.box');
  },
  
  tick: function () {
    // ここでクエリセレクタを呼ばず、あらかじめクエリする
    for (let i = 0; i < this.entities.length; i++) {
      // 各エンティティについて何か処理する
    }
  }
});
```

## `.getAttribute()` によるコンポーネントデータの取得

エンティティのコンポーネントのデータは `.getAttribute` で取得することができます。
A-Frameでは、文字列ではなく値を返すように `.getAttribute` を拡張しています（例えば、コンポーネントは通常複数のプロパティで構成されているので、ほとんどの場合オブジェクトを返します、または `.getAttribute('visible')` のような場合は実際のブール値を返します）。
多くの場合、 `.getAttribute` はコンポーネントの内部データオブジェクトを返すので、オブジェクトを直接変更しないでください。

```js
// <a-entity geometry="primitive: sphere; radius: 2"></a-entity>
el.getAttribute('geometry');
// >> {"primitive": "sphere", "radius": 2, ...}
```

### `position` と `scale` を取得

[vector3]: https://threejs.org/docs/#api/math/Vector3
[updatepos]: #updating-position-rotation-scale-visible

`el.getAttribute('position')` や `el.getAttribute('scale')` を実行すると three.js の [Object3D][object3d] の position と scale のプロパティが [Vector3][vector3]で返されます。
これらのオブジェクトを変更すると、実際のエンティティデータが変更されることに留意してください。

これは、A-Frameでは、three.jsレベルで [position, rotation, scale, visible][updatepos] を変更することができるようにしてあり、 `.getAttribute` が正しいデータを返すために、A-Frameが実際の three.js の Object3D オブジェクトを返すからです。

これは `.getAttribute('rotation')` には当てはまりません。
なぜならA-Frame は良くも悪くもラジアンの代わりに度数を使用するからです。
このような場合、x/y/zのプロパティを持つ通常のJavaScriptオブジェクトが返されます。
ラジアンよりも低いレベルで作業する必要がある場合は、Object3Dのオイラーを `el.object3D.rotation` によって取得することができます。


## A-Frameシーングラフの変更

JavaScriptとDOM APIを使えば、通常のHTML要素と同じように動的にエンティティを追加したり削除したりすることができます。

### `.createElement()` によるエンティティの作成

エンティティを作成するには、`document.createElement` を使用することができます。これによって、空のエンティティが得られます。

```js
var el = document.createElement('a-entity');
```

ただし、このエンティティは、シーンにアタッチするまでは初期化されず、シーンの一部にもなりません。

### `.appendChild()` によるエンティティの追加

To add an entity to the DOM, we can use `.appendChild(element)`. Specifically, we want to add it to our scene. We grab the scene, create the entity, and append the entity to our scene.

DOMにエンティティを追加するには、`.appendChild(element)`を使用します。
具体的には、シーンに追加します。
シーンを取得し、エンティティを作成し、エンティティをシーンに追加します。

```js
var sceneEl = document.querySelector('a-scene');
var entityEl = document.createElement('a-entity');
// エンティティを初期化するために`.setAttribute()`します
sceneEl.appendChild(entityEl);
```

ブラウザでは `.appendChild()` は *非同期な*操作であることに注意してください。
エンティティが DOM に追加されるまで、エンティティに対して多くの操作 (`.getAttribute()` の呼び出しなど) を行うことができません。
もし、追加されたばかりのエンティティの属性を問い合わせる必要がある場合は、エンティティの `loaded` イベントをリッスンするか、A-Frameコンポーネントにロジックを配置し、準備ができた時点で実行されるようにすることが可能です。


```js
var sceneEl = document.querySelector('a-scene');

AFRAME.registerComponent('do-something-once-loaded', {
  init: function () {
    // これは、エンティティが適切にアタッチされ、ロードされた後に呼び出されます
    console.log('I am ready!');
  }
});

var entityEl = document.createElement('a-entity');
entityEl.setAttribute('do-something-once-loaded', '');
sceneEl.appendChild(entityEl);
```

### `.removeChild()` によるエンティティの削除

DOMから、つまりシーンからエンティティを削除するには、親要素から `.removeChild(element)` を呼び出します。
エンティティがある場合、その親（`parentNode`）にエンティティを削除するように依頼する必要があります。

```js
entityEl.parentNode.removeChild(entityEl);
```

## エンティティの変更

空白のエンティティは何もしません。コンポーネントを追加したり、コンポーネントのプロパティを設定したり、コンポーネントを削除することで、エンティティを変更することができます。

### `.setAttribute()` によるコンポーネントの追加

コンポーネントを追加するには、 `.setAttribute(componentName, data)` を使用します。エンティティにジオメトリコンポーネントを追加してみましょう。

```js
entityEl.setAttribute('geometry', {
  primitive: 'box',
  height: 3,
  width: 1
});
```

[physics]: https://github.com/donmccurdy/aframe-physics-system

また、[コミュニティの物理演算コンポーネント][physics]を追加してみましょう。

```js
entityEl.setAttribute('dynamic-body', {
  shape: 'box',
  mass: 1.5,
  linearDamping: 0.005
});
```

[setattr]:  ../core/entity.md#setattribute-attr-value-componentattrvalue

通常のHTMLの `.setAttribute()` とは異なり、エンティティの `.setAttribute()` は、オブジェクトなどのさまざまな種類の引数を取ったり、コンポーネントの単一のプロパティを更新できるように改良されています。
[`Entity.setAttribute()` についてもっと読む][setattr]。


### `.setAttribute()` によるコンポーネントの更新

コンポーネントを更新するためにも、`.setAttribute()`を使用します。コンポーネントの更新にはいくつかの形式があります。

#### 単一プロパティのコンポーネントのプロパティを更新する

[position]: ../components/position.md

シングルプロパティコンポーネントである[position component][position]のプロパティを更新してみましょう。
オブジェクトか文字列を渡すことができます。オブジェクトを渡すと、A-Frameが文字列をパースする必要がないので、少し好ましいです。

```js
entityEl.setAttribute('position', {x: 1, y: 2, z: -3});
// しかし、`entityEl.object3D.position.set(1, 2, -3)`の方が好ましく、それがなぜかは続きを読んでください
```

#### マルチプロパティのコンポーネントの単一プロパティを更新する

[material]: ../components/material.md

マルチプロパティコンポーネントである[material component][material]の1つのプロパティを更新してみましょう。
これは、コンポーネント名、プロパティ名、そしてプロパティ値を `.setAttribute()` に指定することで行います。

```js
entityEl.setAttribute('material', 'color', 'red');
```

#### マルチプロパティコンポーネントの複数のプロパティを更新する

[light]: ../components/light.md

マルチプロパティコンポーネントである[light component][light]の複数のプロパティを一度に更新してみましょう。
これは、コンポーネント名とプロパティのオブジェクトを `.setAttribute()` に指定することで行います。
ここでは、ライトの色と強さを変更しますが、タイプはそのままにしておきます。

```js
// <a-entity light="type: directional; color: #CAC; intensity: 0.5"></a-entity>
entityEl.setAttribute('light', {color: '#ACC', intensity: 0.75});
// <a-entity light="type: directional; color: #ACC; intensity: 0.75"></a-entity>
```

#### `position`、`rotation`、`scale`、`visible` を更新する

特殊なケースとして、パフォーマンス、メモリ、ユーティリティへのアクセスを改善するために、 `.setAttribute` ではなく、エンティティの [Object3D][object3d] を通じて three.js レベルで直接 `position`, `rotation`, `scale`, `visible` を変更することを推奨します。


```js
// positionの例
entityEl.object3D.position.set(1, 2, 3);
entityEl.object3D.position.x += 5;
entityEl.object3D.position.multiplyScalar(5);

// rotationの例
entityEl.object3D.rotation.y = THREE.Math.degToRad(45);
entityEl.object3D.rotation.divideScalar(2);

// scaleの例
entityEl.object3D.scale.set(2, 2, 2);
entityEl.object3D.scale.z += 1.5;

// visibleの例
entityEl.object3D.visible = false;
entityEl.object3D.visible = true;
```

これにより、`.setAttribute` のオーバーヘッドをスキップして、代わりに最も頻繁に更新されるコンポーネントのプロパティを簡単に設定することができます。
three.jsレベルでの更新は、例えば `entityEl.getAttribute('position');` のように実行しても、反映されます。

#### マルチプロパティコンポーネントのプロパティを置き換える

マルチプロパティコンポーネントである[geometry component][geometry]のプロパティをすべて置き換えてみましょう。
そのためには、コンポーネント名、`.setAttribute()`にプロパティのオブジェクト、そして既存のプロパティを破棄することを指定するフラグを指定します。
geometryの既存のプロパティをすべて新しいプロパティに置き換えることになります。


```js
// <a-entity geometry="primitive: cylinder; height: 4; radius: 2"></a-entity>
entityEl.setAttribute('geometry', {primitive: 'torusKnot', p: 1, q: 3, radiusTubular: 4}, true);
// <a-entity geometry="primitive: torusKnot; p: 1; q: 3; radiusTubular: 4"></a-entity>
```

### `.removeAttribute()` によるコンポーネントの削除

エンティティからコンポーネントを削除したり、切り離したりするには、 `.removeAttribute(componentName)` を使用することができます。カメラエンティティからデフォルトの `wasd-controls` を削除してみましょう。

```js
var cameraEl = document.querySelector('[camera]');
cameraEl.removeAttribute('wasd-controls');
```

## イベントとイベントリスナー

[eventlistener]: http://javascript.info/tutorial/introduction-browser-events

JavaScript と DOM では、エンティティやコンポーネントが互いにやりとりするための簡単な方法があります。
イベントとイベントリスナーです。
イベントとは、他のコードが拾って応答できるようなシグナルを送る方法です。[ブラウザのイベントについてはこちら][eventlistener].


### `.emit()`でイベントを発信する

A-Frame要素では、`.emit(eventName, eventDetail, bubbles)`で簡単にカスタムイベントを発信することができます。
例えば、物理コンポーネントを作っていて、エンティティが他のエンティティと衝突したときにシグナルを送りたい場合を考えてみましょう。

```js
entityEl.emit('physicscollided', {collidingEntity: anotherEntityEl}, false);
```

そして、コードの他の部分は、このイベントを待ち、リッスンし、それに応答してコードを実行することができます。
第2引数として、イベントの詳細を通して情報やデータを渡すこともできます。
また、イベントが*bubbles*かどうか、つまり親エンティティもイベントを発するかどうかを指定することができます。
つまり、コードの他の部分がイベントリスナーを登録することができます。


### `.addEventListener()`でイベントリスナーを追加する

通常のHTML要素と同様に、イベントリスナーを `.addEventListener(eventName, function)` で登録することができます。
リスナーが登録されたイベントが発生すると、その関数が呼び出され、イベントを処理します。
例えば、先程の物理衝突のイベントの続きです。

```js
entityEl.addEventListener('physicscollided', function (event) {
  console.log('Entity collided with', event.detail.collidingEntity);
});
```

エンティティが `physicscollided` イベントを発信すると、イベントオブジェクトを持つ関数が呼び出されます。
イベントオブジェクトには、イベントを通じて渡されたデータや情報を含むイベント詳細があります。

### `.removeEventListener()` でイベントリスナーを削除する。

通常のHTML要素と同様に、イベントリスナーを削除したい場合は、 `.removeEventListener(eventName, function)` を使用することができます。リスナーが登録されていたのと同じイベント名と関数を渡さなければなりません。例えば、先ほどの物理衝突イベントの例から続けると、以下のようになります。

```js
// この関数を後で削除する場合は、名前を付けて定義する必要があります
function collisionHandler (event) {
  console.log('Entity collided with', event.detail.collidingEntity);
}

entityEl.addEventListener('physicscollided', collisionHandler);
entityEl.removeEventListener('physicscollided', collisionHandler);
```

### イベントリスナーをバインドする

デフォルトでは、[Javascript実行コンテキストルール](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) は、独立した関数に対して `this` をグローバルコンテキスト (`window`) にバインドします。
これは、これらの関数がデフォルトではコンポーネントの `this` にアクセスできないことを意味します。

イベントリスナー内部でコンポーネントの `this` にアクセスできるようにするには、[バインドされている必要があります](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this).

いくつかの方法があります。

1. アロー関数を使用して、イベントリスナーを定義することができます。アロー関数は自動的に `this` をバインドします。

```
this.el.addEventListener('physicscollided', (event) => {
    console.log(this.el.id);
});
```


2. イベントリスナーをコンポーネントのイベントオブジェクトの中で定義します（これにより、リスナーの追加と削除も自動的に行われます）。

   解説は[こちら](../core/component.html#events)をご覧ください。


3. その関数のバインド版である別の関数を作成します。

```
this.listeners = {
    clickListener: this.clickListener.bind(this);
}
entityEl.addEventListener('click', this.listeners.clickListener);
```

## 注意事項

[faq]: ./faq.md#why-is-the-html-not-updating-when-i-check-the-browser-inspector
[mutation-observer]: https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver
[attr-selectors]: https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors

A-Frameのエンティティやプリミティブは、[パフォーマンス重視][faq]で実装されているため、一部のHTML APIが期待通りに動作しない場合があります。
例えば、[値を含む属性セレクタ][attr-selectors]は動作しませんし、[変異オブザーバ][mutation-observer]は、エンティティのコンポーネントが変更されたときに変更をトリガしません。
