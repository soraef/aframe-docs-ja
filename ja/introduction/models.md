---
title: 3D Models
type: introduction
layout: docs
parent_section: introduction
order: 10
examples:
 - title: Modifying Material of Model
   src: https://glitch.com/edit/#!/aframe-modify-model-material?path=index.html:1:0
---

[3loaders]: https://github.com/mrdoob/three.js/tree/dev/examples/js/loaders
[ecsfind]: ./entity-component-system.md#where-to-find-components
[glTF]: ../components/gltf-model.md
[OBJ]: ../components/obj-model.md
[recommend using glTF]: ../components/gltf-model.md#why-use-gltf

A-Frameには、「glTF」と「OBJ」を読み込むためのコンポーネントが用意されています。glTFは、Web上での3Dモデル転送の標準として採用が進んでいるため、可能であればglTFを使用することをお勧めします。[Three.jsローダー][3loaders]を持つファイル形式であれば、どのようなファイル形式でもコンポーネントを作成することができます。また、他のフォーマット（例：PLY、FBX、JSON、three.js）を処理するために既に書かれているコンポーネントを[エコシステムで見つける][ecsfind]ようにすることも可能です。

モデルは、頂点、面、UV、テクスチャ、マテリアル、アニメーションを含むプレーンテキストファイルの形式で提供されます。three.jsローダーは、これらのファイルを解析して、three.jsのシーン内でメッシュとしてレンダリングします。A-Frameのモデルコンポーネントは、これらのthree.jsローダーをラップしています。


## Animating Models

[mixer]: https://github.com/donmccurdy/aframe-extras/tree/master/src/loaders#animation

[Don McCurdy氏のanimation-mixerコンポーネント][mixer]を使って、モデルに内蔵されているアニメーションを再生することができます。アニメーションは通常、A-Frameレベルで提供されるのではなく、アニメーションツールやプログラムを通じて構築されたモデルに搭載されています。animation-mixer コンポーネントは、将来的に A-Frame のコアに統合されるかもしれません。

アニメーションの作成に関する最初の資料として、以下を参照してください。

