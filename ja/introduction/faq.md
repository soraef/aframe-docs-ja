---
title: FAQ
type: introduction
layout: docs
parent_section: introduction
order: 14
---

[ecs]: ./entity-component-system.md
[github]: http://github.com/aframevr/aframe/
[three]: http://threejs.org
[slack]: https://aframevr.slack.com/join/shared_invite/zt-f6rne3ly-ekVaBU~Xu~fsZHXr56jacQ
[twitter]: https://twitter.com/aframevr/
[stackoverflow]: http://stackoverflow.com/questions/tagged/aframe/

<!--toc-->

## A-Frameの性能はいかがでしょうか？

[a-painter]: https://blog.mozvr.com/a-painter
[tiltbrush]: https://www.tiltbrush.com/

A-Frameは、適切なブラウザ（WebVR搭載のFirefoxなど）を使えば、ネイティブ並みのレイテンシとフレームレートを実現することができます。例えば、[A-Painter][a-painter]はブラウザで[Tilt Brush][tiltbrush] で、90フレーム/秒をスムーズに実行でき、ネイティブと見分けがつかないほどです。

A-FrameがHTMLベースであることは問題ではありません。通常のWebアプリケーションでは、ブラウザの2Dレイアウトがパフォーマンスの主要な問題でしたが、A-Frameではカスタムエレメントを単なるデータコンテナとして機能させ、レイアウトエンジンを起動させないようにしています。3D操作は最小限のオーバーヘッドでメモリ内で行われ、OpenGLまたはDirect3DにバインドするWebGLでレンダリングされます。

A-Frameがオーバーヘッドを最小化するためにとっている対策には、次のようなものがあります。

- setAttribute`を同期化し、コードパスを短縮。setAttribute('position', {x: 1, y: 2, z: 3})` によるエンティティの位置の変更は、ほぼ直接three.jsのオブジェクトに触れます。このオーバーヘッドには、新しいデータと古いデータを比較して、変更をトリガーする必要があるかどうかを判断したり、ライフサイクルハンドラを呼び出したりすることが含まれます。
- DOMに戻るデータをシリアライズしない。エンティティのプロパティを変更する際、ブラウザのDOMインスペクタで見られるような実際のHTMLは、文字列化操作を減らすために更新されません。このため、ほとんどの操作はメモリ上で行われ、Virtual DOMにほんの少し似ています。
- すべてを単一の `requestAnimationFrame` で管理し、コンポーネントが `tick` ハンドラを介して単一のグローバルなレンダリングループにフックできるようにします。
- HTML の属性値をキャッシュする。
- アセット、マテリアル、テクスチャ、ジオメトリのキャッシュ。
- 3Dやゲーム業界で使用されているパフォーマンステクニック（例：ジオメトリインスタンシング、Level-of-Detail、オブジェクトプーリング）を実装したコミュニティコンポーネントを提供する。

[bestpractices]: ../introduction/best-practices.md

A-Frameは、ほとんどの一般的なユースケースに対して良好なパフォーマンスをもたらす合理的なデフォルト値を提供します。しかし、性能は最終的に個々のアプリケーションの複雑さと特性によって決定されます。リソースを最大限に活用するためには、3Dグラフィックスについてより深く理解する必要があります。まずは、[ベストパフォーマンスの実践とガイドライン][bestpractices]をご覧ください。

## なぜ私の体験はVRやARモードに入らないのでしょうか？

[release]: https://github.com/aframevr/aframe/releases
[webxr]: https://immersive-web.github.io/webxr/

A-Frame 1.2.0またはそれ以前のバージョンをお使いの場合は、おそらく[最新リリース][release]にアップデートする必要があります。ブラウザは[WebXR標準][webxr]に移行しつつあり、古いバージョンはもう動作しないかもしれません。

また、コンテンツは HTTPS で提供する必要があります。WebXR API は HTTP では利用できません。

