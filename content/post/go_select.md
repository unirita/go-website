+++
date = "2015-10-08T18:21:16+09:00"
draft = false
title = "select"
author = "shanxia"
categories = ["並列処理"]
+++

# 「select」文
「select」文は、複数のチャネルを待機する場合に使用します。構文は「switch」文と同じく「case」を使用します。「select」文は、「case」に指定したチャネルのうち、どれかを受信するまで待機します。

## 「select」文の使い方
「select」文の書き方は、「switch」文と同じですが、「fallthrough」は使えません。
複数の「case」に指定したチャネルを同時に受信した場合、どれが実行されるのかはランダムです。また、「default」節は、どのチャネルも受信しなかった際に呼ばれる節ですが、これを指定しない場合は受信するまで待機することができます。

「select」文のサンプルコードは以下の通りです。

```go
package main

import (
	"fmt"
	"strconv"
	"time"
)

func main() {
	fmt.Println("開始")

	ch1 := make(chan int)
	ch2 := make(chan string)
	chend := make(chan struct{}) // 終了通知用のチャネル

	// チャネルを送信するゴルーチン
	go func(chint chan<- int, chstr chan<- string, end chan<- struct{}) {

		for i := 0; i < 10; i++ {
			// 偶数回はint型チャネル、奇数回はstring型チャネルを送信する
			if i%2 == 0 {
				fmt.Println("ch1へ送信")
				chint <- i
			} else {
				fmt.Println("ch2へ送信")
				chstr <- "test" + strconv.Itoa(i)
			}
		}

		time.Sleep(1 * time.Second)
		close(end) // クローズして通知

	}(ch1, ch2, chend)

	// 受信用の無限ループ
	for {
		select {
		case val := <-ch1:
			fmt.Println("ch1から受信：", val)
		case str := <-ch2:
			fmt.Println("ch2から受信：", str)
		case <-chend:
			fmt.Println("終了")
			return
		}
	}
}
```

実行結果は、次の通りです。

<pre class="output">
ch1へ送信
ch2へ送信
ch1から受信： 0
ch2から受信： test1
ch1へ送信
ch2へ送信
ch1から受信： 2
ch2から受信： test3
ch1へ送信
ch2へ送信
ch1から受信： 4
ch2から受信： test5
ch1へ送信
ch2へ送信
ch1から受信： 6
ch2から受信： test7
ch1へ送信
ch2へ送信
ch1から受信： 8
ch2から受信： test9
終了
</pre>
