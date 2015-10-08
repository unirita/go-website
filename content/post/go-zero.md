+++
date = "2015-09-17T13:42:23+09:00"
draft = false
title = "Goのゼロ値"
author = "shanxia"
categories = ["Go言語の基本"]
+++

#Goのゼロ値
Go言語では、変数の宣言時に初期化しなかった場合、暗黙でゼロ値が設定されます。変数の型によって値は異なります。

| 型 | ゼロ値 |
|--------|--------|
|論理値型（bool）|false|
|整数型（rune、intなど）|0|
|浮動小数点型（fload、complexなど）|0.0|
|文字列型（string）|""（空文字列）|
|配列型|各要素がゼロ値の配列|
|構造体型|各フィールドがゼロ値の構造体|
|その他|nil（※）|

※ 「nil」とは、C言語の「NULL」、Java言語の「null」に相当する、値がない状態を指す値です。

ゼロ値のコード例は次の通りです。

```go
package main

import (
	"fmt"
)

func main() {
	var b bool
	var i int
	var r rune
	var f float64
	var c complex64
	var s string

	fmt.Println("bool =    ", b)
	fmt.Println("int =     ", i)
	fmt.Println("rune =    ", r)
	fmt.Println("float =   ", f)
	fmt.Println("complex = ", c)
	fmt.Println("string =  ", s)
}
```

これを実行すると、次の様に表示されます。

<pre class="output">
bool =     false
int =      0
rune =     0
float =    0
complex =  (0+0i)
string =   
</pre>
