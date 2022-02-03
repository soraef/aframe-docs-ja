---
title: Developing with three.js
type: introduction
layout: docs
parent_section: introduction
order: 7
examples: []
---

[three.js]: http://threejs.org

A-Frameは[three.js]をベースとしたフレームワークであるため、three.jsのAPIにフルアクセスすることが可能です。ここでは、A-Frameの下にあるthree.jsのシーン、オブジェクト、APIにアクセスする方法について説明します。

<!-- toc -->

## A-Frameとthree.jsのシーングラフの関係

- A-Frameの`<a-scene>`は、three.jsのシーンと一対一でマッピングされます。
- A-Frameの`<a-entity>`は、1つ以上のthree.jsオブジェクトにマッピングされます。
- three.jsのオブジェクトは、`.el`を介して、A-Frameのエンティティへの参照を持っており、これは
はA-Frameによって設定される。

### 親子関係

A-Frameのエンティティが親子関係でネストしている場合、そのthree.jsオブジェクトもネストしています。例えば、このA-Frameのシーンを見てみましょう。


```html
<a-scene>
  <a-box>
    <a-sphere></a-sphere>
    <a-light></a-light>
  </a-box>
</a-scene>
```

three.jsのシーングラフが対応し、以下のようになります。

```
THREE.Scene
  THREE.Mesh
    THREE.Mesh
    THREE.Light
```

## three.jsのAPIにアクセスする

three.jsは、ウィンドウ上のグローバルオブジェクトとして利用可能です。

```js
console.log(THREE);
```

## three.jsのオブジェクトを扱う

A-Frameはthree.jsの上に乗っている抽象化されたものですが、その下でもthree.jsで操作しています。A-Frameの要素には、three.jsのシーングラフにつながるドアがついています。


### three.jsのシーンへのアクセス

[scene]: https://threejs.org/docs/#api/scenes/Scene

[three.js scene][scene] は `<a-scene>` 要素から `.object3D` としてアクセスすることができます。

```js
document.querySelector('a-scene').object3D;  // THREE.Scene
```

また、すべてのA-Frameエンティティは `.sceneEl` を介して `<a-scene>` への参照も持っています。

```js
document.querySelector('a-entity').sceneEl.object3D;  // THREE.Scene
```

[component]: ../core/component.md

[component][component]からは、その実体（つまり`this.el`）を通してシーンにアクセスします。


```js
AFRAME.registerComponent('foo', {
  init: function () {
    var scene = this.el.sceneEl.object3D;  // THREE.Scene
  }
});
```

### エンティティのthree.jsオブジェクトへのアクセス

[group]: https://threejs.org/docs/#api/objects/Group
[object3d]: https://threejs.org/docs/#api/core/Object3D

すべてのA-Frameエンティティ（例：`<a-entity>`）は、独自の[`THREE.Object3D`][object3d]、より具体的には、異なるタイプの`Object3D`を含む[`THREE.Group`][group]を持っています。エンティティのルートである `THREE.Group` には `.object3D` でアクセスします。


```js
document.querySelector('a-entity').object3D;  // THREE.Group
```

[entity]: ../core/entity.md

エンティティは、複数のタイプの `Object3D` で構成することができます。例えば、ジオメトリコンポーネントとライトコンポーネントの両方を持つことで、エンティティは `THREE.Mesh` と `THREE.Light` の両方になることができます。


```html
<a-entity geometry light></a-entity>
```

コンポーネントは、エンティティのルートである `THREE.Group` の下にメッシュとライトを追加します。メッシュとライトへの参照は、エンティティの `.object3DMap` に異なるタイプの three.js オブジェクトとして格納されます。


```js
console.log(entityEl.object3DMap);
// {mesh: THREE.Mesh, light: THREE.Light}
```

しかし、エンティティの `.getObject3D(name)` メソッドでアクセスすることができます。

```js
entityEl.getObject3D('mesh');  // THREE.Mesh
entityEl.getObject3D('light');  // THREE.Light
```

