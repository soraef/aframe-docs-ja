---
title: "VR Headsets & WebXR Browsers"
type: introduction
layout: docs
parent_section: introduction
order: 3
---

[w3c]: https://immersive-web.github.io/webxr/

<!--toc-->

## VRとは何ですか？

VR（バーチャルリアリティ）とは、ディスプレイを搭載したヘッドセットを用いて、
リアルな映像や音などの感覚を生成し、ユーザーに没入感のある仮想環境を体験させる技術です。
VRでは、人々が歩き回ったり、手を使って操作したりすることで、
まるで別の場所に移動したかのような、拘束されない世界を作り出すことができます。


### ヘッドセットごとの違いは何ですか？

コンシューマー向けVRヘッドセットは、さまざまな機能を持つものが市場に出回っています。
重要な機能の、特徴的な差異は、以下のものです。

- 位置トラッキング（6自由度 （6DoF））か回転トラッキング（3自由度（3DoF））か。
- コントローラーがあるかないか、そのコントローラーが6DoFか3DoFか。一般的に、コントローラーの自由度数は、ヘッドセットの自由度数と一致します。
- Are powered by a PC or by a mobile device or standalone.
- PCから出力、制御されるものか、モバイル機器から出力、制御されるもの、スタンドアローンであるもの。

回転トラッキングは、物体の周囲を見回したり、回転させたりすることができます。すべてのヘッドセットは回転トラッキングを提供します。

位置トラッキングによって、動き回ったり、物体に近づいたり、前方に手を伸ばしたりすることができます。
VR業界が進歩するにつれ、最低限実現可能な体験は、位置トラッキング型ヘッドセットと位置トラッキング型コントローラーを持つという方向に向かうでしょう。
位置トラッキングは、人々に臨場感を与え、現実の環境にいることを実感させるために重要です。
回転トラッキングのみでは、周囲を見回したり、コントローラを動かしたりすることに制約されます。

### 現行のヘッドセットは何ですか？

[HTC Vive]: https://www.vive.com/
[Oculus headsets]: https://www.oculus.com/
[Google Daydream]: https://vr.google.com/daydream/
[Samsung GearVR]: http://www.samsung.com/global/galaxy/gear-vr/
[Windows Mixed Reality]: https://developer.microsoft.com/en-us/windows/mixed-reality/
[Vive Focus]: https://enterprise.vive.com/us/vivefocus/

| ヘッドセット                 | プラットフォーム   | 位置トラッキング | コントローラー        | コントローラーの位置トラッキング |
|-------------------------|------------|---------------------|--------------------|--------------------------------|
| [HTC Vive]              | PC         | :white_check_mark:  | :white_check_mark: | :white_check_mark:             |
| [Oculus Rift]           | PC         | :white_check_mark:  | :white_check_mark: | :white_check_mark:             |
| [Google Daydream]       | Android    | :x:                 | :white_check_mark: | :x:                            |
| [Samsung GearVR]        | Android    | :x:                 | :white_check_mark: | :x:                            |
| [Windows Mixed Reality] | PC         | :white_check_mark:  | :white_check_mark: | :white_check_mark:             |
| [Oculus Go]             | スタンドアローン | :x:                 | :white_check_mark: | :x:                            |
| [Vive Focus]            | スタンドアローン | :x:                 | :white_check_mark: | :x:                            |
| [Oculus Quest]            | スタンドアローン | :white_check_mark:  | :white_check_mark: | :white_check_mark:             |
| [Oculus Quest 2]            | スタンドアローン | :white_check_mark:  | :white_check_mark: | :white_check_mark:             |

## WebXRとは何ですか？

WebXRは、ブラウザ上で没入型3D、VR（およびAR）体験を作成するためのJavaScript APIです。
簡単に言うと、Web上のブラウザでVRを可能にします。

A-Frameは、WebXR APIを使用してVRヘッドセットのセンサーデータ（位置、向き）にアクセスし、カメラの変換やVRヘッドセットへの直接コンテンツのレンダリングを行います。
WebXRはデータを提供するもので、グラフィックスやレンダリングを提供するWebGLと混同しないように注意してください。

