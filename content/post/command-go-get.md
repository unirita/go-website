+++
date = "2015-11-09T15:14:35+09:00"
draft = false
title = "go getコマンド"
author = "hondata"
categories = "外部ライブラリ"
+++

# go getコマンドについて

 外部ライブラリを導入するには、go getコマンドを利用すると便利です。

<pre class="output">
go get パッケージ名
</pre>

go getコマンドを発行すると、以下の処理が自動的に行われます。

* 指定したパッケージのGitリモートリポジトリを$GOPATH/srcへダウンロード
* 依存パッケージのGitリモートリポジトリを$GOPATH/srcへダウンロード
* ソースコードのビルド（go installコマンド相当）

例えば、以下のコマンドを発行するとします。

<pre class="output">
go get github.com/unirita/cuto/master
</pre>

すると、以下のリポジトリが自動的にダウンロードされ、$GOPATH/src以下に配置されます。

<pre class="output">
（指定パッケージのリポジトリ）
https://github.com/unirita/cuto
（以下、依存パッケージのリポジトリ）
https://github.com/BurntSushi/toml
https://github.com/cihub/seelog
https://github.com/coopernurse/gorp
https://github.com/mattn/go-sqlite3
</pre>

そして、$GOPATH/bin以下に実行ファイルmasterが生成されます。

## オプション

go getコマンドで使用できる主なオプションには、以下の様なものがあります。

|オプション|説明|
|---|---|
|-u|常にリポジトリの最新のコミットをダウンロードします。<br>このオプションを指定しない場合はローカルに存在しないリポジトリのみダウンロードが行われます。|
|-t|パッケージのダウンロード後、ビルド前にユニットテストの実行を行います。テストに失敗した場合はビルドを行わずに終了します。|
|-d|ダウンロードのみを行い、ビルドを行いません。|

その他、細かなコマンド仕様は[公式ドキュメント](https://golang.org/cmd/go/#hdr-Download_and_install_packages_and_dependencies)に記載されています。
