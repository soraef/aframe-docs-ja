---
title: "Hosting & Publishing"
type: introduction
layout: docs
parent_section: introduction
order: 12
---

このセクションでは、A-FrameサイトとそのアセットをWeb上に展開、ホスト、公開する方法をいくつか紹介します。

<!--toc-->

## Publishing a Site

サイトをデプロイし、ホストするための無料のサービスはたくさんあります。ここでは、より簡単で人気のあるオプションをいくつか紹介しますが、AWS、Heroku、またはセルフホスティングなどの他のオプションがあることは確かです。重要な注意点は、ブラウザの WebVR API の一般的なセキュリティ制限のため、これらのサイトは SSL/HTTPS で提供する必要があることです。以下のオプションはすべて SSL/HTTPS で提供されます。


### Glitch

![Glitch](https://cloud.githubusercontent.com/assets/674727/25643449/b5ee2542-2f54-11e7-9d45-22f3aa0b208f.jpg)

*Glitchは、あなたが夢のアプリを作ることができるフレンドリーなコミュニティです。リミックスするためのサンプルアプリ、それらを修正するためのコードエディタ、即時ホスティングとデプロイメントがあり、誰でも無料でGlitchでウェブアプリを作ることができます。*


[Glitch](https://glitch.com)は、ブラウザからサイトを作成し公開する最も簡単かつ最速の方法です。Glitchでは、コードやファイルの追加、アセットのアップロード、他のユーザーとの共同編集、独自のURL名の定義、そして変更するたびに即座にデプロイすることができます。アカウント作成もログインも必要ありません。


1. [A-Frame Starter Glitch](https://glitch.com/~aframe/)にアクセスしてみてください。
2. **Remix your own** を押して、プロジェクトをコピーします。
3. 左上の*Project info and options*アイコンをクリックし、アプリケーションの名前を変更します（例：`https://yoursitename.glitch.me`）。
4. HTMLを編集し、ファイルを追加し、プロジェクトを変更します。
5. **Show**をクリックして、アプリケーションを表示します(例: スターターグリッチは`https://aframe.glitch.me`にホストされています)。
6. プロジェクトを変更するたびに、アプリケーションは即座に更新されます。これは、サインインして自分のアバターをクリックし、**Refresh App on Changes**をトグルすることでオフにすることができます。


### Neocities

![Neocities](https://cloud.githubusercontent.com/assets/674727/25643397/6db47790-2f54-11e7-9eb3-ac18a1513e9f.jpg)

*「自分だけの無料ホームページを作ろう 無限の創造性、広告ゼロ。ネオシティーズは、失われた個人の創造性を取り戻すために、129,100のウェブサイトからなるソーシャルネットワークです。私たちは、無料のウェブホスティングと、あなた自身のウェブサイトを作成することができるツールを提供しています。参加してください！」*。

[Neocities](https://neocities.org)もまた、ブラウザ内からサイトを作成し公開するための、無料で簡単な方法です。Glitchのような機能はありませんが、Neocitiesはフレンドリーで、CDNではなくプロジェクトディレクトリにアセットをアップロードすることができます。このため、少なくともモデルのホスティングについては、Neocitiesの方が優れています。Neocitiesでは、ファイルを作成し、編集することができます。そして、それらは私たちのためにホストされ、公開されます（例：`ngokevin.neocities.org`）。


![Neocities Editor](https://cloud.githubusercontent.com/assets/674727/25643399/704cffe0-2f54-11e7-8d32-868b51407f81.jpg)

## Hosting Assets

また、オーディオ、テクスチャ、モデル、ビデオなどのアセットのホスティングについても説明します。

A-Frameサイトが同じディレクトリ（つまり、同じドメイン）にアセットと一緒に公開されている場合、アセットのホスティングについてそれほど心配する必要はありません。A-Frameサイトは、相対URLを使ってアセットを参照することができ、同じドメイン上にあるので、そのアセットを取得することに問題はありません。例えば、すべてのリソースを同じルートディレクトリに置き、Neocities、GitHub Pages、またはSurge経由ですべてを公開しても、問題は発生しないでしょう。


## Content Delivery Network (CDN)

CDNなど外部でアセットをホスティングする場合は、考慮する必要があります。アセットに対する第一の要件は、[Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/Access_control_CORS) を有効にして提供されることです。これにより、A-Frameサイトがシーンに表示するアセットを取得することができます。  さらに、`<a-assets>` を使用している場合、通常、`<img>`、`<audio>`、`<video>` などのアセットには `crossorigin="anonymous"` を設定する必要があります。

CDN経由でアセットをホスティングするための簡単なオプションがいくつかあります。

- [Glitch Asset Uploader](https://glitch.com/) - Glitchのコードエディタには、アセットをアップロードしてCDNのURLを取得するためのパネルがあります。
- [imgur](https://imgur.com/) - 画像については、人気のある画像ホスティングサービスであるimgurを使用することができます。


### Hosting Models

モデルのホスティングは、それほど単純ではありません。モデルは通常、フォルダ内のファイルグループとして提供され、モデルファイルは画像などの他のファイルを相対的に参照します。したがって、モデルは同じディレクトリにある一つのフォルダとしてアップロードされなければなりません。多くの無料アセットホスティングサービスは、一度に1つのファイルしかアップロードできないようになっています。一つの解決策は、画像を個別にアップロードした後、すべての画像のパス名をCDNのパス名に変更することですが、これは面倒な作業です。CDN経由でモデルを簡単にホスティングするための、いくつかの既知のソリューションがあります。

Neocities経由でサイトを公開する場合、サイトディレクトリにファイルやディレクトリをいくつでもアップロードすることができます。

![Neocities](https://cloud.githubusercontent.com/assets/674727/25639880/713c8266-2f42-11e7-9f2a-8e552bda80fa.jpg)

*Neocities Asset Uploader*

[jsdelivr]: https://www.jsdelivr.com/?docs=gh

あるいは、GitHubのリポジトリにアセットをアップロードし、GitHubを利用してモデルファイルを提供することも可能です。

1. GitHub リポジトリにアクセスします。
2. **Upload files** をクリックします。
3. アセットをアップロードして、アップロードが完了するのを待ちます。
4. 最下部に簡単なメッセージを入力し、**Commit changes**をクリックします。
5. 処理が完了するのを待ちます。
6. 完了したら、主アセットファイルをクリックします。
7. **Raw**をクリックします。
8. GitHubにホストされたアセットのURLができました。あとは、[JSDelivr CDN][jsdelivr] 経由でアセットのホスティングと参照が可能です。

以下は、ワークフローの動画です。

<div style="position:relative;height:0;padding-bottom:56.25%"><iframe src="https://www.youtube.com/embed/_D_C_oSKp9Y?ecver=2" width="640" height="360" frameborder="0" style="position:absolute;width:100%;height:100%;left:0" allowfullscreen></iframe></div>

しかし、eeは他のすべてのアセットを提供するのと同じようにモデルをホストし、サーバーとCDNにそれを処理させることもできます。もし、特別な場所にホスティングしたいのであれば、S3も選択肢の一つです。

## Sharing Our Project

私たちの素晴らしいプロジェクトを公開したら、それを共有して、他の人たちが中に飛び込めるようにしたいですね。

### Creating Media

[gifpardy]: https://github.com/ngokevin/gifpardy
[obs]: https://obsproject.com/

A-FrameやVRは非常に視覚的なものなので、プロジェクトの動画やGIFを作成したいと思います。

まず、画面を録画したいと思います。OS Xの場合、内蔵のQuickTime Playerの画面録画か、[OBS Studio][obs]を使って画面を録画します。Windowsでは、[OBS Studio][obs]を利用します。OBS Studioは、Webカメラの画像を画面の上にストリーミングして合成することもサポートしており、ヘッドセットを使用している人を現実に見せるのに便利です（複合現実感でも）。

次に、ビデオをトリミングしたいと思うことがあります。OS Xでは、QuickTime Playerのトリムツール（`<cmd> + t`）を使うことができます。

一つのコマンドでGIFに変換するには、[gifpardy][gifpardy]を使ってください。gifpardy`はffmpegとgifsicleを裏で使っています．

```
gifpardy in.mp4
gifpardy in.mp4 out.gif
gifpardy -r 320x240 --delay 8 in.mp4
```

[brewery]: https://itunes.apple.com/us/app/gif-brewery-by-gfycat-capture-make-video-gifs/id1081413713?mt=12

また、GIFをトリミング、リサイズ、クロップし、エクスポート前にプレビューするためのUIを持つ[GIF Brewery][brewery]を使用することも可能です。または、[LICECap](https://licecap.en.softonic.com/)を使って直接GIFにキャプチャーすることもできます。

### Sharing Media

[blog]: https://aframe.io/blog/
[reddit-webvr]: https://www.reddit.com/r/webvr
[slack-webvr]: https://webvr-slack.herokuapp.com/

A-Frameで何かを作ったら、ぜひシェアしてください。プロジェクトをシェアしていただくと、[*A-Frameの一週間*](https://aframe.io/blog/)で紹介し、コミュニティの皆様に見ていただくことができます。素晴らしいチャンネルは以下の通りです。

- [Twitter](https://twitter.com) - Mention `@aframevr` or include the `#webvr` hashtag.
- [`#projects` channel on Slack](http://aframevr.slackarchive.io/projects/)
- [WebVR Slack channel][slack-webvr].
- [/r/WebVR subreddit][reddit-webvr].
- Write a case study and tell us to feature on the [A-Frame Blog][blog].

## Embedding

A-Frameのシーンを2DのWebページのレイアウトに埋め込みたい場合、[embedded component](../components/embedded.md) を使うと、フルスクリーンスタイルを削除して、CSSでキャンバスにスタイルを設定できるようになります。

なお、1つのページには一度に1つのシーンのみ埋め込むことができます。もし複数のシーンが必要な場合は、[`<iframe>`s](https://developer.mozilla.org/docs/Web/HTML/Element/iframe)を使用します。

