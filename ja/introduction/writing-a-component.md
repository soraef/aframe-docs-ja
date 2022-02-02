---
title: Writing a Component
type: introduction
layout: docs
parent_section: introduction
order: 8
examples: []
---

[component]: ../core/component.md
[ecs]: ../introduction/entity-component-system.md
[emit]: ./javascript-events-dom-apis.md#emitting-an-event-with-emit

A-Frameの[entity-component framework][ecs]のコンポーネントは、外観、動作、機能を構築するために混合、組み合わせ、エンティティ上に合成することができるJavaScriptモジュールです。JavaScriptでコンポーネントを登録し、DOMから宣言的に利用することができます。コンポーネントは、設定可能、再利用可能、共有可能です。A-Frameアプリケーションのコードのほとんどは、コンポーネントの中にあるべきものです。


[vehicleimage]: https://cloud.githubusercontent.com/assets/674727/21803890/0d44f0c2-d6e1-11e6-8b4f-7fb14c05a492.jpg
![vehicleimage]
<small class="image-caption"><i>Image by Ruben Mueller from vrjump.de</i></small>

このガイドでは、かなりゆっくりと説明します。このガイドを読む前に、[Component API documentation][component] にざっと目を通すことをお勧めします。コンポーネントは `<a-scene>` の前に定義する必要があることに注意してください。


```html
<html>
  <head>
    <script src="foo-component.js"></script>
  </head>
  <body>
    <script>
      // Or inline before the <a-scene>.
      AFRAME.registerComponent('bar', {
        // ...
      });
    </script>

    <a-scene>
    </a-scene>
  </body>
</html>
```

[learning]: #learning-through-components-in-ecosystem

コンポーネントの書き方について、例を挙げて説明します。この例では、ほとんど些細なことを行いますが、データの流れ、API、使い方を説明します。自明ではないコンポーネントの例を見るには、[Learning Through Components in Ecosystemセクション][learning]を参照してください。


<!-- toc -->

## Example: `hello-world` Component

[init]: ../core/component.md#init

まずは、一般的な考え方を知るために、最も基本的なコンポーネントから見ていきましょう。このコンポーネントは、コンポーネントの実体が[`.init()`ハンドラ][init]を使ってアタッチされると、簡単なメッセージを一度だけログに記録します。


### Registering the Component with `AFRAME.registerComponent`

コンポーネントは `AFRAME.registerComponent()` で登録します。コンポーネントの名前を渡しますが、これはDOMでコンポーネントを表現する際のHTML属性名として使われます。次に、メソッドとプロパティを持つJavaScriptオブジェクトである**コンポーネント定義**を渡します。定義内では、**ライフサイクルハンドラーメソッド**を定義することができます。そのうちのひとつが [`.init()`][init] で、これはコンポーネントがその実体に最初に差し込まれたときに一度だけ呼び出されます。


以下の例では、`.init()`ハンドラに簡単なメッセージを記録させているだけです。

```js
AFRAME.registerComponent('hello-world', {
  init: function () {
    console.log('Hello, World!');
  }
});
```

### Using the Component from HTML

そして、この `hello-world` コンポーネントを HTML の属性として宣言的に使用することができます。

```html
<a-scene>
  <a-entity hello-world></a-entity>
</a-scene>
```

さて、エンティティがアタッチされて初期化されると、`hello-world` コンポーネントが初期化されます。コンポーネントの素晴らしいところは、エンティティの準備が整った後に呼び出されることです。**シーンやエンティティがセットアップされるのを待つ心配はありません**。コンソールを確認すると、シーンが実行され始め、エンティティがアタッチされた後に `Hello, World!` が一度だけログに記録されます。


### Using the Component from JS

コンポーネントを設定するもう一つの方法は、静的なHTMLを介するのではなく、 `.setAttribute()` でプログラム的に設定することです。scene 要素はコンポーネントを受け取ることができるので、プログラムによって `hello-world` コンポーネントをシーンに設定してみましょう。


