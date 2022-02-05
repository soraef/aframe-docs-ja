---
title: Visual Inspector & Dev Tools
type: introduction
layout: docs
parent_section: introduction
order: 11
---

[inspector]: https://github.com/aframevr/aframe-inspector
[motioncapture]: https://github.com/dmarcos/aframe-motion-capture-components

ここでは、VRの開発体験を向上させる多くの便利なツールについて説明します。

- [**A-Frame Inspector**][inspector] - シーンの異なるビューを取得し、エンティティを微調整したときの視覚効果を確認するためのインスペクタツールです。ブラウザのDOMインスペクタのVRアナログです。どのA-Frameシーンでも、`<ctrl> + <alt> + i`で開くことができます。
- キーボードショートカット
- [**モーションキャプチャー**][motioncapture] - ヘッドセットとコントローラーのポーズとイベントを記録し、再生するためのツールです。録画ボタンを押して、VRヘッドセット内を動き回り、コントローラーでオブジェクトとインタラクションします。そして、その記録を任意のコンピュータで再生し、迅速な開発とテストを行うことができます。ヘッドセットへの出入りを減らすことができます。

A-Frameの上に構築され、コードなしで使用できるGUIツールについて説明します。また、複数のマシンにまたがって開発を容易にするその他のツールについても触れます。

<!--toc-->

## A-Frame Inspector

[A-Frame Inspector][inspector]は、シーンの検査や微調整を行うためのビジュアルツールです。インスペクタを使うと、以下のことができます。

- ハンドルとヘルパーを使ったエンティティのドラッグ、回転、拡大縮小
- ウィジェットを使ったエンティティのコンポーネントとそのプロパティの調整
- コードとブラウザを行き来することなく、値を変更した結果を即座に確認できる

インスペクターは、ブラウザのDOMインスペクターに似ていますが、3DとA-Frame用に調整されています。インスペクタを切り替えると、どんなA-Frameのシーンでも開くことができます ソースを表示しよう

