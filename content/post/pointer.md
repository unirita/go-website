+++
date = "2015-09-18T15:51:35+09:00"
draft = false
title = "Goのポインタ"
author = "shanxia"
categories = ["Go言語の基本"]
+++

# Go言語のポインタ
C言語でおなじみのポインタですが、Go言語にもポインタがあります。
宣言する方法もC言語と同じで、たとえばint型変数のポインタは、「*int」と記述します。変数のアドレスを取得するときも、C言語と同様に「&」をつけ、アドレスから変数の中身へアクセスする時は「*」を使います。

ポインタの使用例は次の通り。

```go
package main

import (
	"fmt"
)

func main() {
	// int型のポインタ変数
	var pointer *int
	// int型変数
	var n int = 100

	// &（アドレス演算子）を使って、nのアドレスを代入
	pointer = &n

	fmt.Println("nのアドレス：", &n)
	fmt.Println("pointerの値：", pointer)

	fmt.Println("nの値：", n)
	// *(間接参照演算子）を利用して、ポインタの中身を取得
	fmt.Println("pointerの中身：", *pointer)
}
```
実行結果は次の通り。アドレスは環境によって異なります。
> nのアドレス：0x8201e0320
pointerの値： 0x8201e0320
nの値： 100
pointerの中身： 100

なお、ポインタの元祖であるC言語では、ポインタの値を演算して、別アドレスにすることができますが、Go言語ではできません。

## ポインタ渡しと値渡し
ある変数を関数の引数として渡す場合、値のコピーが渡されます。そのため呼び出された関数内で変数の値を変更しても、元の値には影響がありません。これを「値渡し」と呼びます。
一方、ポインタ変数を関数に渡した場合は、ポインタが指し示す値が、呼び出し元も、呼び出された方も同じものであるため、呼び出された関数内で元の値を変更することができます。これを「ポインタ渡し（または参照渡し）」と呼びます。

ポインタ渡しの例は、次の通り。

```go
package main

import (
	"fmt"
)

func main() {
	a, b := 10, 10

	// aはそのまま、bはアドレス演算子をつけて呼び出す
	called(a, &b)

	fmt.Println("値渡し：", a)
	fmt.Println("ポインタ渡し：", b)
}

func called(a int, b *int) {
	// 変数をそのまま変更
	a = a + 1
	// 変数の中身を変更
	*b = *b + 1
}
```
実行結果は次の通り。
>値渡し：10
ポインタ渡し：11

## 「new」を利用したメモリ確保
ポインタの利用は、変数のアドレスを格納するだけではなく、新しくメモリを確保したいときにも使用します。Go言語では、メモリの動的割り当てに「new」を使用して、利用したい型を指定します。
Go言語には、Javaの様なガベージコレクタが組み込まれているため、確保したメモリをプログラマーが解放する必要はありません。

「new」の使用例は次の通り。

```go
// int型のメモリ割り当て
var p *int = new(int)

// 構造体myStruct型のメモリ割り当て
type myStruct struct {
	a int
	b int
}
var my *myStruct = new(myStruct)

```
なお、「new」で割り当てたメモリは、変数宣言と同様に、型ごとのゼロ値で初期化されます。