```js
document.querySelector('a-scene').setAttribute('hello-world', '');
```

## Example: `log` Component

`hello-world`コンポーネントと同じように、`log`コンポーネントを作ってみましょう。これはまだ `console.log` を実行するだけですが、`Hello, World!` 以外にも `console.log` を実行できるようにしましょう。この `log` コンポーネントは渡された文字列をログに記録します。スキーマで設定可能なプロパティを定義することで、コンポーネントにデータを渡す方法を見つけることができます。


### Defining Properties with the Schema

**スキーマ**は、そのコンポーネントの**プロパティ**を定義します。例えるなら、コンポーネントを関数と考えると、コンポーネントのプロパティはその関数の引数のようなものです。プロパティには、名前（コンポーネントに複数のプロパティがある場合）、デフォルト値、および **プロパティタイプ** があります。プロパティタイプは、データが文字列として渡された場合（つまり、DOMから）、どのようにパースされるかを定義します。


`log` コンポーネントでは、`schema` を使って `message` プロパティタイプを定義しましょう。message` プロパティタイプは `string` プロパティタイプを持ち、デフォルトの値は `Hello, World!` となります。


```js
AFRAME.registerComponent('log', {
  schema: {
    message: {type: 'string', default: 'Hello, World!'}
  },
  // ...
});
```

### Using Property Data from a Lifecycle Handler

`string` プロパティタイプは、入力されたデータの解析を行わず、そのままライフサイクルのメソッドハンドラに渡します。では、その `message` プロパティタイプを `console.log` してみましょう。hello-world` コンポーネントと同様に、 `.init()` ハンドラを書きます。しかし、今回はハードコードされた文字列をログに記録するわけではありません。このコンポーネントのプロパティタイプの値は `this.data` から取得することができます。そこで、`this.data.message`を記録することにしましょう。

```js
AFRAME.registerComponent('log', {
  schema: {
    message: {type: 'string', default: 'Hello, World!'}
  },

  init: function () {
    console.log(this.data.message);
  }
});
```

[inlinecss]: http://webdesign.about.com/od/beginningcss/qt/tipcssinlinesty.htm

そして、HTMLから、コンポーネントをエンティティにアタッチすることができます。マルチプロパティコンポーネントの場合、構文は [inline css styles][inlinecss] と同じです（プロパティ名と値のペアは `:` で区切り、プロパティは `;` で区切ります）。


```html
<a-scene>
  <a-entity log="message: Hello, Metaverse!"></a-entity>
</a-scene>
```

### Handling Property Updates

これまでのところ、`.init()`ハンドラだけを使用してきました。このハンドラは、コンポーネントのライフサイクルの最初に一度だけ、その初期プロパティだけを使用して呼び出されます。しかし、コンポーネントはしばしば動的にそのプロパティを更新することがあります。そこで、プロパティの更新を処理するために `.update()` ハンドラを使用することができます。

[methodsimage]: https://cloud.githubusercontent.com/assets/674727/21803913/2966ba7e-d6e1-11e6-9179-8acafc87540c.jpg
![methodsimage]
<small class="image-caption"><i>Lifecycle method handlers. Image by Ruben Mueller from vrjump.de</i></small>

これを実証するために、`log`コンポーネントは、そのエンティティが[イベントをemit][emit]したときだけログを記録するようにします。まず、コンポーネントがリッスンするイベントを指定するために、 `event` プロパティタイプを追加します。


```js
// ...
schema: {
  event: {type: 'string', default: ''},
  message: {type: 'string', default: 'Hello, World!'},
},
// ...
```

[addeventlistener]: ./javascript-events-dom-apis.md#adding-an-event-listener-with-addeventlistener

次に、すべてを `.init()` ハンドラから `.update()` ハンドラに移動させます。この `.update()` ハンドラも、コンポーネントがアタッチされたときに `.init()` の直後に呼び出されます。時には、`.update()` ハンドラにロジックのほとんどを記述して、コードを繰り返すことなく初期化と更新処理を一度に行えるようにすることもあります。


