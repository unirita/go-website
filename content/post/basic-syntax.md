+++
date = "2015-09-16T15:10:26+09:00"
draft = false
title = "基本的な文法"
author = "hondata"
categories = ["Go言語の基本"]
+++

# 概要

Go言語は手続き型言語です。C言語と同様に（正確にはmainパッケージ内の）main関数をエントリポイントとして、上から下へ処理が進んでいきます。

# Go言語の特徴的な文法

```go
package main

import "fmt"

func main() {
    var num int
    var pow int
    var result = 1

    num = 2
    pow = 4
    for i := 0; i < pow; i++ {
       result *= num
    }

    fmt.Printf("%dの%d乗は%dです。\n", num, pow, result)
}
```

上記はべき乗の計算をするコードです。このコードを例に、Go言語の特徴的な文法について説明します。

## 文

他の多くの言語と同じく、Go言語でも文の終わりはセミコロンで終端します。ただし、殆どのセミコロンは自動補完が働くため、書く必要がありません。

セミコロンが必要な例として、以下の様に1行に2つ以上の文を書く場合は行末以外のセミコロンを明示的に書く必要があります。

```go
var a = 2; var b = 1
```

## 変数宣言

```go
var num int
```

C言語をはじめとする多くの言語では型名の後ろに変数名を書きますが、Go言語では型名を変数名の後ろに置きます。

なお、宣言と同時に初期化を行う場合は以下の様に型を省略することができます。

```go
var result = 1
i := 0
```

## 括弧について

多くの言語ではif、for、switch文の条件部に丸括弧が必要ですが、Go言語では不要です。

```go
if a < 5 {}   // OK
if (a < 5) {} // NGではないが冗長
```

また、ブロック内が単文の場合でも波括弧が必須です。

```go
if a < 5 { fmt.Println(a) } // OK
if a < 5 fmt.Println(a)     // ERROR
```
