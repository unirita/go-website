+++
date = "2015-09-24T11:17:02+09:00"
draft = true
title = "パッケージ"
author = "hondata"
categories = ["中級者向け"]
+++

# パッケージについて

Go言語では、名前空間を分けるための仕組みとして**パッケージ**を使用します。

## パッケージの宣言

```go
package somepkg

var SomeVar int
var someVar2 int

func SomeFunc() {
    SomeVar = 10
    someVar2 = 5
}

func someFunc2() {
    SomeFunc()
}
```

上記のコードでは、1行目でパッケージ名`somepkg`を宣言しています。
これにより、変数`SomeVar`・`someVar2`や関数`SomeFunc`・`someFunc2`は`somepkg`パッケージのメンバとなります。

同パッケージ内のメンバは、無制限に参照することができます。

## 他パッケージメンバの参照

```go
package otherpkg

func OtherFunc() {
    SomeFunc()  // エラー
    SomeVar = 5 // これもエラー
}
```

上記のコードでは、`somepkg`とは別のパッケージ`otherpkg`を宣言しています。
otherpkgからsomepkgのメンバへアクセスしようとしていますが、これはエラーになります。

他のパッケージのメンバにアクセスするには、import文を使用します。
import文によって読み込んだパッケージのメンバへのアクセスは、`パッケージ名.メンバ名`という形で行います。

```go
package otherpkg

import "somepkg"

func OtherFunc() {
    somepkg.SomeFunc()
    somepkg.SomeVar = 5
}
```

## パッケージ別名

```go
package otherpkg

import some "somepkg"

func OtherFunc() {
    some.SomeFunc()
    some.SomeVar = 5
}
```

パッケージのimport時に、パッケージに別名を付けることができます。
例えば上記のコードではパッケージ`somepkg`に`some`という別名を付けて使用しています。

パッケージ別名は長いパッケージ名を扱う場合や、同名の別パッケージを複数使用する場合に役立ちます。

なお、別名として`.`を指定したパッケージは、メンバへのアクセス時にパッケージ名の指定を省略することができます。

```go
package otherpkg

import . "somepkg"

func OtherFunc() {
    SomeFunc()
    SomeVar = 5
}
```

折角分けた名前空間を混ぜてしまうことになるので原則的に推奨されませんが、
別パッケージに分けざるを得なかったテストコードから製品コードを参照する場合など、ごく稀に活用できる場面があります。

## メンバの可視性

パッケージのメンバのうち、名前の頭文字が英字大文字で始まるものはimport文を使用することで他パッケージからの参照が可能です。
このようなメンバを**パブリックメンバ**と呼びます。

```go
package otherpkg

import "somepkg"

func OtherFunc() {
    somepkg.SomeFunc()
    somepkg.SomeVar = 5
}
```

一方、名前の頭文字が小文字のメンバはimport文を使用しても他パッケージから参照することができません。
このようなメンバを**プライベートメンバ**と呼びます。

```go
package otherpkg

import "somepkg"

func OtherFunc() {
    somepkg.someFunc2()   // エラー
    somepkg.someVar2 = 5  // エラー
}
```

## import文の正確な挙動について

import文は、正確には以下の様な動作をします。

> GOPATH（別ページで説明）のsrcディレクトリ直下をベースとして、srcディレクトリ内のディレクトリを相対パス指定する。
> import文は指定されたディレクトリ直下のコードのパッケージ名を読み込む。

つまり、import文に指定した文字列と、読み込まれるパッケージ名が一致しないケースがあります。

<pre>
（GOPATH）/
┗src/
　┣test/
　┃┣sub/
　┃┃┗sub.go（パッケージ名：sub）
　┃┣sub2/
　┃┃┗sub2.go（パッケージ名：other2）
　┃┗test.go（パッケージ名：test）
　┗test2/
　　┗test.go（パッケージ名：other）
</pre>

例えば上記のようなファイル構成の場合、import文と参照時のパッケージ名は以下の様になります。

```go
import "test"      // パッケージ名: test
import "test2"     // パッケージ名: other
import "test/sub"  // パッケージ名: sub
import "test/sub2" // パッケージ名: other2
```

## mainパッケージについて

パッケージ名が`main`のパッケージは扱いが特殊で、main関数を定義することでエントリポイント（プログラム実行時の処理開始位置）として使用できます。
逆に、mainパッケージ以外はエントリポイントとして扱うことができないため、go runコマンドでの実行やgo build/go installコマンドによる実行ファイルの生成ができません。

実は「Go言語入門」にて扱ったコードはすべてmainパッケージだったため、実行可能だった訳です。

```go
package main

import "fmt"

func main() {
  fmt.Println("Go言語はじめました！")
}
```

# パッケージに関する禁則事項

## 循環参照

二つのパッケージがお互いにもう片方をimportをするとコンパイルエラーになります。
例えば、以下の2つのパッケージは循環参照によりエラーとなります。

```go
package test1

import "fmt"
import "test2"

var SomeVar = "test1"

func SomeFunc() {
    fmt.Println(test2.SomeVar2)
}
```

```go
package test2

import "fmt"
import "test1"

var SomeVar2 = "test2"

func SomeFunc2() {
    fmt.Println(test1.SomeVar)
}
```

## 複数パッケージの同フォルダへの配置

1つのフォルダ直下に、互いに異なるパッケージが宣言された複数のコードを置くことはできません。つまり、以下のような構造はエラーになります。

<pre>
（GOPATH）/
┗src/
　┣test/
　　┣test.go（パッケージ名：test）
　　┗test2.go（パッケージ名：test2）
</pre>

逆に、パッケージが同じソースコードであれば同ディレクトリにいくつでも配置が可能です。

### 例外

実は、同フォルダに複数パッケージを配置できる例外的なケースが一つだけあります。

<pre>
（GOPATH）/
┗src/
　┣somepkg/
　　┣some.go（パッケージ名：somepkg）
　　┗some_test.go（パッケージ名：somepkg_test）
</pre>

ファイル名の（拡張子を除いた）末尾が`_test`で終わるコードはテストコードとして扱われる（詳しくは別章で説明します）のですが、
テストコードについては`テスト対象のパッケージ名_test`というパッケージ名を使用することが許されています。

殆どの場合テストコードはテスト対象のパッケージに含めるため使うことは稀ですが、
先述の循環参照を回避するためにこの仕様を利用するケースがあります。
