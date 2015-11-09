+++
date = "2015-10-09T10:31:47+09:00"
draft = true
title = "sync"
author = "shanxia"
categories = ["並列処理"]
+++

# Go言語の同期処理
ゴルーチン間の同期処理は、チャネルを利用して簡単に実現できますが、Javaのsynchronizedの様に、手軽に排他制御を行いたい場合があります。そんな場合は、Goの標準パッケージで提供されている、syncパッケージを使用します。
Goのsyncパッケージには、他にも排他制御を行う便利な機能がそろっていますが、ここでは代表的な2つの機能を紹介します。

## sync.WaitGroup
sync.WaitGroupを使用すると、複数のゴルーチンを簡単に待機することができます。
使用方法のサンプルコードを、以下に記します。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func parallel(wg *sync.WaitGroup) {

	fmt.Println("A")
	time.Sleep(1 * time.Millisecond)
	fmt.Println("B")
	time.Sleep(1 * time.Millisecond)
	fmt.Println("C")

	// 終了を通知する
	wg.Done()
}

func main() {
	// WaitGroup構造体を初期化
	wg := new(sync.WaitGroup)
	// 3つのゴルーチンを同時に実行します
	for i := 0; i < 3; i++ {
		wg.Add(1)// WaitGroupに、ゴルーチンを1つずつ追加
		go parallel(wg)
	}
	// wg.Addで追加したすべてゴルーチンが、Doneで終了通知されるまで待機
	wg.Wait()
}
```
これを実行すると、次の様な結果になります。
<pre class="output">A
A
A
B
B
B
C
C
C</pre>

実行環境によっては、順序が変わる事もあります。
実行結果から、main関数は、すべてのゴルーチンが終了するまで待機することが分かります。


## sync.Mutex
sync.Mutexは、他言語の排他制御でおなじみのミューテックスです。Go言語でも同様の機能を持っています。sync.WaitGroupのサンプルコードに、sync.Mutexを追加したコードを以下に記します。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func parallel(wg *sync.WaitGroup, mt *sync.Mutex) {
	// ミューテックスを使用してロックします。
	mt.Lock()
	// 関数終了後にアンロックします。
	defer mt.Unlock()

	fmt.Println("A")
	time.Sleep(1 * time.Millisecond)
	fmt.Println("B")
	time.Sleep(1 * time.Millisecond)
	fmt.Println("C")

	// 終了を通知する
	wg.Done()
}

func main() {
	// WaitGroup構造体を初期化
	wg := new(sync.WaitGroup)
	// Mutex構造体を初期化
	mt := new(sync.Mutex)
	// 3つのゴルーチンを同時に実行します
	for i := 0; i < 3; i++ {
		wg.Add(1) // WaitGroupに、ゴルーチンを1つずつ追加
		go parallel(wg, mt)
	}
	// wg.Addで追加したすべてゴルーチンが、Doneで終了通知されるまで待機
	wg.Wait()
}
```
実行すると、次の結果になります。

<pre class="output">A
B
C
A
B
C
A
B
C</pre>

sync.MutexのLockメソッドを実行すると、同じMutexポインタを持つ他のゴルーチンのLock()メソッドが待機状態になります。サンプルコードでは、1つのゴルーチンのparallel関数が終了する（Unlockメソッドが実行される）まで、他ゴルーチンのparallel関数のLockメソッドは制御を返しません。
そのため、ABCの順序が守られて結果に表示されます。
