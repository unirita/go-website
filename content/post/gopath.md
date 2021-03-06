+++
date = "2015-10-02T11:45:06+09:00"
draft = false
title = "GOPATHについて"
author = "hondata"
categories = ["中級者向け"]
+++

# GOPATHとは

Go言語では、環境変数GOPATHに保存されたパス（以降、単にGOPATHと呼びます）を開発時の作業ディレクトリとして扱います。
GOPATHを利用することで、外部ライブラリの導入やビルド作業を非常に簡単に行うことができます。

## GOPATHの構成

GOPATH直下には、3つのディレクトリを配置します。

<pre class="output">
$GOPATH/
┣bin/
┣pkg/
┗src/
</pre>

<dl>
  <dt>bin</dt>
  <dd>実行ファイルが格納されるディレクトリ（go installコマンドで自動生成されます）</dd>

  <dt>pkg</dt>
  <dd>ビルドしたパッケージオブジェクトが格納されるディレクトリ（go buildをはじめとする各種コマンドで自動生成されます）</dd>

  <dt>src</dt>
  <dd>パッケージごとのソースコードを配置するディレクトリ</dd>
</dl>

srcディレクトリは最初に自分で用意する必要がありますが、他のディレクトリはコマンドで自動生成されます。

## GOPATHの複数指定

環境変数PATHと同じ書式で、GOPATHも複数指定することができます。
つまりLinuxならコロン、Windowsならセミコロン区切りで複数のパスを指定します。

GOPATHを複数指定した場合、import文によりロードするパッケージの探索は先に指定されたGOPATHから順に行われます。
