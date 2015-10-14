+++
date = "2015-10-08T15:05:05+09:00"
draft = false
title = "テストカバレッジの測定"
author = "hondata"
categories = ["Goのテスト"]
+++

# コンソール上でカバレッジを見る

Go言語では、テストコードのC0カバレッジ（行カバレッジ）を簡単に測定することができます。
カバレッジの測定をするには、go testコマンドに-coverオプションを追加します。

<pre class="output">
$ go test -cover cart
ok      cart    0.106s  coverage: 62.5% of statements
</pre>

## プロファイルデータの出力

カバレッジのプロファイルデータを出力することで、より細かい情報を見ることが出来るようになります。
プロファイルデータを出力するには、-coverprofileオプションを使用します。

<pre class="output">
$ go test -coverprofile=cover.out cart
ok      cart    0.104s  coverage: 62.5% of statements
</pre>

コンソール出力は-coverオプション使用時と同じですが、
カレントディレクトリにプロファイルデータがcover.outとして出力されるのが判ると思います。

## 関数別にカバレッジを見る

プロファイルデータを用いて、関数別のカバレッジを見ることが出来ます。
そのためにはgo tool coverコマンドのfuncオプションを使用します。

<pre class="output">
$ go tool cover -func=cover.out
cart/cart.go:7:         New             100.0%
cart/cart.go:13:        Add             100.0%
cart/cart.go:17:        Get             0.0%
cart/cart.go:24:        GetAll          100.0%
total:                  (statements)    62.5%
</pre>

関数別にカバレッジを見ることで、カバレッジの低下を招いているのはどの関数なのか確認ができます。
上記の結果であれば、Get関数へのテストが不足していることが一目瞭然です。

# ブラウザでカバレッジを見る

プロファイルデータを用いると、ブラウザ上でグラフィカルにカバレッジを表示することができます。
そのためにはgo tool coverコマンドのhtmlオプションを使用します。

<pre class="output">
$ go tool cover -html=cover.out
</pre>

上記のコマンドを発行すると自動的に標準のブラウザが起動し、カバレッジ情報が表示されます。

![ブラウザ上でのカバレッジ表示](/gostudy/images/coverage_html.png)