私たちがしたいことは、メッセージをログに記録する前にイベントを聞く[イベントリスナーを追加][addeventlistener]することです。もし `event` プロパティタイプが指定されていない場合は、単にメッセージをログに記録します。


```js
AFRAME.registerComponent('log', {
  schema: {
    event: {type: 'string', default: ''},
    message: {type: 'string', default: 'Hello, World!'}
  },

  update: function () {
    var data = this.data;  // Component property values.
    var el = this.el;  // Reference to the component's entity.

    if (data.event) {
      // This will log the `message` when the entity emits the `event`.
      el.addEventListener(data.event, function () {
        console.log(data.message);
      });
    } else {
      // `event` not specified, just log the message.
      console.log(data.message);
    }
  }
});
```

[remove an event listener]: ./javascript-events-dom-apis.md#removing-an-event-listener-with-removeeventlistener

イベントリスナープロパティを追加したので、実際のプロパティの更新を処理してみましょう。イベントプロパティの種類が変更されたとき（例えば、 `.setAttribute()` の結果）、以前のイベントリスナーを削除して、新しいイベントリスナーを追加する必要があります。


しかし、[イベントリスナーを削除する]ためには、関数への参照が必要です。そこで、まずイベントリスナーをアタッチするたびに `this.eventHandlerFn` に関数を保存するようにしましょう。`this` を介してコンポーネントにプロパティをアタッチすると、他のすべてのライフサイクルハンドラで利用できるようになります。


```js
AFRAME.registerComponent('log', {
  schema: {
    event: {type: 'string', default: ''},
    message: {type: 'string', default: 'Hello, World!'}
  },

  init: function () {
    // Closure to access fresh `this.data` from event handler context.
    var self = this;

    // .init() is a good place to set up initial state and variables.
    // Store a reference to the handler so we can later remove it.
    this.eventHandlerFn = function () { console.log(self.data.message); };
  },

  update: function () {
    var data = this.data;
    var el = this.el;

    if (data.event) {
      el.addEventListener(data.event, this.eventHandlerFn);
    } else {
      console.log(data.message);
    }
  }
});
```

これで、イベントハンドラ関数が格納されました。イベントハンドラ関数は保存されているので、`event` プロパティタイプが変更されるたびにイベントリスナーを削除することができます。ここでは、`event` のプロパティタイプが変更されたときだけ、イベントリスナーを更新するようにしたいと思います。これは、`.update()` ハンドラによって提供された `oldData` 引数に対して `this.data` をチェックすることによって行われます。


```js
AFRAME.registerComponent('log', {
  schema: {
    event: {type: 'string', default: ''},
    message: {type: 'string', default: 'Hello, World!'}
  },

  init: function () {
    var self = this;
    this.eventHandlerFn = function () { console.log(self.data.message); };
  },

  update: function (oldData) {
    var data = this.data;
    var el = this.el;

    // `event` updated. Remove the previous event listener if it exists.
    if (oldData.event && data.event !== oldData.event) {
      el.removeEventListener(oldData.event, this.eventHandlerFn);
    }

    if (data.event) {
      el.addEventListener(data.event, this.eventHandlerFn);
    } else {
      console.log(data.message);
    }
  }
});
```

それでは、このコンポーネントを更新イベントリスナーでテストしてみましょう。以下は、このシーンです。

```html
<a-scene>
  <a-entity log="event: anEvent; message: Hello, Metaverse!"></a-entity>
</a-scene>
```

試しに、私たちのエンティティにイベントを[emit][emit]させてみましょう。

```js
var el = document.querySelector('a-entity');
el.emit('anEvent');
// >> "Hello, Metaverse!"
```

では、イベントを更新して `.update()` ハンドラをテストしてみましょう。

```js
var el = document.querySelector('a-entity');
el.setAttribute('log', {event: 'anotherEvent', message: 'Hello, new event!'});
el.emit('anotherEvent');
// >> "Hello, new event!"
```

### Handling Component Removal

