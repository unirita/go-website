+++
date = "2015-10-02T14:20:38+09:00"
draft = false
title = "go installコマンドについて"
author = "hondata"
categories = ["中級者向け"]
+++

# go installコマンド

GOPATH以下で開発を行う場合、go installコマンドを用いると実行ファイルを簡単に生成することができます。
go installコマンドは以下の様に発行します。

```bash
go install mainパッケージが格納されたディレクトリのパス
```

## コマンド発行例

例えば、以下の様なディレクトリ構成だったとします。

<pre>
src/
┗testpkg
  ┗main
</pre>

このとき、testpkgディレクトリ直下にmainパッケージが配置されている場合は、以下のコマンドで実行ファイルの生成ができます。

```bash
go install testpkg
```

同様に、testpkg/mainディレクトリ直下にmainパッケージが配置されている場合は、以下のコマンドで実行ファイルの生成ができます。

```bash
go install testpkg/main
```

## 実行ファイルの配置場所

go installコマンドによって生成された実行ファイルは、自動的に$GOPATH/binディレクトリ内に配置されます。
実行ファイル名はgo installコマンドの引数に指定したディレクトリ名が使われます。