## VRをサポートしているブラウザは何ですか？

[Supermedium](https://supermedium.com) と
[Exokit](https://github.com/exokitxr/exokit)を含みます：

<iframe src="https://caniuse.com/#search=webxr" height="480px" width="100%"></iframe>

## A-FrameはWebXRをどのようにしたいのですか？

A-Frameが目指すのは、ネイティブに近いパフォーマンスで、没入感の高いインタラクティブなVRコンテンツです。
そのために、A-Frameは、位置追跡型ヘッドセットと位置追跡型コントローラが最低限実現可能な水準になると考えています。
これは、A-Frameがイノベーションを起こすと同時に、VRウェブに特有の新しい知見（例：リンクトラバーサル、分散化、アイデンティティ）を発見したいパラダイムなのです。
このようなコンテンツは、フラットで静的な360°コンテンツやメニューとは対照的です。

同時に、A-Frameは、誰もがVRコンテンツ制作に携われるようにしたいと考えています。
A-Frameは、すべての主要なヘッドセットとそのコントローラをサポートしています。
幸いなことに、大規模なコミュニティと貢献者のおかげで、A-Frameは将来の展望を描くと同時に、現在のVR環境のニーズを満たすことができるのです。


## A-Frameはどのプラットフォームに対応していますか？

A-Frameは、ブラウザを通じてほぼ全てのプラットフォームに対応しています。A-Frameが対応している一般的なプラットフォームは以下の通りです。

- デスクトップでのVR（ヘッドセット使用）
- モバイルでのVR（ヘッドセット使用）
- スタンドアローンヘッドセットでのVR
- デスクトップでのフラット3D（マウスとキーボードなど）
- モバイルのフラット3D（magic windowなど）

その他、A-Frameとの連携が確認されているプラットフォームとしては、以下のようなものがあります。

- ARヘッドセット上の拡張現実（AR）（例：Magic Leap、HoloLens)
- モバイル上の拡張現実（AR）（例：magic window、ARKit、ARCore)

## A-FrameはどのVRヘッドセットに対応していますか？

A-Frameは、ブラウザを通してほとんどのヘッドセットに対応しています。A-Frameが対応しているVRヘッドセットには、以下のようなものがあります。

- HTC Vive
- Oculus Rift
- Oculus Quest
- Oculus Go
- Google Daydream
- Samsung GearVR
- Vive Focus

一般的な推奨ハードウェア（要件ではありません）については、こちらをご覧ください。

- [Oculus Riftハードウェアの推奨環境](https://www.oculus.com/en-us/oculus-ready-pcs/)
- [HTC Viveハードウェア推奨環境](https://www.vive.com/us/ready/)
- スマートフォンの場合、iOSはiPhone 6、AndroidはGalaxy S6以上

## A-Frameはどのブラウザに対応していますか？

A-Frameは、[WebXR仕様][w3c]を実装しているブラウザであればVRを、ほとんどのブラウザでフラット3Dをサポートしています。
大手ブラウザベンダーは徐々にWebXR仕様に移行していますが、A-Frameの開発者が直面する変化はあまりなく、APIの名称変更が主な内容となっています。

- [Supermedium](https://www.supermedium.com) (OculusとSteamで入手可能です)
- Firefox
- Oculus Browser
- Samsung Internet
- Microsoft Edge
- Chrome (オリジン試験版のWebXR)
- Exokit (実験的なサポート)

[webvrpolyfill]: https://github.com/googlevr/webvr-polyfill

A-Frameは、[WebVR polyfill][webvrpolyfill]を介して、WebXRをサポートしていないほとんどのモダンなモバイルブラウザをサポートしています。
これらのブラウザは公式のWebXRサポートを持っておらず、ポリフィルを使用していることに注意してください。これらのブラウザが高品質の体験を提供し、互換性があると期待しすぎないことが重要です。

- Safari for iOS
- Chrome for Android
- Firefox for iOS
- Samsung Internet
- UC Browser

A-Frameは、フラットな3Dをサポートするために、WebGLをサポートしているため、すべてのモダンブラウザをサポートしています。

- Firefox
- Chrome
- Safari
- Edge
- Internet Explorer 11