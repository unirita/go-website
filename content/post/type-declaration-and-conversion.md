+++
date = "2015-09-17T14:52:03+09:00"
draft = true
title = "型の宣言と変換について"
author = "hondata"
categories = ["Go言語の基本"]
+++

# 型の宣言

Go言語では、typeキーワードを使用することで新たな型を宣言することができます。

```go
package main

import "fmt"

type Score int

func main() {
    var myScore Score = 100
    fmt.Printf("私の点数は%d点です。\n", myScore)
}
```

一見何に使うのか判らないかもしれない型宣言ですが、以下の様な使い方ができます。

## 可読性の向上

型宣言を使うことで、コードが読みやすくなることがあります。
例えば数値型や文字列型は名前が短いですが、構造体型や関数型は型名が非常に長く複雑になる傾向にあります。

```go
package main

import "fmt"

func main() {
    var readFunc func(struct{name string; meaning string}) string
    var dict struct{name string; meaning string}
    readFunc = readOut
    dict.name = "コーヒー"
    dict.meaning = "コーヒー豆から作られる黒色の飲み物"
    fmt.Println(readFunc(dict))
}

func readOut(s struct{name string; meaning string}) string {
    return fmt.Sprintf("「%s」 は 「%s」 という意味です", s.name, s.meaning)
}
```

型宣言を使用することで、よりわかりやすいコードに直すことができます。

```go
package main

import "fmt"

type Dictionary struct {
    name string
    meaning string
}

type ReadFunc func(Dictionary) string

func main() {
    var readFunc ReadFunc
    var dict Dictionary
    readFunc = readOut
    dict.name = "コーヒー"
    dict.meaning = "コーヒー豆から作られる黒色の飲み物"
    fmt.Println(readFunc(dict))
}

func readOut(d Dictionary) string {
    return fmt.Sprintf("「%s」 は 「%s」 という意味です", d.name, d.meaning)
}
```

## 関数のレシーバ型としての使用

型宣言によって新たに宣言した型に対して、関数を関連付けることができます。
（オブジェクト指向型言語で言う「メソッド」と似ています。）

下記の例では、自身の値を表示する関数ShowをScore型に関連付けています。

```go
type Score int
func (s Score) Show() { fmt.Printf("点数は%d点です\n", s) }
func main() {
    var myScore Score = 100
    myScore.Show()
}
```

この時、Score型の変数myScoreを関数Showの**レシーバ**と呼びます。特に構造体を扱うようになると、この書き方は頻出します。

なお、int型をはじめとする基本型を関数のレシーバ型に指定することはできません。

# 型の変換

型変換を行うには変数を丸括弧で囲み、手前に型名を書きます

```go
package main

import "fmt"

type Score int

func main() {
    var myScore Score = 100
    showInt(int(myScore))
}

func showInt(i int) {
    fmt.Printf("value: %d\n", i)
}
```

上記の例では、int型の引数を取るshowInt関数へScore型の変数myScoreを渡すため、
関数呼び出しのタイミングでmyScoreをint型に変換しています。