- [Blender Tutorial - Creating and Editing Actions for Re-use in Animations and Games](https://www.youtube.com/watch?v=Gb152Qncn2s)を参照してください。
- [ワークフロー：アニメーション from Blender to three.js by Arturo Paracuellos](http://unboring.net/workflows/animation.html)

## Where to Find Models

3Dモデルを探す場所には、以下のようなものがあります。

- [Sketchfab](https://sketchfab.com)
- [Clara.io](http://clara.io)
- [Archive3D](http://archive3d.net)
- [Sketchup's 3D Warehouse](https://3dwarehouse.sketchup.com)
- [TurboSquid](http://www.turbosquid.com/Search/3D-Models/free)

## How to Create Models

モデルを作成するためのプログラムには

- [Supercraft](https://supermedium.com/supercraft/) - Built **with** A-Frame to model directly within VR with no modeling skill required and load with [`aframe-supercraft-loader`](https://www.npmjs.com/package/aframe-supercraft-loader).
- [Blender](https://www.blender.org/)
- [MagicaVoxel](https://ephtracy.github.io/)
- [Autodesk Maya](https://www.autodesk.com/products/maya/overview) or [Maya LT](https://www.autodesk.com/products/maya-lt/overview)
- [Maxon Cinema4D](https://www.maxon.net/en-us/)

## Hosting Models

[Hosting and Publishing &mdash; Hosting Models](./hosting-and-publishing.md#hosting-models)を参照してください。

## Modifying Materials

[modify]: https://glitch.com/edit/#!/aframe-modify-model-material?path=index.html:1:0

モデルの素材を変更するには、モデルの読み込みを待って、モデルから作成されたthree.jsのメッシュを変更する必要があります。A-Frameのモデルコンポーネントは、ネットワーク上でモデルを要求し、モデルを解析し、three.jsのメッシュやオブジェクトを作成し、`<a-entity>`下の `.getObject3D('mesh')` にロードします。そのメッシュに到達して、何でも、この場合はthree.jsのマテリアルを変更することができます。


[ロードしたモデルのマテリアルを変更する][modify]のライブ例を参照してください。

```html
<script>
  AFRAME.registerComponent('modify-materials', {
    init: function () {
      // Wait for model to load.
      this.el.addEventListener('model-loaded', () => {
        // Grab the mesh / scene.
        const obj = this.el.getObject3D('mesh');
        // Go over the submeshes and modify materials we want.
        obj.traverse(node => {
          if (node.name.indexOf('ship') !== -1) {
            node.material.color.set('red');
          }
        });
      });
    }
  });
</script>

<a-scene background="color: #ECECEC">
  <a-assets>
    <a-asset-item id="cityModel" src="https://cdn.aframe.io/test-models/models/glTF-2.0/virtualcity/VC.gltf"></a-asset-item>
  </a-assets>
  <a-entity gltf-model="#cityModel" modify-materials></a-entity>
</a-scene>
```

## Troubleshooting

[hostingmodels]: ./hosting-and-publishing.md#hosting-models

何よりもまず、コンソールにエラーがないかどうかを確認してください。CORS に関連する一般的な問題は、モデルを適切にホスティングすることで解決できます。また、モデルが不足している追加ファイルを必要としているかどうかも、コンソールが教えてくれます。

### I Don't See My Model

コンソールにエラーが表示されない場合は、モデルを縮小してみてください。エクスポート時にスケールの不一致があることがよくあり、その場合、カメラがモデルの中に入ってしまい、見ることができなくなります。


```html
<a-entity gltf-model="#tree" scale="0.01 0.01 0.01"></a-entity>
```

これがうまくいかない場合は、`ctrl + alt + i`を押してインスペクタを開き、ズームアウトしてモデルが実際にそこにあることを確認します。

### I Don't See Textures

テクスチャがすぐに動作しないことがあります。これは通常、エクスポーターがテクスチャに `C:\Path</ToModel</texture.jpg` や `/Path/To/Model/texture.jpg` といった絶対パスを使用するからで、これはウェブ上では動作しません。代わりに、`texture.jpg` や `images/texture.jpg` のように、モデルまたは `.mtl` ファイルからの相対パスで指定します。


1. モデル（OBJの場合は.mtl）をプレーンテキストエディタで開きます。
2. テクスチャの名前を検索します（例：`texture.jpg`）。
3. テクスチャのパスを絶対ではなく、相対にして修正します。

この方法でうまくいかなかった場合、MTLファイルをチェックすると、TGAやその他のプレーン・イメージではない種類のテクスチャを使おうとしていることに気づくかもしれません。この場合、three.jsのローダーを追加で入れる必要があります。しかし、コンバータを使用して、すべてのTGAをPNGのような画像に変換し、「tga」のインスタンスをすべて「png」に置き換えると、より簡単になるかもしれません。

### My Model Isn't Animating

[aframe-extras]: https://github.com/donmccurdy/aframe-extras

Don McCurdy氏による[aframe-extras]の一部である[animation-mixer component][mixer] は、three.js (.json) と glTF (.gltf) モデルでアニメーションを再生するためのコントロールを提供するものです。


### My Model Looks Distorted

モデルでよくある問題は、法線の向きが正しくないことです。法線の問題は、ジオメトリの面の一部が透明になっていたり、メッシュに「穴」が開いていたりする場合に分かります。

1. Blenderにモデルをインポートします。
2. プロパティパネルのシェーディングセクションでバックフェイスカリングをオンにし(`n`)、どの面が不正なのかを把握します。
3. オブジェクトを選択します。
4. 編集モードへ (`<tab>`)
5. a キーですべての面を選択
6. `<ctrl> + n` を押して法線を一致させます。もし逆になっているようであれば
ツールバー (`t`) を使って反転させます。

## Model Performance

大きなファイルや複雑なモデルを読み込むと、ブラウザの動作が遅くなったり、読み込みすらできなくなることに気づくことがあります。高忠実度のレンダリング用に設計された複雑なモデルは、リアルタイムのアプリケーションには向いていないのです。しかし、外観を維持しながらパフォーマンスを向上させるために、いくつかできることがあります。

### Testing for Performance

[stats]: ../components/stats.md

シーンのパフォーマンスを知るために、[stats component][stats]を有効にしてください。

```html
<a-scene stats>
```

グラフを見ながらシーンを移動してさまざまなシナリオをテストし、必ず異なるハードウェアでテストしてください。開発用PCは、スマートフォンよりも簡単にシーンを処理できる可能性が高いでしょう。60fpsに達しない場合は、原因が見つかるまで、シーンのさまざまな要素を削除してみてください。パフォーマンスの原因となっているのが3Dモデルであれば、それを最適化することができます。

### Optimizing Complex Models

シーンの最適化で最も大きな要素のひとつが、モデルの複雑さと面数です。ジオメトリは少なければ少ないほど良いのです。

モデルの面数を減らす手っ取り早い方法は、Blenderのdecimateモディファイアを使用することです。decimateモディファイアはモデルのUV座標を保持したまま、ジオメトリの面の量を減らします。適切なモデリング技術に勝るものはありませんが、Decimateモディファイアは最適化されていないファイルをよりスムーズに動作させるための素晴らしい最終手段になりえます。モディファイヤーの設定は、モデルやどの程度ディテールを除去したいかに大きく依存します。


![Blender's Decimate Modifier](https://cloud.githubusercontent.com/assets/674727/25730604/f5402d90-30f2-11e7-9571-96bcdef11a6a.jpg)

比率（ハイライト表示）を調整し、面の数を減らす。

最後に一部の面を手動で修正する必要があるかもしれません。結果に満足したら、*コピーを保存*し、希望のファイルタイプにエクスポートします。テストして、必要に応じて調整します。なお、モディファイアを適用しないと効果が出ない場合があります。