## アセット（画像、動画、モデルなど）が読み込まれないのですが？

[cors]: https://en.wikipedia.org/wiki/Cross-origin_resource_sharing
[localserver]: ./installation.md#local-development
[startplayback]: https://aframe.io/aframe/examples/test/video/
[videotestcode]: https://github.com/aframevr/aframe/blob/master/examples/test/video/index.html
[videoplaycomponent]: https://github.com/aframevr/aframe/blob/master/examples/js/play-on-click.js

まず、ローカルで開発を行っている場合は、アセットリクエストが正しく動作するように、[ローカルサーバを使用している][localserver]ことを確認すること。

異なるドメインからアセットを読み込む場合は、アセットに [CORS (cross-origin resource sharing) ヘッダ][cors] を付けて提供していることを確認してください。CORS ヘッダを使用してアセットを提供するホストを見つけるか、アプリケーションと同じドメイン（ディレクトリ）にアセットを配置することができます。

動画を読み込もうとしている場合、ブラウザが動画をサポートしていることを確認してください（エンコーディング、フレームレート、サイズなど）。

ビデオの自動再生はますます厳しくなっており、ブラウザによってルールが異なる可能性があります。現在では、ユーザーによる強制的なジェスチャーが一般的に行われています。最大限の互換性を確保するために、ユーザーがクリックして [ビデオ再生][startplayback] を開始できるボタンを提供することができます。[簡単なサンプルコード][videotestcode]は、docsに記載されています。特に、[play-on-click component][videoplaycomponent] に注意してください。

詳しくは、[*Hosting and Publishing* guide](./hosting-and-publishing.md) を読んでください。

## ブラウザのインスペクタで確認しても、HTMLが更新されないのはなぜですか？

[debug]: ../components/debug.md
[flushtodom]: ../core/entity.md#flushtodom-recursive

ブラウザの開発者ツールを開くと、HTML属性の値が空になっているのがわかります。