[remove]: ./javascript-events-dom-apis.md#removing-a-component-with-removeattribute

[エンティティからコンポーネントを取り外す][remove]というケースを扱ってみましょう。
(つまり、`.removeAttribute('log')`)です。この場合、`.remove()` ハンドラを実装すればよいでしょう。
コンポーネントを削除する際に呼び出されます。`log` コンポーネントの場合、以下のようになります。
は、エンティティに接続されたコンポーネントのイベントリスナーをすべて削除します。

```js
AFRAME.registerComponent('log', {
  schema: {
    event: {type: 'string', default: ''},
    message: {type: 'string', default: 'Hello, World!'}
  },

  init: function () {
    var self = this;
    this.eventHandlerFn = function () { console.log(self.data.message); };
  },

  update: function (oldData) {
    var data = this.data;
    var el = this.el;

    if (oldData.event && data.event !== oldData.event) {
      el.removeEventListener(oldData.event, this.eventHandlerFn);
    }

    if (data.event) {
      el.addEventListener(data.event, this.eventHandlerFn);
    } else {
      console.log(data.message);
    }
  },

  /**
   * Handle component removal.
   */
  remove: function () {
    var data = this.data;
    var el = this.el;

    // Remove event listener.
    if (data.event) {
      el.removeEventListener(data.event, this.eventHandlerFn);
    }
  }
});
```

では、削除ハンドラをテストしてみましょう。コンポーネントを削除し、イベントを発行しても何も起こらないことを確認しましょう。


```html
<a-scene>
  <a-entity log="event: anEvent; message: Hello, Metaverse!"></a-entity>
</a-scene>
```

```js
var el = document.querySelector('a-entity');
el.removeAttribute('log');
el.emit('anEvent');
// >> Nothing should be logged...
```

### Allowing Multiple Instances of a Component

[multiple]: ../core/component.md#multiple

同じエンティティに複数の `log` コンポーネントをアタッチできるようにしましょう。そのためには、[`.multiple` フラグで複数のインスタンスを作成する][multiple]ことを有効にします。これを `true` に設定しましょう。


```js
AFRAME.registerComponent('log', {
  schema: {
    event: {type: 'string', default: ''},
    message: {type: 'string', default: 'Hello, World!'}
  },

  multiple: true,

  // ...
});
```

マルチインスタンスコンポーネントの属性名のシンタックスは `<COMPONENTNAME>__<ID>` という形式で、2つのアンダースコアにIDのサフィックスが付きます。IDは任意に設定することができます。例えば、HTMLでは


```html
<a-scene>
  <a-entity log__helloworld="message: Hello, World!"
            log__metaverse="message: Hello, Metaverse!"></a-entity>
</a-scene>
```

Or from JS:

```js
var el = document.querySelector('a-entity');
el.setAttribute('log__helloworld', {message: 'Hello, World!'});
el.setAttribute('log__metaverse', {message: 'Hello, Metaverse!'});
```

コンポーネント内では、必要であれば `this.id` と `this.attrName` を使用して異なるインスタンスを区別することができます。例えば、 `log__helloworld` があれば、 `this.id` は `helloworld` で、 `this.attrName` は完全な `log__helloworld` となります。


これで、基本的な `log` コンポーネントができました。

## Example: `box` Component

[usingthree]: ./developing-with-threejs.md

あまりつまらない例ですが、[using three.js][usingthree] というコンポーネントを書いて、3Dオブジェクトを追加したり、シーングラフに影響を与えたりする方法を調べてみましょう。アイデアを得るために、ジオメトリとマテリアルの両方を持つボックスメッシュを作成する、基本的な`box`コンポーネントを作成します。


[geometry]: ../components/geometry.md
[material]: ../components/material.md

[boximage]: https://cloud.githubusercontent.com/assets/674727/20326452/b7f94966-ab3d-11e6-95e1-47cabf425278.jpg
![boximage]
<small class="image-caption"><i>Image by Ruben Mueller from vrjump.de</i></small>

