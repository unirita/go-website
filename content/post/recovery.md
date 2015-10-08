+++
date = "2015-10-02T19:23:41+09:00"
draft = false
title = "recovery"
author = "shanxia"
categories = ["エラーハンドリング"]
+++

# リカバリとは
「panic」を呼び出して、関数をさかのぼる途中に「recover」を呼び出すことで、パニック状態を中断させることができます。「recovery」の戻り値は、「panic」に与えた引数なので、パニック状態ではない場合はnilが返ります。これを利用して、現在パニック状態なのか、そうではないのかを判断できます。
ただし「recover」を呼び出すことができるのは、「defer」によって遅延指定された関数内だけです。これはパニック状態で呼び出される可能性がある関数は、「defer」で指定された関数のみだからです。

「recovery」を使った例を以下に示します。

```go
package main

import "fmt"

func func1(b bool) {
	defer func() {
		fmt.Println("defer start.")

		if err := recover(); err != nil {
		    // パニック中だった
			fmt.Println("recovery!")
		}
		fmt.Println("defer end.")
	}()
	if b {
		panic("Occure panic!")
	}
}

func main() {
	func1(false)
	func1(true)
}
```

これを実行すると、以下の様に表示されます。

<pre class="output">
defer start.
defer end.
defer start.
recovery!
defer end.
</pre>