![HTML](https://cloud.githubusercontent.com/assets/674727/25720562/2b243bda-30c2-11e7-98d5-479157d20046.jpg)

A-Frameでは、パフォーマンス向上のため、HTMLの更新を行わず、文字列化操作を省力化しています。これは、突然変異の観測が行われないことも意味します。DOMに同期する必要がある場合は、[debugコンポーネント][debug] や [`.flushToDOM` メソッド][flushtodom] を使ってください。

## ビデオが再生されないのですが？

[iosvideo]: https://developer.apple.com/library/iad/documentation/UserExperience/Conceptual/iAdJSProgGuide/PlayingVideosinAds/PlayingVideosinAds.html

モバイル、そして現在ではデスクトップのブラウザでは、インラインビデオの再生に制限があります。

[iOS プラットフォームの制限][iosvideo] のため、インライン動画（自動再生の有無にかかわらず）を取得するためには、次のことを行う必要があります。

- メタ タグに `<meta name="apple-mobile-web-app-capable" content="yes">` を設定します（見つからない場合はインジェクションされます）。
- video 要素に `playsinline` 属性を設定する（すべての動画に自動的に追加される）。
- 古いバージョンの iOS では、Web ページをホームスクリーンに固定する可能性があります。

モバイル ブラウザとデスクトップ ブラウザは、バッテリーを節約し、邪魔な広告を避けるために、ビデオの自動再生ポリシーを厳しくしています。ほとんどのブラウザでは、ビデオの再生を開始するためにユーザーの操作（クリックまたはタップ イベントなど）が必要になっています。

-[Chrome for Android](https://bugs.chromium.org/p/chromium/issues/detail?id=178297)
-[Chrome desktop](https://www.chromium.org/audio-video/autoplay)
-[Safari](https://webkit.org/blog/7734/auto-play-policy-changes-for-macos/)
-[Firefox](https://hacks.mozilla.org/2019/02/firefox-66-to-block-automatically-playing-audible-video-and-audio/)

[video-playback-example]: https://aframe.io/aframe/examples/test/video/
[video-playback-code]: https://github.com/mayognaise/aframe-html-shader/

[動画再生に必要なロジックを含むA-Frameサンプル][video-playback-example]があり、ユーザーにクリックやタップを要求して動画再生を開始させます。[ソースコードも公開されています][video-playback-code]。

動画にはあまり力を入れていませんが、コミュニティからの有益な情報が含まれている可能性のあるGitHub issueを以下に紹介します。

- [*モバイルでビデオやビデオスフィアが機能しない*](https://github.com/aframevr/aframe/issues/316)
- [*iOSのビデオエンコードの制限を文書化*](https://github.com/aframevr/aframe/issues/1846)
- [*公式ビデオスフィアデモはモバイルでは動作しない*](https://github.com/aframevr/aframe/issues/2152)

## A-Frameで<<iframe>>を表示したり、HTMLをレンダリングするにはどうしたらよいですか？

WebGL 内でブラウザが `<iframe>` を表示する方法はありません。キャンバスの上に `<iframe>` を重ねることは可能ですが、`<iframe>` は VR で表示されませんし、シーンと統合されることもありません。

[html-shader]: https://github.com/mayognaise/aframe-html-shader/

とはいえ、基本的なHTMLやCSSは、インタラクティブ性を持たせずにテクスチャとしてレンダリングすることが可能です。<<canvas>` にペイントして、そのキャンバスをテクスチャのソースとして使用することができます。エコシステムには、これを可能にするコンポーネントがあります。

- [HTML Shader][html-shader]

## どの3Dモデル形式が有効ですか？

[gltf]: https://en.wikipedia.org/wiki/GlTF
[whygltf]: ../components/gltf-model.md#why-use-gltf

GL Transmission Format [glTF (`.gltf`)][gltf] は、機能が豊富で、コンパクトで、効率的だからです。glTFは、エディタ形式よりも、*伝送形式*の提供に焦点を当て、ウェブ技術との相互運用性が高いのです。[glTFとA-FrameのglTFコンポーネントについてもっと読む][whygltf].

[obj]: https://en.wikipedia.org/wiki/Wavefront_.obj_file

[Wavefront (`.obj`)][obj] もよく知られたフォーマットですが、アニメーションや頂点色のサポートがないなど、いくつかの制限があります。

エコシステムには、他のフォーマットを読み込むためのコンポーネントもあります。

- [`.PLY` models](https://github.com/donmccurdy/aframe-extras/blob/master/src/loaders/ply-model.js)
- [three.js `.JSON` Object](https://github.com/donmccurdy/aframe-extras/blob/master/src/loaders/json-model.js)
- [three.js `.JSON` Scene](https://github.com/donmccurdy/aframe-extras/blob/master/src/loaders/object-model.js)

以下は、モデルを使用するためのいくつかの基本的な例です。

- [Model Example 1](https://aframe.io/aframe/examples/test/model/)
- [Model Example 2](https://aframe.io/aframe/examples/primitives/models/)

## アセット（資産）はどこにあるのですか？

[awesomestock]: https://github.com/neutraltone/awesome-stock-resources

一般に、[awesome-stock-resources][awesomestock]は、素晴らしいコレクションです。
フリーアセット

[textures]: https://www.textures.com/

画像は、[textures.com][textures]をご覧ください。

[flickr]: https://www.flickr.com/groups/equirectangular/

360°画像は、[equirectangular images on Flickr][flickr] で検索してください。

3Dモデルについては、こちらをご覧ください。

- [Google Blocks](https://vr.google.com/objects)
- [Sketchfab](https://sketchfab.com)
- [Clara.io](http://clara.io)
- [Archive3D](http://archive3d.net)
- [Sketchup's 3D Warehouse](https://3dwarehouse.sketchup.com)
- [TurboSquid](http://www.turbosquid.com/Search/3D-Models/free)

音については、チェックしてみてください。

- [Freesound.org](http://www.freesound.org/)
- [Annual GDC Game Audio Bundles by Sonniss](http://www.sonniss.com/gameaudiogdc2016/)

## Vimeoの動画をテクスチャとしてレンダリングすることはできますか？

はい。[VimeoにはA-Frameプラグインがあります](https://github.com/vimeo/vimeo-webvr-demo)が、レンダリングは個人のVimeoアカウントからのビデオだけに限られます。

## YouTubeの動画をテクスチャとしてレンダリングすることはできますか？

[proxy]: https://github.com/cvan/webvr360

いいえ。テクスチャとして[プロキシ]したり、ローカルにダウンロードして提供することは可能ですが、それは彼らの利用規約に反しています。

## 自分のシーンにリンクを貼ることはできますか？

ブラウザは、WebVR 仕様に記載されている `vrdisplayactivate` イベントを介して、WebVR ページから WebVR ページへ移動する機能を提供します。現在のところ、すべてのブラウザがこれを実装しているわけではありません。WebVR を搭載した Firefox はこれを実装しています。リンクトラバーサルのためのリンクコンポーネントが A-Frame 0.6.0 でリリースされました。

```html
<a-entity link="on: click; href: https://aframe-aincraft.glitch.me"></a-entity>
```

## カメラが障害物を通過するのを防ぐことはできますか？

これは、サポートする予定のデバイスと、ユーザーにシーンをどのようにナビゲートさせるかによります。ほとんどのVR体験では、ベストプラクティスに従って、ユーザーの動きに比例してカメラを動かすだけにしてください。

[teleport]: https://github.com/fernandojsg/aframe-teleport-controls

ルームスケールのVR空間でユーザーが前に出た場合、カメラをブロックしないようにします。ほとんどの VR アプリケーションでは、[テレポート コントロール][teleport] を使用するなどの方法でロコモーションを行うか、障害物が邪魔にならないようにシーンを設計するか、あまり移動を必要としないようにするか、世界を通じてユーザーを移動させるより創造的な方法を検討する方がよいでしょう。

[physics]: https://github.com/donmccurdy/aframe-physics-system

ゲームパッドやキーボード操作による非VRデスクトップ体験や、カメラが乗り物の中にあるVRシーンでは、[物理エンジン][physics]を追加して障害物を通過して移動できないようにすることが可能です。

## A-Frameはどのようなユニットを使用しているのですか？

WebVR APIもメーターを使用するため、A-Frameでは1:1の割合でメーターを使用します。A-Frameの5単位は、現実の5メートルに相当します。さらに、Blenderのようなプログラムを使用し、インペリアルまたはメトリックモードで設定した場合、測定値も1:1に変換されます。Blenderの10フィートは、現実の10フィートに相当します。

## A-FrameはVRMLとどう違うのですか？

[extensible]: https://extensiblewebmanifesto.org/

A-Frameは、JavaScriptのフレームワークです。VRMLとは異なり、A-Frameは3Dファイルフォーマットでも、マークアップ言語でも、標準規格でもない。A-Frameは[Extensible Web Manifesto][拡張可能]を受け入れています。標準化については、勝てるアイデアが出てきたときだけ検討する。

技術的には、A-Frameはthree.jsの上に乗っている[entity-component-system][ecs]というゲームエンジンです。JavaScriptのフレームワークであるため、より複雑なアプリケーションではコーディングが必要になることが予想されます。3Dファイルフォーマットとは異なり、A-FrameはJavaScript、three.js、Web APIにフルアクセスすることでパワーとインタラクティブ性を提供します。

## A-Frameは「X」機能に対応していますか？

[aframecomponents]: https://github.com/aframevr/aframe/tree/master/src/components
[writingcomponent]: ./writing-a-component.md

A-Frameには、多くのコンポーネントやプリミティブが同梱されています。[エンティティ-コンポーネント-システムアーキテクチャ][ecs]の上に構築されているため、ある機能が存在しない場合、それを有効にするためのコンポーネントを[書く、見つける][writingcomponent]ことが可能です。また、標準コンポーネントの1つがユースケースに合わない場合は、それを[コピーして変更][aframecomponents]することができます。

[finding]: ./entity-component-system.md#where-to-find-components

詳しくは、[*Where to Find Components*][finding]をお読みください。

## A-Frameは、「X」というライブラリやフレームワークに対応していますか？

A-FrameはDOMの上に構築されているため、ほとんどのライブラリやフレームワークを含めて動作します。

- [Vue.js](https://github.com/frederic-schwarz/aframe-vuejs-3dio)
- [Preact](https://github.com/aframevr/aframe-react#using-with-preact)
- [D3.js](http://blockbuilder.org/search#text=aframe)
- [React](https://github.com/aframevr/aframe-react)
- [Angular](https://stackoverflow.com/questions/46464103/various-errors-when-attempting-to-integrate-aframe-into-angular2-project-esp-wi)
- jQuery
- Ember.js
- Meteor

## A-Frameはどのようなヘッドセット、ブラウザー、デバイス、プラットフォームに対応していますか？

[deviceplatform]: ./vr-headsets-and-webvr-browsers.md

そのほとんどが 詳しくは、*[VRヘッドセットとWebVRブラウザ][デバイスプラットフォーム]*をお読みください。

## パフォーマンスを向上させるには？

[bestpractices-perf]: ./best-practices.md#performance

詳しくは、*[ベストプラクティス &mdash; パフォーマンス][bestpractices-perf]*をお読みください。

## A-Frameのチームと連絡を取るにはどうすればよいですか？

私たちは、迅速な対応と親切な対応を心がけています 質問、フィードバック、バグレポート、プルリクエストをお待ちしています。

- [質問をお持ちですか？A-Frame StackOverflow タグ][stackoverflow]を使って私たちに質問してください。
- チャットしたいですか？コミュニティの[A-Frame Slackチャンネル][slack]で、私たちと一緒に過ごしましょう。
- [共有したいですか？twitter][@aframevr]で私たちにツイートしてください。
- [問題を見つけた？A-Frame GitHub リポジトリ][github]に課題を投稿してください。

## ロードマップはどこにあるのか？

[roadmap]: https://github.com/aframevr/aframe/blob/master/ROADMAP.md

[ロードマップはGitHubにあります][roadmap]!

## 「A-Frame」と呼ぶか、「aframe」と呼ぶか、「aframevr」と呼ぶか、「aFrame」と呼ぶか？

A-Frame!

## テクスチャが黒くレンダリングされるのはなぜですか？

[precision]: ../components/renderer.md#precision

Adreno 300シリーズGPUを搭載した携帯電話では、問題が発生する可能性があります。回避策として、[renderer precision][precision] を `medium` に設定してください。本当の修正は、ドライバ/デバイスレベルで行われる必要があります。

## ジャイロセンサー/マジックウィンドウモードが使えないのはなぜですか？

[新しいブラウザポリシー](https://www.w3.org/TR/orientation-event/#dom-deviceorientationevent-requestpermission)では、サイトがDeviceMotionEventにアクセスする前に、ユーザーに許可を求めるよう求めています。[iOS 13 から](https://webkit.org/blog/9674/new-webkit-features-in-safari-13/) DeviceMotionEvent は `https` で提供されるページに対してのみ利用可能です。他のブラウザも同じポリシーと制限を適用します。A-Frame は、ユーザーに必要なパーミッションを要求するために、カスタマイズ可能な UI を組み込みました](https://aframe.io/docs/1.0.0/components/device-orientation-permission-ui.html#sidebar). A-Frameの最新版](https://github.com/aframevr/aframe/releases)に必ずアップデートしてください。