**注意:** これは `Hello, World!` コンポーネントを書くのとちょうど3Dで同等です。A-Frameは[geometry][geometry] と [material][material] コンポーネントを提供していますが、実際に箱を作りたいのであれば、このコンポーネントを使用します。


### Schema and API

まずはスキーマから。スキーマはコンポーネントのAPIを定義する。ここでは、`width`, `height`, `depth`, `color` をプロパティで設定できるようにします。`width`, `height`, `depth`,は数値型 (つまり、float) で、デフォルトは 1 メートルです。`color` 型はカラータイプ（つまり文字列）で、デフォルトはグレーです。


```js
AFRAME.registerComponent('box', {
  schema: {
    width: {type: 'number', default: 1},
    height: {type: 'number', default: 1},
    depth: {type: 'number', default: 1},
    color: {type: 'color', default: '#AAA'}
  }
});
```

後で、このコンポーネントをHTML経由で使用する場合、構文は次のようになります。

```html
<a-scene>
  <a-entity box="width: 0.5; height: 0.25; depth: 1; color: orange"
            position="0 0 -5"></a-entity>
</a-scene>
```

### Creating the Box Mesh

[mesh]: https://threejs.org/docs/index.html#Reference/Objects/Mesh
[threegeometry]: https://threejs.org/docs/index.html#Reference/Geometries/BoxBufferGeometry
[threematerial]: https://threejs.org/docs/index.html#Reference/Materials/MeshStandardMaterial
[setobject3d]: ./developing-with-threejs.md#setting-an-object3d-on-an-entity

three.js のボックスメッシュは `.init()` から作成し、プロパティの更新はすべて `.update()` ハンドラーに任せます。three.js でボックスを作成するには、[`THREE.BoxBufferGeometry`][threegeometry]、 [`THREE.MeshStandardMaterial`][threematerial] 、最後に [`THREE.Mesh`][mesh] を作成することになります。次に、エンティティにメッシュを設定し、[`.setObject3D(name, object)`を使って][setobject3d]three.jsのシーングラフにメッシュを追加します．


```js
AFRAME.registerComponent('box', {
  schema: {
    width: {type: 'number', default: 1},
    height: {type: 'number', default: 1},
    depth: {type: 'number', default: 1},
    color: {type: 'color', default: '#AAA'}
  },

  /**
   * Initial creation and setting of the mesh.
   */
  init: function () {
    var data = this.data;
    var el = this.el;

    // Create geometry.
    this.geometry = new THREE.BoxBufferGeometry(data.width, data.height, data.depth);

    // Create material.
    this.material = new THREE.MeshStandardMaterial({color: data.color});

    // Create mesh.
    this.mesh = new THREE.Mesh(this.geometry, this.material);

    // Set mesh on entity.
    el.setObject3D('mesh', this.mesh);
  }
});
```

では、更新の処理をしましょう。ジオメトリ関連のプロパティ（`width`, `height`, `depth` ）が更新されたら、ジオメトリを再作成するだけです。マテリアル関連のプロパティ（例：`color`）が更新された場合、その場でマテリアルを更新するだけです。メッシュを更新するためにメッシュにアクセスするには `.getObject3D('mesh')` を使用します。


```js
AFRAME.registerComponent('box', {
  schema: {
    width: {type: 'number', default: 1},
    height: {type: 'number', default: 1},
    depth: {type: 'number', default: 1},
    color: {type: 'color', default: '#AAA'}
  },

  init: function () {
    var data = this.data;
    var el = this.el;
    this.geometry = new THREE.BoxBufferGeometry(data.width, data.height, data.depth);
    this.material = new THREE.MeshStandardMaterial({color: data.color});
    this.mesh = new THREE.Mesh(this.geometry, this.material);
    el.setObject3D('mesh', this.mesh);
  },

  /**
   * Update the mesh in response to property updates.
   */
  update: function (oldData) {
    var data = this.data;
    var el = this.el;

    // If `oldData` is empty, then this means we're in the initialization process.
    // No need to update.
    if (Object.keys(oldData).length === 0) { return; }

    // Geometry-related properties changed. Update the geometry.
    if (data.width !== oldData.width ||
        data.height !== oldData.height ||
        data.depth !== oldData.depth) {
      el.getObject3D('mesh').geometry = new THREE.BoxBufferGeometry(data.width, data.height,
                                                                    data.depth);
    }

    // Material-related properties changed. Update the material.
    if (data.color !== oldData.color) {
      el.getObject3D('mesh').material.color = new THREE.Color(data.color);
    }
  }
});
```

