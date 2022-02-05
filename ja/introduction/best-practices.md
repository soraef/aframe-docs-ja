---
title: Best Practices
type: introduction
layout: docs
parent_section: introduction
order: 13
---

## A-Frame

[ecs]: ./entity-component-system.md
[mixins]: ../core/mixins.md
[template]: https://github.com/supermedium/superframe/tree/master/components/template/

A-Frameのコア構造は、[entity-component (ECS)][ecs]です。再利用性、モジュール性、コンポーザビリティ、デカップリング、カプセル化、宣言性、テスト容易性のために、純粋なA-Frameコンポーネント内にアプリケーションコードを配置し、構造化する。

**Do not do this**:

```
<a-scene>
  <a-box></a-box>
  <!-- ... -->
</a-scene>

<script>
  // My JavaScript code here!
  // ... NO!
</script>
```

A-Frameのコンポーネント内にコードを配置することで、正しいタイミングで実行され、再利用可能なコードをカプセル化し、A-Frameを使用する目的であるフレームワークを利用することができます。

**Do this**:

```
<script>
  AFRAME.registerComponent('code-that-does-this', {
    init: function () {
      // Code here.
      console.log(this.el);
    }
  });

  AFRAME.registerComponent('code-to-attach-to-box', {
    init: function () {
      // Code here.
      console.log(this.el);
    }
  });
</script>

<a-scene code-that-does-this>
  <a-box code-to-attach-to-box></a-box>
  <!-- ... -->
</a-scene>
```

## Performance

[animation]: ../components/animation.md#direct-values-through-object3d-and-components
[asm]: ../core/asset-management-system.md
[hardware]: ./vr-headsets-and-webvr-browsers.md
[stats]: ../components/stats.md
[pool]: ../components/pool.md
[background]: ../components/background.md
[geometrymerger]: https://www.npmjs.com/package/aframe-geometry-merger-component


- [ハードウェアの推奨スペック][hardware]を使用する。
- **[statsコンポーネント][stats]**を使用して、様々なメトリクス（FPS、頂点と面数、ジオメトリとマテリアル数、描画コール、エンティティ数）に目を配りましょう。FPSを最大化し、それ以外のものを最小化したい。
- 描画コールをできる限り制限する。最適化されていないジオメトリ、オブジェクト、モデルは通常、それぞれドローコールが発生します。経験則では、最大300以下に抑えるようにします。可能であれば、すべての静的メッシュを [Merge][geometrymerger] にまとめてください。three.js のジオメトリは、頂点ごとに色や uv などのデータを保持し、マージ後のジオメトリの保守や操作に使用することができます。
- **[アセットマネジメントシステム][asm]**を利用して、ブラウザキャッシュやアセットのプリロードを行ってください。
- モデルを使用する場合、リアルタイムのライティングとシャドウに依存するよりも、テクスチャにライトをベイクすることを検討してください。
- 一般的に、シーン内のエンティティやライトの数は少ないほどよいです。
- ランタイム中にレンダラーがテクスチャのサイズを変更する必要がないように、テクスチャの解像度が2の累乗（例：256x256、512x1024）であることを確認すること。
- モデルの面および頂点の数を制限する。
- さらに、ジオメトリインスタンシング、[ジオメトリマージ][geometrymerger]、レベルオブディテール（LOD）などのテクニックもあります。
- レイキャスターやコライダーを使用する場合、シーン内のすべてのオブジェクトに対してレイキャスティングを行うのではなく、どのエンティティに対してレイキャスティングを行うかを選択する。
- 継続的に動作するビヘイビアを追加する場合は、A-Frame コンポーネントの `tick` ハンドラーを使用して、グローバル レンダー ループにフックします。必要に応じて、`AFRAME.utils.throttleTick`などのユーティリティを使用して、`tick`ハンドラの実行回数を制限してください。
- シーンの背景としてソリッドカラーを定義するには、`a-sky`の代わりに **[background component][background]** を使用します。これにより、不要なジオメトリが作成されるのを防ぐことができます。
- `setAttribute` のオーバーヘッドを避けるために、three.js レベルで `position`、`rotation`、`scale`、`visible` を更新する (`el.object3D.position`, `el.object3D.rotation`, `el.object3D.scale`, `el.object3D.visible`) ようにしました。
- 同じタイプのエンティティを多数作成、削除、再作成する必要がある場合は、**[pool component][pool]** を使用してエンティティを事前に生成し、再利用することができます。これにより、エンティティをその場で作成するコストを回避し、ガベージコレクションを削減することができます。
- アニメーションコンポーネントを使用する場合、[animate values directly][animation] を使用すると `.setAttribute` をスキップして JS 値を直接アニメーションさせることができます。例えば、`material.opacity`の代わりに `components.material.material.opacity` をアニメートさせます。

