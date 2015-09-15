+++
date = "2015-09-15T16:28:22+09:00"
draft = false
title = "IDEについて"
author = "hondata"
categories = ["序章"]
+++

# Goの開発環境

Goはシンプルな言語使用とコマンドラインインタフェースを持つ言語なので、テキストエディタでも十分に開発を行うことが可能です。

実際のところvimやSublime Textといったテキストエディタを好んで使っている方は多く、Web上で検索をするとエディタをより便利に使うための設定が大量にヒットします。
最近はGo言語と同じくGoogle製のテキストエディタAtomもリリースされ、Go言語と相性の良いエディタの一つとして愛好者が増えています。

一方、テキストエディタよりIDEの方が開発しやすいという方も多いと思います。そこで、2015年現在Go言語の開発を行う上で実用的なIDEを三つ紹介します。

## LiteIDE

[GitHubページ](https://github.com/visualfc/liteide) /
[バイナリダウンロード](http://sourceforge.net/projects/liteide/files/)

LiteIDEはGo言語向けに特化したIDEとして公開されているOSSです。それだけに、Go言語を開発する上で必要な機能が必要十分に揃っています。
後述するIntelliJやEclipseを使用したことが無い、その操作感にこだわりが無いようであれば、まずはこれを試してみると良いでしょう。

## IntelliJ IDEA + go-lang-idea-plugin

[IntelliJ IDEA](https://www.jetbrains.com/idea/) /
[go-lang-idea-plugin](https://github.com/go-lang-plugin-org/go-lang-idea-plugin)

IntelliJ IDEAは主にJava開発者に人気のIDEです。これにOSSのプラグインを加えることでGo言語向けの機能を追加することができます。
昔は有償版しか無いIDEでしたが、今は無料のCommunity Editionがあります。

Go言語の開発環境として比較的人気で、記事が多く見つかるため導入には困らない印象です。

## Eclipse + GoClipse

[Eclipse](https://eclipse.org/) /
[GoClipse](https://goclipse.github.io/)

IntelliJと同じく、人気のIDEとプラグインの組み合わせです。上記はEclipseの公式サイトへのリンクですが、日本語で開発を行う場合は日本語化プロジェクトの[Pleiades](http://mergedoc.osdn.jp/)から入手するのが良いでしょう。

IntelliJと比べた使い勝手は人によって一長一短と言ったところです。Eclipseを他の言語の開発で使っているという方は使用してみてはいかがでしょうか。
