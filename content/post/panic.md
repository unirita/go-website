+++
date = "2015-10-02T18:44:32+09:00"
draft = false
title = "パニック"
author = "shanxia"
categories = ["エラーハンドリング"]
+++

# パニックとは
関数内でエラーが発生したとき、通常ならば呼び出し元がエラーハンドリングを行いますが、致命的なエラーのため、その必要がない場合があります。この場合は、「panic」という組み込み関数を呼び出して、「パニック」状態を発生させます。
ある関数内で「panic」を呼び出すと、その関数の実行は中断され、呼び出し元に復帰します。呼び出し元に復帰後も、順次関数の呼び出し元をさかのぼり、最終的にプログラム自体が終了します。
パニック状態でプログラムが終了すると、「panic」を呼び出した箇所とともに、引数として渡したパラメータの値が標準エラー出力に出力されます。
「panic」を使った例を以下に示します。

```go
package main

func func1() {
	panic("Occured panic!")
}

func main() {
	func1()
}
```
これを実行すると、次の様に出力されます。
>panic: Occured panic!

>goroutine 1 [running]:
main.func1()
	/home/src/samples/main.go:4 +0x65
main.main()
	/home/src/samples/main.go:8 +0x14
exit status 2

## panic実行前に指定したdefer関数
「panic」を呼び出すと、その関数は中断されますが、呼び出し前にdeferで指定した関数は実行されます。
「panic」指定時のdeferの例を以下に示します。

```go
package main

import "fmt"

func func1() {
	defer func() {
		fmt.Println("defer 2")
	}()
	panic("Occured panic!")
}

func main() {
	defer func() {
		fmt.Println("defer 1")
	}()
	func1()
}
```

これを実行数と、次の様に表示されます。
>defer 2
defer 1
panic: Occured panic!

>goroutine 1 [running]:
main.func1()
	/Users/katsuji/Documents/workspace/gocuto/src/samples/main.go:9 +0x86
main.main()
	/Users/katsuji/Documents/workspace/gocuto/src/samples/main.go:16 +0x35
exit status 2

## ランタイムパニック
インデックスの超過など、Go言語のランタイムがパニックを起こすことがあります。これを「ランタイムパニック」と呼び、このときには「runtime.Errorインタフェース」が渡されます。

```go
// runtime.Errorインタフェースの定義内容
package runtime
type Error interface {
	error
	
	RuntimeError()
}
```

ランタイムパニックは、次の様にして確認することができます。

```go
package main

func main() {
	arr := [...]int{1, 2, 3}

	index := 3

	arr[index] = 0
}
```

実行すると、次の結果が表示されます。

>panic: runtime error: index out of range
goroutine 1 [running]:
main.main()
	/home/src/samples/main.go:8 +0x63
exit status 2