### GPU Texture Preloading

GPU へのテクスチャのノンブロッキングアップロードが可能になるまで は、すべてのマテリアルとテクスチャを前もって描画するようにしてくださ い。マテリアルとテクスチャが初めて描画されると、ブラウザは GPU へのアップロード中にハングアップしてブロックします。呼び出すことで、手動でテクスチャをプリロードすることができます。

```js
document.querySelector('a-scene').renderer.setTexture2D(ourTexture, 0);
```

A-Frameでは、これを自動的に行う便利なAPIを用意するよう努力する予定です。

[360]: https://aframe-360-gallery.glitch.me

例えば、[360&deg;画像ギャラリー][360]ではそれが顕著に表れています。ブラウザのパフォーマンスツールを見ると、1回目に新しい画像に切り替えたときはコマ落ちしますが、2回目に画像に戻したときはスムーズな遷移が見られます。

マテリアルとテクスチャはできるだけ再利用し、少数のユニークなマテリアルを目指します。テクスチャアトラスは、素材を再利用しつつ、より多様な印象を与えることができる効率的な方法のひとつです。`MeshLambertMaterial` や `MeshBasicMaterial` などの単純な three.js マテリアルの方が性能が良く、ローポリのシーンでは十分な場合が多いです。

特に、アンライティング（ベーシック）マテリアルにプリベイクされたライティングは、パフォーマンスを大幅に向上させることができます。A-FrameのデフォルトのPBRベース（Standard）マテリアルは、より物理的にリアルですが、より高価で、シンプルなシーンでは不要な場合が多いです。

### Minimizing Garbage Collection in JavaScript

[firefox-alloc]: https://developer.mozilla.org/en-US/docs/Tools/Performance/Allocations
[chrome-alloc]: https://developers.google.com/web/tools/chrome-devtools/memory-problems/#spot_frequent_garbage_collections

JavaScriptのオブジェクト、配列、文字列、関数などのゴミを作ったり、新しいインスタンスを作成することはできるだけ避けましょう。2Dウェブでは、ガベージコレクタが実行されるアイドル時間が多いため、JavaScriptオブジェクトを大量に作成してもそれほど大きな問題にはなりません。VRの場合、ガベージコレクションはメモリを掃除するために一時停止するので、フレーム落ちの原因になることがあります。これを避けるため、メモリの割り当てを最小限にし、オブジェクトを保持してガベージコレクションされないように工夫しています。

[Firefox][firefox-alloc] と [Chrome][chrome-alloc] のパフォーマンスツールで、割り当てとガベージコレクションを検出する方法について詳しく説明します。

`Object.keys(obj).forEach(function () { });` のようなパターンは避けるようにしてください。これは、`for (key in obj)` を使う場合と比較して、新しい配列や関数、コールバックを作成することになります。また、配列の反復処理では、 `.forEach` を避け、代わりに単純な `for` ループを使用します。オブジェクトの不要なコピーを避けるために、 `utils.clone` や `.slice` の代わりに `utils.extend(target, source)` のようなメソッドを使用します。

イベントを発生させる場合、イベントの詳細については同じオブジェクトを再利用するようにしましょう。