### Removing the Box Mesh

最後に、コンポーネントやエンティティが削除されたときの処理をします。今回は、シーンからメッシュを削除したいと思います。これは `.remove()` ハンドラと `.removeObject3D(name)` を使って行います。


```js
AFRAME.registerComponent('box', {
  // ...

  remove: function () {
    this.el.removeObject3D('mesh');
  }
});
```

これで基本的なthree.jsの`box`コンポーネントが完成です! 実際には、three.jsのコンポーネントはもっと便利なことをするはずです。three.jsで実現できることはすべて、A-Frameコンポーネントでラップして、宣言的にすることができます。というわけで、three.jsの機能とエコシステムをチェックして、あなたがどんなコンポーネントを書けるか見てみてください


## Example: `follow` Component

あるエンティティに他のエンティティをフォローするように指示する `follow` コンポーネントを書いてみましょう。これは、レンダリングループの各フレームで継続的に動作するビヘイビアをシーンに追加する `.tick()` ハンドラの使用を実証するものです。これはまた、エンティティ間のリレーションシップのデモでもあります。


### Schema and API

まず最初に、どのエンティティを追いかけるかを指定する `target` プロパティが必要です。A-Frameには、`selector`というプロパティタイプがあり、クエリーセレクタを渡すと、エンティティエレメントを返してくれる。さらに、`speed` プロパティ（単位：m/s）を追加して、エンティティがどの程度の速度で追従するかを指定します。


```js
AFRAME.registerComponent('follow', {
  schema: {
    target: {type: 'selector'},
    speed: {type: 'number'}
  }
});
```

後で、このコンポーネントをHTML経由で使用する場合、構文は次のようになります。

```html
<a-scene>
  <a-box id="target-box" color="#5E82C5" position="-3 0 -5"></a-box>
  <a-box follow="target: #target-box; speed: 1" color="#FF6B6B" position="3 0 -5"></a-box>
</a-scene>
```

### Creating a Helper Vector

ハンドラ `.tick()` は毎フレーム（例えば、1秒間に90回）呼び出されるので、そのパフォーマンスを確認したいと思います。例えば、`THREE.Vector3`オブジェクトのような不要なオブジェクトをtick毎に作成するようなことはしたくありません。これはガベージコレクションを中断させることにつながります。`THREE.Vector3` を使ってベクトル演算をする必要があるので、`.init()` ハンドラで一度作成し、後で再利用できるようにします。


```js
AFRAME.registerComponent('follow', {
  schema: {
    target: {type: 'selector'},
    speed: {type: 'number'}
  },

  init: function () {
    this.directionVec3 = new THREE.Vector3();
  }
});
```

### Defining a Behavior With the `.tick()` Handler

今度は、`.tick()`ハンドラを書いて、コンポーネントがターゲットに向かって希望する速度でエンティティを連続的に移動させるようにします。A-Frame はグローバルなシーンのアップタイムを `time` として、最後のフレームからの時間を `timeDelta` として `tick()` ハンドラにミリ秒単位で渡します。このフレームでは `timeDelta` を使って、速度が与えられているときにターゲットに向かってどの程度移動すべきかを計算することができます。


エンティティの向かうべき方向は、ターゲットの方向ベクトルからエンティティの位置ベクトルを引くことで計算される。オブジェクトは `.object3D` からアクセスでき、そこから位置ベクトル `.position` が得られます。方向ベクトルは `init()` ハンドラで割り当てた `this.directionVec3` に格納します。

