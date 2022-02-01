---
title: Introduction
section_title: Introduction
type: introduction
layout: docs
order: 1
parent_section: docs
section_order: 1
installation: true
examples:
  - title: Hello, World!
    src: https://glitch.com/edit/#!/aframe?path=index.html
---

[three.js]: https://threejs.org

## Getting Started

[glitch]: http://glitch.com/~aframe

A-Frameは、何のインストールも要らず、プレーンなHTMLファイルのみで開発できます。

A-Frameを試すのに最適な方法は、**[Glitchにあるスターターサンプルを触ってみること][glitch]** です。

このサイトはオンラインコードエディターで、無料で即座にホストとデプロイしてくれます。もしくは以下のように、`<head>`にA-Frameを含めて `.html` ファイルを作成しても実行可能です。

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

[Installation]: ./installation.md
[school]: https://aframe.io/school/

[インストール][Installation]のページでは、A-Frameを使い始めるための様々なオプションをご紹介しています。A-Frame の学習を始めるには、[A-Frame School][school] で、ドキュメントを補完する視覚的なステップバイステップのレッスンをご覧ください。

##  A-Frameとは?

[github]: https://github.com/aframevr/
[community]: https://aframe.io/community/

![A-Frame](https://cloud.githubusercontent.com/assets/674727/25392020/6f011d10-298c-11e7-845e-c3c5baebd14d.jpg)

:a:-Frameは、バーチャルリアリティ（VR）体験を構築するためのウェブフレームワークです。
A-FrameはHTMLの上に構築されているため、簡単に使い始めることができます。
しかし、A-Frameは単なる3Dシーングラフやマークアップ言語ではありません。
コアとなるのは、[three.js]に宣言的、拡張的、かつ構成可能な構造を提供する、強力なエンティティコンポーネント・フレームワークです。

VRコンテンツを開発するための簡単かつ強力な方法として開発されたものです。
元々はMozilla内で考案され、現在はA-Frameの共同制作者が[Supermedium](https://supermedium.com)内で管理しています。
[独立したオープンソースプロジェクト][github]として、A-Frameは[最大のVRコミュニティ][community]の1つに成長しました。

A-Frameは、Vive、Rift、Windows Mixed Reality、Daydream、GearVR, Cardboard、Oculus Go などほとんどのVRヘッドセットをサポートしており、拡張現実（AR）にも使用できます。
A-Frameは全領域をサポートしていますが、 A-Frameの目標は位置追跡とコントローラをフルに活用し、基本的な360&deg;コンテンツを超える完全没入型のインタラクティブなVR体験を定義することです。

<div class="docs-introduction-examples">
  <a href="https://supermedium.com/supercraft">
    <img alt="Supercraft" target="_blank" src="https://user-images.githubusercontent.com/674727/41085457-f5429566-69eb-11e8-92e5-3210e4c6c4a0.gif" height="190" width="32%">
  </a>
  <a href="https://aframe.io/a-painter/?url=https://ucarecdn.com/962b242b-87a9-422c-b730-febdc470f203/">
    <img alt="A-Painter" target="_blank" src="https://cloud.githubusercontent.com/assets/674727/24531388/acfc3dda-156d-11e7-8563-5bd75252f70f.gif" height="190" width="32%">
  </a>
  <a href="https://supermedium.com">
    <img alt="Supermedium" target="_blank" src="https://user-images.githubusercontent.com/674727/37294616-7212cd20-25d3-11e8-9e7f-c0c61074f1e0.png" height="190" width="32%">
  </a>
  <a href="https://aframe.io/a-blast/">
    <img alt="A-Blast" target="_blank" src="https://cloud.githubusercontent.com/assets/674727/24531440/0336e66e-156e-11e7-95c2-f2e6ebc0393d.gif" height="190" width="32%">
  </a>
  <a href="https://aframe.io/a-saturday-night/">
    <img alt="A-Saturday-Night" target="_blank" src="https://cloud.githubusercontent.com/assets/674727/24531477/44272daa-156e-11e7-8ef9-d750ed430f3a.gif" height="190" width="32%">
  </a>
  <a href="https://github.com/googlecreativelab/webvr-musicalforest">
    <img alt="Musical Forest by @googlecreativelab" target="_blank" src="https://cloud.githubusercontent.com/assets/674727/25109861/b8e9ec48-2394-11e7-8f2d-ea1cd9df69c8.gif" height="190" width="32%">
  </a>
</div>

## A-Frameの特徴

:eyeglasses: **シンプルなVR開発**：`<script>`タグと`<a-scene>`タグを用意するだけです。
A-Frameは、3Dボイラープレート、VRセットアップ、デフォルトの制御を行います。インストールやビルドの必要はありません。

:heart: **HTMLの宣言性**：HTMLは読みやすく、理解しやすく、コピー&ペーストしやすいです。
HTMLをベースにしているため、A-Frameはウェブ開発者、VR愛好家、アーティスト、デザイナー、教育者、メーカー、子供たちなど、誰でも利用可能です。

:electric_plug: **エンティティ・コンポーネントアーキテクチャ**: A-Frameは、強力な[three.js]フレームワークで、宣言的、合成可能、再利用可能な[エンティティ・コンポーネント構造][ecs]を提供します。
HTMLは氷山の一角で、開発者はJavaScript、DOM API、three.js、WebVR、WebGLに制約なしにアクセスすることができます。


:globe_with_meridians: **クロスプラットフォームVR**: Vive、Rift、Windows Mixed Reality、Daydream、GearVR、Cardboard用のVRアプリケーションを構築し、それぞれのコントローラをサポートします。
ヘッドセットやコントローラをお持ちではないですか？問題ありません！A-Frameは標準的なデスクトップとスマートフォンでも動作します。

[ecs]: ./entity-component-system.md

[A-Painter]: https://github.com/aframevr/a-painter
[Tilt Brush]: https://www.tiltbrush.com/

:zap: **パフォーマンス**: A-FrameはWebVRのために一から最適化されています。A-FrameはDOMを使用しますが、その要素はブラウザのレイアウトエンジンに触れることはありません。3Dオブジェクトの更新はすべてメモリ内で行われ、ガベージやオーバーヘッドも小さいです。最もインタラクティブで大規模なWebVRアプリケーションは、90fpsでスムーズに動作するA-Frameで行われました。

[inspector]: ./visual-inspector-and-dev-tools.md

:mag: **ビジュアルインスペクター**: A-Frameには便利な[ビジュアル3Dインスペクター][inspector]が内蔵されています。A-Frameの*どこでも*シーンを開いて、`<ctrl> + <alt> + i`を押して、ボンネットの中を覗いてみてください。

![Inspector](https://cloud.githubusercontent.com/assets/674727/25377018/27be9cce-295b-11e7-9098-3e85ac1fe172.gif)

[augmented reality]: https://github.com/jeromeetienne/AR.js#augmented-reality-for-the-web-in-less-than-10-lines-of-html
[environment]: https://github.com/supermedium/aframe-environment-component
[multiuser]: https://github.com/haydenjameslee/networked-aframe
[oceans]: https://github.com/donmccurdy/aframe-extras/tree/master/src/primitives
[particle systems]: https://github.com/IdeaSpaceVR/aframe-particle-system-component
[physics]: https://github.com/donmccurdy/aframe-physics-system
[state]: https://npmjs.com/package/aframe-state-component
[super hands]: https://github.com/wmurphyrd/aframe-super-hands-component
[teleportation]: https://github.com/fernandojsg/aframe-teleport-controls

:runner: **コンポーネント**: ジオメトリ、マテリアル、ライト、アニメーション、モデル、レイキャスト、シャドウ、ポジションオーディオ、テキスト、主要なヘッドセット用のコントロールなど、A-Frameのコアコンポーネントを使用すれば、すぐにでも十全な開発を行えます。更には[エンバイロメント][environment]、[ステート][state]、[パーティクルシステム][particle
systems]、[物理演算][physics]、[マルチユーザー][multiuser]、[海][oceans]、[テレポーテーション][teleportation]、[スーパーハンド][super hands]、
[拡張現実（AR）][augmented reality]などの数百ものコミュニティコンポーネントが用意されています。

:earth_americas: **実績とスケーラビリティ**: A-Frameは、Google、Disney、Samsung、トヨタ、フォード、シボレー、アムネスティ・インターナショナル、ヨーロッパ合同原子核研究機構、NPR、Al Jazeera、The Washington Post、NASAなどの企業で使用されています。Google、Microsoft、Oculus、Samsungなどの企業がA-Frameにコントリビュートしています。


## さぁ、始めましょう！

[Discord]: https://supermedium.com/discord
[slack]: https://aframevr.slack.com/join/shared_invite/zt-f6rne3ly-ekVaBU~Xu~fsZHXr56jacQ

初めての人のための、A-Frame入門がうまくいくためのプランを紹介します。


1. [ニュースレターを購読して](https://aframe.io/subscribe/)、A-Frameの最新情報やコミュニティ・プロジェクトの特集を確認しましょう。

2. ドキュメントを読んで理解しましょう。
[Glitch](https://glitch.com/~aframe) は、推奨されるコーディングのプレイグラウンド、および例として使われます。

3. [Discord] と [Slack] でコミュニティに参加できます。何か質問があれば [StackOverflow](http://stackoverflow.com/questions/ask/?tags=aframe) で検索して質問すれば、誰かが答えてくれますよ！

4. 何かを作ったら、そのプロジェクトをオンラインで共有して見てください！
[ニュースレター](https://aframe.io/subscribe/)や[ブログ](https://aframe.io/blog/)で紹介できるかもしれません！

そして、JavaScriptと[three.js](https://threejs.org/)の基礎知識を掘り下げることは、本当に役に立ちます。楽しみましょう！