![Inspector Preview](https://cloud.githubusercontent.com/assets/674727/17754515/b5596eb6-6489-11e6-9485-4cb10fa5b100.png)

### Opening the Inspector

最も簡単な方法は、キーボードの**<ctrl> + <alt> + i`**のショートカットを押すことです。これにより、CDN経由でInspectorのコードが取得され、Inspectorでシーンが表示されます。同じショートカットで、Inspectorを閉じることもできます。

ローカルのシーンをインスペクタで開くだけでなく、（作者が明示的に無効にしていない限り）インスペクタを使用して野生のあらゆるA-Frameシーンを開くことができます。

インスペクタのローカル、開発、カスタムビルドの提供に関する詳細は、[Inspector README][inspector]を参照してください。

### Using the Inspector

#### Scene Graph

インスペクタのシーングラフは、シーンをツリーベースで表現したものです。シーングラフを使用して、*選択、検索、削除、クローン、エンティティの追加*やHTMLの書き出しができます。

![Inspector Scene Graph](https://cloud.githubusercontent.com/assets/674727/18565455/ae082fea-7b44-11e6-856f-2b9ca0e60bed.gif)

シーングラフには、three.jsの内部オブジェクトではなく、A-Frameのエンティティがリストアップされます。HTMLもシーングラフの表現であるため、インスペクタのシーングラフは、基礎となるHTMLを忠実に反映しています。エンティティは、HTML IDまたはHTMLタグ名を使って表示されます。

#### Viewport

ビューポートは、インスペクタから見たシーンを表示します。ビューポートを回転、パン、ズームして、シーンの見え方を変えることができます。

- **回転:** 左マウスボタンを押したまま（またはトラックパッドで指を1本下げたまま）ドラッグします。
- **パン:** マウスの右ボタンを押しながら（またはトラックパッドで2本の指を押しながら）ドラッグします。
- **ズーム:**上下にスクロール（またはトラックパッドの2本指スクロール）

また、ビューポートから、エンティティを選択して変形させることができます。

- **選択**：エンティティを左クリック、ダブルクリックでカメラフォーカス
- **変形**：ビューポート右上のヘルパーツールを選択し、エンティティを囲む赤・青・緑のヘルパーをドラッグすると変形します。


![Inspector Viewport](https://cloud.githubusercontent.com/assets/674727/18565454/ad047c84-7b44-11e6-8c4a-0f1fe55c6682.gif)

#### Components Panel

コンポーネントパネルには、選択したエンティティのコンポーネントとプロパティが表示されます。共通コンポーネントの値の修正（位置、回転、スケールなど）、付属コンポーネントの値の修正、ミキシンの追加と削除、コンポーネントの追加と削除を行うことができます。

各プロパティのウィジェットの種類は、プロパティの種類に依存する。例えば、ブーリアンにはチェックボックス、数値には値スライド、色にはカラーピッカーが使用される。

個々のコンポーネントのHTML出力をコピーすることができる。これは、視覚的に微調整してコンポーネントの望ましい値を見つけ、それをソースコードに同期させるのに便利です。


![Inspector Components](https://cloud.githubusercontent.com/assets/674727/18565449/aa63a7b6-7b44-11e6-999c-450c88812293.gif)

#### Shortcuts

**`h`**キーを押すと、すべてのショートカットのリストが表示されます。

## Mouse and Keyboard Shortcuts

[debugcursor](https://www.npmjs.com/package/aframe-debug-cursor-component)

`<a-entity cursor="rayOrigin: mouse"></a-entity>` を使用すると、マウスを使ってエンティティをホバーしたりクリックしたりすることができます。これらは `mouseenter`, `mouseleave`, `click` イベントを提供し、コントローラ (`<a-laser-controls>` など) でカーソルを使用したときと互換性があります。カーソルイベントを記録するための [debug-cursor component] (debugcursor) があります。

`document.addEventListener('keydown', function (evt) { // ... });`を使用して、アプリケーション内のアクションをテストするためのキーボードショートカットバインディングを開発すると便利です。


[debugcontrol]: https://gist.github.com/ngokevin/803e68351f70139da51fda48d3b484e3

[6DoFのコントローラーをキーボードで操作できるコンポーネント][debugcontrol]もあります。

## Motion Capture

ルームスケールのVRは、開発が面倒です。コードを変更するたびに

- ウェブページを開く（多くの場合、別のコンピューターで実行される）。
- VRに入る
- ヘッドセットを装着する
- コントローラを手に取る（しばしば電源を入れ直す必要がある）
- ヘッドセットとコントローラーを使用してのテストラン
- ヘッドセットとコントローラーを外し、ブラウザの開発ツールを起動します。
- 現在は実験的でバグが多いため、必要に応じてブラウザを再起動してください。
- 繰り返し

ルームスケールのVR開発は糖蜜になる。しかし、私たちは、VR開発の自動化、高速化、そして外出先での開発を可能にするワークフローを考え、[A-Frame Motion Capture Components](https://github.com/dmarcos/aframe-motion-capture-components/)を採用しました。

モーションキャプチャーコンポーネントを使えば、ヘッドセットやコントローラーの動き、コントローラーのボタン操作などのVRアクションを記録し、そのVRアクションを、ヘッドセットなしでどこからでも、どんなデバイスでも繰り返し再生することができるのです。

### Use Cases

![Rapid Development](https://cloud.githubusercontent.com/assets/674727/24733615/9cb99dae-1a2d-11e7-85e3-a75d6c06bb91.gif)

以下は、モーションキャプチャーによってVR開発の人間工学を大幅に改善した実際の使用例です。

1. **テストトライアルの高速化:** ヘッドセットの着脱、VRへの進入、コントローラーの操作、手動操作、ブラウザの再起動は必要ありません。一度録画するだけで、何時間も開発することができます。

2. **外出先での開発**: 何かをテストするたびにヘッドセットとVRに入り直すのではなく、録画したものを例えばMacbookに送ってコーヒーショップに出かけ、安定したブラウザで録画したものを使ってVRアプリケーションの開発を続けることができるのです。また、A-Frame Inspector (`<ctrl> + <alt> + i`) を使ってリプレイをフリーズさせ、いろいろと調べることができます。

3. **自動化された統合テスト**: リグレッションテストケースやQAとして、様々なレコーディングを何度も行うことができます。録音を保存し、開発を行い、時々録音をクリックして、すべてがまだ機能していることを確認します。複数の録画ファイルをプロジェクトに保存して、後でテストすることができます。

4. **複数の開発者が1つのヘッドセットを共有する**。一人の開発者がViveで録音を取り、その録音を使って開発のためにどこかに出かけても、Viveは他の開発者が使用したり録音を取ったりするために自由に使える状態になります。

5. **録画のリクエスト**: 私たちはViveやRiftを手にしていないけれど、同僚や友人は持っているかもしれません。彼らに私たちのウェブアプリケーションへのリンクを[ngrok](https://ngrok.io)(ウェブってすごいでしょう？)を介して与え、彼らに録音してもらい、それを私たちに送ってください。これで、私たちは開発から解放されました。

6. **バグの実証**: あるいは、VRウェブアプリケーションでバグを発見し、それを誰かに見せたいとします。録音をとって、それを送ってデバッグしてもらいましょう。バグ再現の手順を説明する必要はなく、すべて録画に含まれています

7. **ユニットテストの自動化**: KarmaやMochaのようなユニットテストフレームワークで録音を再生し、アサーションを行うために録音を使用することができます。例えば、箱をタッチして、色が変わるかどうかをチェックします。例として、[William Murphy]による[A-Frame Machinima Testing][machinima]を参照してください。 
[machinima]: https://github.com/wmurphyrd/aframe-machinima-testing/
[William Murphy]: https://twitter.com/datatitian

### How to Record

詳しくは、[Motion Capture documentation](https://github.com/dmarcos/aframe-motion-capture-components/)をお読みください。以下は録画の設定方法です。

1. モーション・キャプチャ・コンポーネントのスクリプト・タグをHTMLファイルにドロップします（例：`<script src="https://unpkg.com/aframe-motion-capture-components/dist/aframe-motion-capture-components.min.js">`）。
2. シーンに `avatar-recorder` コンポーネントを追加します (例: `<a-scene avatar-recorder>`)
3. VRに入る
4. スペースキーを押して録音を開始します。
5. 動きや動作を記録する
6. <スペース>` を押して録音を停止します。
7. 録画したJSONファイルを保存するか、`u`を押してアップロードし、コンピュータ間で共有するための短いURLを取得します。

さて、録画したものを再生してみましょう。デスクトップで今すぐWASDとマウスのドラッグ操作でカメラの録画を試してみましょう。Record Example](http://dmarcos.github.io/aframe-motion-capture-components/)にアクセスし、ブラウザのConsoleを開いてフィードバックをもらい、`<space>`を押して録画の開始と停止を行います!

![Record](https://cloud.githubusercontent.com/assets/674727/24766726/3d660b7e-1ab1-11e7-9f64-d97b5732ec43.gif)

### How to Replay

デフォルトでは、録画もlocalStorageから保存され、再生されます。もし、外出先で録画したものを持ち運びたい場合は、以下のように再生します（すでに上記のスクリプトタグがあると仮定しています）。

1. ウェブページからアクセスできる場所に録画ファイルを置く (プロジェクトディレクトリやオンラインなど)
2. シーンに `avatar-replayer` コンポーネントを追加します (例: `<a-scene avatar-replayer>`)
3. URLに `?avatar-recording=path/to/recording.json` を追加 *または* `<a-scene avatar-replayer="src: path/to/recording.json">` と設定する。

そして、ヘッドセットなしで、どこからでも、どんなデバイスでも、心ゆくまで録音を再生することができます。ヘッドセットを装着して、クリックをいくつか記録し、ラップトップから、記録で発したコントローライベントの上にイベントハンドラを構築できます。

### Spectator Mode

A-Frameモーションキャプチャコンポーネントには、`アバター-プレーヤー`コンポーネントから有効にできるSpectatorモード機能があります。

```
<a-scene avatar-replayer="spectatorMode: true">
```

これにより、三人称視点での録画を見ることができます。一人称視点だと何が起きているのかがわかりにくいので、これは便利な機能です。一人称視点は当然手ぶれしますし、手がカメラの邪魔をしたり、画面の外で動作していたり、カメラが常に遠ざかっていると一箇所に集中することが難しくなります。Spectator Modeでは、シーンを自由に動き回り、好きな角度で見たり、好きな場所に焦点を当てたりすることができます。

![Spectator Mode](https://cloud.githubusercontent.com/assets/674727/24733331/f2bc6094-1a2b-11e7-9442-bcf30d18af79.gif)

## GUI Tools Based on A-Frame

A-Frameの上に、インターフェイスやアプリケーションを介してコードを抽象化し、コンテンツ制作をより簡単にするツールを構築した人たちがいます。これらは、開発者用ツールというよりも、より完全なエディターとして機能しています。

### [Supercraft](https://supermedium.com/supercraft)

自分の手でVRサイトを構築し、すべてVRで、Webに公開する。

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/ONhhKHjSO-4?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

### [Ottifox](http://ottifox.com/)

*Ottifoxは、ブラウザ上で動作するインタラクティブなVRシーンを簡単に作成できる、強力なデザイン・プロトタイピングソリューションです」*。

![Ottifox](https://cloud.githubusercontent.com/assets/674727/25638463/5cc90a3e-2f3d-11e7-987e-d8d299773f67.jpg)

### [Hologram](http://hologram.cool/)

*WebVRをオールインワンで実現*。

![Holgoram](https://user-images.githubusercontent.com/674727/36452389-24416be0-1649-11e8-8d7d-4a3a69097650.png)

### [Fader](https://fader.vragments.com/)

*今すぐあなただけのVRストーリーを作ってください。Faderでは、VRストーリーを作成し、公開することができます。360球に複数の情報レイヤーを追加し、シーンをデザインし、あなたのストーリーを伝えましょう。簡単、高速、Webベース！"*。

![Fader](https://cloud.githubusercontent.com/assets/674727/25638761/53535724-2f3e-11e7-90f2-5efb3a58c549.jpg)

## Cross-Machine Development

VR開発では、複数のマシンにまたがって開発することが一般的です。例えば、ノートパソコンで開発し、VRデスクトップでテストするといった具合です。その際に役立つのが、以下のツールです。

### Synergy

![Synergy](https://cloud.githubusercontent.com/assets/674727/25640613/07a10392-2f45-11e7-94a5-f07cf8e32ad9.jpg)

[Synergy](https://symless.com/synergy)は、1つのマウスとキーボードを複数のコンピュータで共有できるようにするものです。例えば、ノートパソコンからデスクトップを操作することができます。ラップトップからコードを書くこともできます。そして、ノートパソコンを使って、デスクトップを操作して、ブラウザを更新したり、VRを入力したり、別のURLにアクセスしたり、モーションキャプチャーの記録を取ったり、ブラウザの開発者ツールを調べたりすることができます。机の上にキーボードとマウスを2セット置く必要はありません。


Synergy Basicのライセンスは19ドルですが、複数のコンピュータで開発するのであれば、その価値は十分にあると思います。


### ngrok

![ngrok](https://cloud.githubusercontent.com/assets/674727/25640652/38effdcc-2f45-11e7-99a9-c98bd694e79c.jpg)

[ngrok](https://ngrok.io) lets us easily expose a local development server for other computers to access, even through firewalls or NAT networks. The steps are to:


1. ngrokのダウンロード
2. コマンドラインを開き、ngrokがダウンロードされたのと同じディレクトリに移動します。
3. 開発用のローカルサーバを起動しておく (例: `python -m SimpleHTTPServer 8080`)
4. ./ngrok http <PORT>` を実行します（例：`./ngrok http 8080` ）。
5. ngrokインスタンスがスピンアップし、相手のコンピュータがブラウザからアクセスできるURLを提供します（例：`https://abcdef123456.ngrok.io`）。

ngrokは、プレミアムアカウントを取得し、覚えやすいURLを得るために独自のドメインを予約すれば、最も人間工学的に優れています。そうでなければ、URLは毎回ランダムになり、非常に入力しにくい。基本ライセンスでは、3つの予約ドメインが$5/月で提供され、プロライセンスでは2つの同時インスタンスが$8.25/月で提供されます。[ngrokの価格詳細はこちら](https://ngrok.com/product#pricing)。

予約ドメインでは、`https://abc.ngrok.io`のようなURLを予約することができる。なお、`abc`は一例であり、現在は取得されています。そして、ngrokを起動するたびに、このドメインを渡します。


```
./ngrok http --subdomain=abc 8080
```
ngrokを起動するたびに、確実に同じURLを使用することができます。さらに簡単にするために、Bashのエイリアスを追加することもできます。


```
alias ngrok="~/path/to/ngrok http --subdomain=abc"
```

そして、どこからでもコマンドラインから簡単に使用することができます。

```
ngrok 8080
```

あるいは、両方のコンピュータを同じローカルネットワークに接続し、 `ipconfig` や `ifconfig` を使って、一方のコンピュータからもう一方のコンピュータにローカルIPアドレス (例: `http://192.168.1.135:8000`) を指すようにすることもできます。しかし、ローカルのIPアドレスを取得するためにコマンドを実行しなければならず、ローカルのIPアドレスはしばしば変更され、IPを覚えて入力するのが難しいため、ワークフローを混乱させる可能性があります。

### Motion Capture

モーションキャプチャについては前述しましたが、改めて説明すると、モーションキャプチャはマシンをまたいだ開発に非常に役立ちます。VRで撮影した映像は共有でき、他のPCで再生することも可能です。モーションキャプチャーを `<space>` で撮影した後、キーボードの `u` を押すと、撮影データがアップロードされ、簡単に転送できる URL が得られます (自分たちでメールするよりも簡単です)。また、[file.io](https://file.io)を使って録画データを転送することもできます。