次に、移動距離と速度、そして最後のフレームからの経過時間を考慮し、エンティティの位置に追加する適切なベクトルを探します。エンティティを `.setAttribute` で移動させ、次のフレームで `.tick()` ハンドラが再び実行されます。


完全な `.tick()` ハンドラは以下の通りです。`.tick()` は、レンダリングループを実際に参照することなく、レンダリングループに簡単にフックすることができるので、とても便利です。メソッドを定義するだけです。以下のコードコメントに従ってください。


```js
AFRAME.registerComponent('follow', {
  schema: {
    target: {type: 'selector'},
    speed: {type: 'number'}
  },

  init: function () {
    this.directionVec3 = new THREE.Vector3();
  },

  tick: function (time, timeDelta) {
    var directionVec3 = this.directionVec3;

    // Grab position vectors (THREE.Vector3) from the entities' three.js objects.
    var targetPosition = this.data.target.object3D.position;
    var currentPosition = this.el.object3D.position;

    // Subtract the vectors to get the direction the entity should head in.
    directionVec3.copy(targetPosition).sub(currentPosition);

    // Calculate the distance.
    var distance = directionVec3.length();

    // Don't go any closer if a close proximity has been reached.
    if (distance < 1) { return; }

    // Scale the direction vector's magnitude down to match the speed.
    var factor = this.data.speed / distance;
    ['x', 'y', 'z'].forEach(function (axis) {
      directionVec3[axis] *= factor * (timeDelta / 1000);
    });

    // Translate the entity in the direction towards the target.
    this.el.setAttribute('position', {
      x: currentPosition.x + directionVec3.x,
      y: currentPosition.y + directionVec3.y,
      z: currentPosition.z + directionVec3.z
    });
  }
});
```

## Learning Through Components in Ecosystem

エコシステムには数多くのコンポーネントが存在し、そのほとんどがGitHubでオープンソース化されています。学ぶ方法の一つは、他のコンポーネントのソースコードを閲覧して、それらがどのように構築されているか、どのようなユースケースを提供しているかを確認することです。ここでは、いくつかの場所を紹介します。


[corecomponents]: https://github.com/aframevr/aframe/tree/master/src/components
[paintercomponents]: https://github.com/aframevr/a-painter/tree/master/src/components

[weekofaframe]: https://aframe.io/blog/
[officialsite]: https://aframe.io/
[community]: https://aframe.io/community/
[npmcomponents]: https://www.npmjs.com/search?q=keywords:aframe&page=1&ranking=optimal
[aframetwitter]: https://twitter.com/aframevr/

- [A-Frame core components][corecomponents] - Source code of A-Frame's standard components.
- [A-Painter components][paintercomponents] - Application-specific components for A-Painter.
- [**A Week of A-Frame** Weekly Series][weekofaframe]
- [Official Site][officialsite]
- [Community][community]
- [Components on npm][npmcomponents]
- [Twitter][aframetwitter]

## Publishing a Component

[angle]: https://www.npmjs.com/package/angle

実際に使用される多くのコンポーネントは、アプリケーションに特化したものや、一点物のコンポーネントになるでしょう。しかし、もしあなたがコミュニティにとって有用で、他のアプリケーションでも動作するような汎用性のあるコンポーネントを書いたのであれば、それを公開すべきです!


コンポーネントテンプレートには、[`angle`][angle]を使用することをお勧めします。`angle` はA-Frameのコマンドラインインターフェースです。その特徴の一つは、GitHubやnpmに公開するためのコンポーネントテンプレートを設定することと、エコシステム内の他の全てのコンポーネントと一貫性を持たせることです。テンプレートをインストールするには


```sh
npm install -g angle && angle initcomponent
```

`initcomponent` はテンプレートをセットアップするために、コンポーネント名などのいくつかの情報を尋ねます。コード、サンプル、ドキュメントを書いて、GitHub と npm に公開しましょう!