では、この3つの.jsオブジェクトがそもそもどのように設定されたのかを見てみましょう。

### エンティティに `Object3D` を設定する

オブジェクト3Dを設定すると、そのオブジェクト3Dがエンティティのグループに追加され、新しく設定されたオブジェクト3Dがthree.jsのシーンの一部となります。オブジェクト3Dを設定するには、エンティティの `.setObject3D(name)` メソッドを使用します。


例えば、コンポーネント内部から点光源を設定する場合。

```js
AFRAME.registerComponent('pointlight', {
  init: function () {
    this.el.setObject3D('light', new THREE.PointLight());
  }
});
// <a-entity light></a-entity>
```

ここでは、光源を `light` という名前で設定します。後でアクセスするには、前述のようにエンティティの `.getObject3D(name)` メソッドを使用することができます。


```js
entityEl.getObject3D('light');
```

また、A-Frameのエンティティにthree.jsオブジェクトを設定すると、A-Frameはthree.jsオブジェクトから`.el`を介してA-Frameエンティティへの参照を設定することになります。


```js
entityEl.getObject3D('light').el;  // entityEl
```

### Removing an `Object3D` From an Entity

`Object3D`をエンティティから削除するには、エンティティの `.removeObject3D(name)` メソッドを使用します。ポイントライトの例に戻ると、コンポーネントがデタッチされたときに、ライトを削除します。


```js
AFRAME.registerComponent('pointlight', {
  init: function () {
    this.el.setObject3D('light', new THREE.PointLight());
  },

  remove: function () {
    // Remove Object3D.
    this.el.removeObject3D('light');
  }
});
```

## Transforming Between Coordinate Spaces

すべてのオブジェクトとシーン（世界）一般は、それぞれ独自の座標空間を持っています。親オブジェクトの位置、回転、スケール変換は、その子オブジェクトの位置、回転、スケール変換に適用される。このシーンを考えてみましょう。

```html
<a-entity id="foo" position="1 2 3">
  <a-entity id="bar" position="2 3 4"></a-entity>
</a-entity>
```

fooの変換はbarの変換の上に適用されるので、世界の基準点からfooの位置は(1,2,3)、barの位置は(3, 5, 7)となる。fooの基準点から見ると、fooの位置は(0, 0, 0)、barの位置は(2, 3, 4)である。これらの参照点と座標空間との間で変換を行いたいことがよくある。上記は簡単な例ですが、barの位置のワールド空間座標を求めたり、任意の座標をfooの座標空間に変換するような操作を行いたい場合があります。3Dプログラミングでは、これらの操作は行列で実現されますが、three.jsはそれらを簡単にするヘルパーを提供します。


### Local to World Transforms

通常は、親の`Object3D`に対して `.updateMatrixWorld ()` を呼び出す必要がありますが、three.jsのデフォルトでは `Object3D.matrixAutoUpdate` は `true` に設定されています。three.jsの `.getWorldPosition (vector)` と `.getWorldQuaternion (quaternion)` を使用することができます。


`Object3D`のワールドポジションを取得する。

```js
var worldPosition = new THREE.Vector3();
entityEl.object3D.getWorldPosition(worldPosition);
```

`Object3D`のワールドローテーションを取得する。

```js
var worldQuaternion = new THREE.Quaternion();
entityEl.object3D.getWorldQuaternion(worldQuaternion);
```

three.js `Object3D` に [ローカルからワールドへの変換で利用できる関数が増えました][object3d] が追加されました。

- `.localToWorld (vector)`
- `.getWorldDirection (vector)`
- `.getWorldQuaternion (quaternion)`
- `.getWorldScale (vector)`

### World to Local Transforms

ワールドからオブジェクトのローカル空間へ変換する行列を得るには、オブジェクトのワールド行列の逆行列を取得します。


```js
var worldToLocal = new THREE.Matrix4().getInverse(object3D.matrixWorld)
```

そして、その `worldToLocal` 行列を、変換したいものに適用することができるのです。

```js
anotherObject3D.applyMatrix(worldToLocal);
```
