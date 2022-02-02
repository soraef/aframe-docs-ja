---
title: Installation
type: introduction
layout: docs
parent_section: introduction
order: 2
---

このインストールについてのセクションでは、A-Frameを使い始めるためのいくつかの方法を紹介していますが、A-FrameはHTMLとJavaScriptが主体なので、ほとんどの方法では実際のインストールは必要ありません。

<!--toc-->

## ブラウザ上のコードエディタ

一番手っ取り早いのは、ブラウザで動かしてみる方法です。

### Glitchで動かす

![Glitch](https://cloud.githubusercontent.com/assets/674727/24480466/54b17d22-1499-11e7-8a18-d4f76b49ad07.jpg)

[glitch]: https://glitch.com/~aframe

[Glitch][glitch] provides an online code editor with instant deployment and
hosting of web sites. The editor supports both front-end and back-end code as
well as multiple files and directories. Glitch lets us remix (i.e., copy)
existing projects and make them our own and instantly host and deploy changes
for everyone to see.

[Glitch][glitch]は、Webサイトの即時デプロイとホスティングを可能にするオンライン・コード・エディタを提供します。
このエディタは、フロントエンドとバックエンドの両方のコードと、複数のファイルやディレクトリをサポートしています。
Glitchは、既存のプロジェクトを改変（コピーした上で）して自分たちのものにし、変更を即座にホストして誰もが見られるように配備することを可能にします。

このA-Frameプロジェクトの[**Remix your own**][glitch]で、`index.html`のHTMLをいじると、変更するたびにあなたのサイトがライブで公開されるのを見ることができます！
例えば、ベースとなるA-Frame Glitchは `aframe.glitch.me` で公開されていますが、Remixではあなた自身のカスタムURL名を提供します。

以下は、その他のA-FrameのスターターGlitchです。

- [aframe-aincraft](https://glitch.com/~aframe-aincraft) - Minecraftのデモ
- [aframe-gallery](https://glitch.com/~aframe-gallery) - 360&deg; イメージギャラリー
- [aframe-registry](https://glitch.com/~aframe-registry) - 各種コンポーネントのショーケース
- [aframe-vaporwave](https://glitch.com/~aframe-vaporwave) - レトロフューチャーなシーン
- [networked-aframe](https://glitch.com/~networked-aframe) - マルチユーザー

### その他のコードエディタ

Below are a couple of A-Frame starter kits on other browser-based code
editors. Both support remixing or forking:

以下は、他のブラウザベースのコードエディタ上のA-Frameスターターキットです。どちらもリミックスやフォークをサポートしています。

- [CodePen &mdash; A-Frame](https://codepen.io/mozvr/pen/BjygdO)

## ローカル開発

### ローカルサーバーを使用する

以下のオプションについては、ローカルサーバーを使用してプロジェクトを開発し、ファイルが適切に提供されるようにする必要があります。ローカルサーバーのオプションは以下の通りです。

- HTMLファイルと同じディレクトリで、`npm i -g five-server@latest && five-server --port=8000`をターミナルで実行します。
- HTMLファイルと同じディレクトリで `python -m SimpleHTTPServer` (Python3の場合は `python -m http.server`) をターミナルで実行します。

Once we are running our server, we can open our project in the browser using
the local URL and port which the server is running on (e.g.,
`http://localhost:8000`). Try *not* to open the project using the `file://`
protocol which does not provide a domain; absolute and relative URLs may not
work.
サーバーを起動したら、サーバーが動作しているローカルのURLとポートを使って、ブラウザでプロジェクトを開くことができます (例: `http://localhost:8000`)。
ドメインを指定しない `file://` プロトコルを使ってプロジェクトを開くことは*しない*ようにしてください。

### GitHubでボイラープレートをダウンロードする

[ghpages]: https://pages.github.com/

ボイラープレートは:

- 現在の[A-Frameのバージョン](#builds-prod)にリンクするシンプルなHTMLファイルです。
- オプションのローカル開発サーバー
- [GitHub Pages][ghpages]で共有するための簡単なデプロイワークフロー

を含みます。

ボイラープレートを取得するには、以下の2つの方法があります。

<a class="btn btn-download" href="https://github.com/aframevr/aframe-boilerplate/">GitHubでフォーク</a>
<br>なお、これは更新停止となっており、これに同梱されているAframeのバージョンは0.5です。）

<a class="btn btn-download" href="https://github.com/aframevr/aframe-boilerplate/archive/master.zip" download="aframe-boilerplate.zip">.zipファイルをダウンロード<span></span></a>

### JSのビルドをインクルード

HTMLファイルにA-Frameを含めるには、CDNビルドを指す`<script>`タグを追加します。

```html
<head>
  <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
</head>
```

自分でライブラリを用意したい場合は、JSビルドをダウンロードすることができます。

<a id="builds-prod" class="btn btn-download" href="https://aframe.io/releases/1.2.0/aframe.min.js" download>プロダクトバージョン <span>1.2.0</span></a> <em class="install-note">最小化済み</em>
<a id="builds-dev" class="btn btn-download" href="https://aframe.io/releases/1.2.0/aframe.js" download>開発バージョン <span>1.2.0</span></a> <em class="install-note">ソースマップ付き非圧縮</em>

### npmからインストール

また、A-Frameをnpmでインストールすることもできます。

```bash
$ npm install aframe
```

そして、BrowserifyやWebpackなどを使ってA-Frameをアプリケーションにバンドルすることができます。

```js
require('aframe');
```

[angle]: https://www.npmjs.com/package/angle

npmを使用する場合、A-Frameのコマンドラインインターフェースである[`angle`][angle]を使用することができます。 `angle` は、1つのコマンドでシーンテンプレートを初期化することができます。

```sh
npm install -g angle && angle initscene
```

## Cordovaで開発

A-Frame is compatible with Cordova apps. Currently, network access is required as A-Frame and its dependencies load assets from CDN sources.
A-FrameはCordovaアプリと互換性があります。現在、CDNソースからアセットを読み込むというA-FrameとCordovaの依存関係のため、ネットワークアクセスが必要です。

[Cordova A-Frame ショーケースアプリ(demo)](https://github.com/benallfree/cordova-aframe-showcase)

### インストール

Install the [cordova-plugin-xhr-local-file](https://github.com/benallfree/cordova-plugin-xhr-local-file) plugin. This is needed because
Cordova runs from `file://`, and XHR requests to local `file://` assets (JSON fonts, 3D models, etc) will fail without this plugin.

```bash
cordova plugin add cordova-plugin-xhr-local-file
```

In your `index.html`, adjust as follows:

```html
<head>
  <meta
        http-equiv="Content-Security-Policy"
        content="
          default-src 
            'self' 
            data: 
            gap: 
            https://ssl.gstatic.com 
            'unsafe-eval' 
            https://cdn.aframe.io         <-- required
            https://dpdb.webvr.rocks      <-- required
            https://fonts.googleapis.com  <-- required
            https://cdn.jsdelivr.net      <-- your choice, see below
            ; 
          style-src 
            'self' 
            'unsafe-inline'
            ; 
          media-src *; 
          img-src 
            'self' 
            data:                         <-- required
            content:                      <-- required
            blob:                         <-- required
            ;
        "
      />
  ...
  <script src="https://cdn.jsdelivr.net/npm/aframe@1.2.0/dist/aframe-master.min.js"></script>
  <script id='my-scene' type="text/html">
    ...your scene goes here...
  </script>
  <script>
    document.addEventListener('deviceready', function() {
      // After the 'deviceready' event, Cordova is ready for you to render your A-Frame scene.
      document.getElementById('scene-root').innerHTML = document.getElementById('my-scene').innerHTML
    })
  </script>
</head>
<body>
  <div id='scene-root'></div>
  ...
</body>
```

### 議論


#### デバイスレディー
The most important difference between a browser environment and a Cordova environment is waiting for the `deviceready` event
before rendering your scene. 

The sample above shows a pure DOM+JS approach, but you can also use a framework like React:

```javascript
document.addEventListener('deviceready', () => {
  ReactDOM.render(<Root />, document.getElementById('root'))
})
```

#### Layout

Depending on your target device, you may find that A-Frame's default CSS causes certain buttons and controls to appear out of
position or too close to the edge of the phone screen. Supply your own CSS overrides to adjust positioning to fit
the target device.