```js
AFRAME.registerComponent('foo', {
  init: function () {
    this.someData = [];
    this.evtDetail = {someData: this.someData};
  },

  tick: function (time) {
    this.someData.push(time);
    this.el.emit('bar', this.evtDetail);
  }
});
```

上記のすべての提案は、すべてのフレームで実行される `tick()` ハンドラでは特に重要です。

ガベージコレクタの活動を減らすための他の記事。

- [JSにおけるガベージコレクタの活動を減らすためのベストプラクティス](https://stackoverflow.com/questions/18364175/best-practices-for-reducing-garbage-collector-activity-in-javascript)
- [低ガベージなリアルタイムJavaScriptの書き方](https://www.construct.net/en/blogs/construct-official-blog-1/how-to-write-low-garbage-real-time-javascript-761)

### `tick` Handlers

[スロットル]: ../core/utils.html#aframe-utils-throttletick-function-t-dt-minimuminterval-optionalcontext

コンポーネントティクハンドラでは、新しいオブジェクトの作成は質素にすること。オブジェクトの再利用を心がけましょう。プライベートで再利用可能な補助変数を作成するパターンとして、クロージャを使用する方法があります。以下では、ヘルパーのベクトルとクォータニオンを作成し、各フレームで新しいものを作成するのではなく、フレーム間でそれらを再利用しています。これらの変数は、コンポーネントのすべてのインスタンスで共有されるため、状態を保持しないように注意してください。こうすることで、メモリ使用量とガベージコレクションを減らすことができます。

```js
AFRAME.registerComponent('foo', {
  tick: function () {
    this.doSomething();
  },

  doSomething: (function () {
    var helperVector = new THREE.Vector3();
    var helperQuaternion = new THREE.Quaternion();

    return function () {
      helperVector.copy(this.el.object3D.position);
      helperQuaternion.copy(this.el.object3D.quaternion);
    };
  })()
});
```

また、ティック内のコンポーネントを継続的に変更する場合、プロパティを更新するために同じオブジェクトを渡すことを確認してください。A-Frameは、渡された最新のオブジェクトを記録し、それ以降の呼び出しで型チェックを無効にすることで、さらなるスピードアップを図ります。以下は、tick毎に回転を変更する推奨tick関数の例です。

```js
AFRAME.registerComponent('foo', {
  tick: function () {
    var el = this.el;
    var rotationTmp = this.rotationTmp = this.rotationTmp || {x: 0, y: 0, z: 0};
    var rotation = el.getAttribute('rotation');
    rotationTmp.x = rotation.x + 0.1;
    rotationTmp.y = rotation.y + 0.1;
    rotationTmp.z = rotation.z + 0.1;
    el.setAttribute('rotation', rotationTmp);
  }
});
```

tick関数は1秒間に90回実行されるため、クリティカルなパフォーマンスコードとして扱われます。[より少ない間隔でコードを実行するために `utils.throttleTick`][throttle] を使用することを検討してください。

## VR Design

[oculus]: https://developer.oculus.com/design/latest/concepts/book-bp/

VRのためのデザインは、フラットな体験のためのデザインとは異なります。新しいメディアとして、特にユーザーの快適さと存在感を維持するために、従うべき新しいベストプラクティスのセットが存在するのです。この点については、すでに詳しく説明されていますので、これらのガイドに従うことにします。なお、VRインタラクションデザインはかなり新しいものであり、絶対的なものは何もありません。

- [Oculus Best Practices (for VR)][oculus]

いくつか注意点があります。

- 一般的な黄金律は、カメラの制御をユーザーから不意に奪わないことです。
- 単位（位置など）はメートルとすること。これは、WebVR APIがメートル単位でポーズを返すためで、ほとんどのカメラコントロールに入力されます。単位をメートルとすることで、期待通りのスケールを実現します。

手やコントローラーを活用する 最高の体験を得るためには、アプリケーションを特定のフォームファクターに向けるのではなく、一度にすべてのプラットフォーム向けに水増しする必要があります。
