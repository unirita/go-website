+++
date = "2015-10-09T13:33:49+09:00"
draft = false
title = "ソケット通信"
author = "hondata"
categories = ["通信"]
+++

# サーバの実装

Go言語でソケット通信をするには、netパッケージを使用します。

```go
package main

import (
    "fmt"
    "net"
)

func main() {
    listener, err := net.Listen("tcp", "0.0.0.0:1234")
    if err != nil {
        fmt.Printf("Listen error: %s\n", err)
        return
    }
    defer listener.Close()

    conn, err := listener.Accept()
    if err != nil {
        fmt.Printf("Accept error: %s\n", err)
        return
    }
    defer conn.Close()

    fmt.Println("クライアントからの受信メッセージ:")
    buf := make([]byte, 1024)
    for {
        n, err := conn.Read(buf)
        if n == 0 {
            break
        }
        if err != nil {
            fmt.Printf("Read error: %s\n", err)
        }
        fmt.Print(string(buf[:n]))
    }
}
```

上記のプログラムは、クライアントからのメッセージを1回だけ受け取って標準出力するサーバプログラムです。
以下、肝となる部分を解説します。

## リスナの作成

```go
listener, err := net.Listen("tcp", "0.0.0.0:1234")
```

net.Listen関数の第一引数にプロトコル名、第二引数に受信ホスト名・ポート番号を指定することで、リスナの作成ができます。
他の多くの言語と同じように、Go言語ではリスナを用いて接続の待ち受けを行います。

## 接続を待つ

```go
conn, err := listener.Accept()
```

リスナのAcceptメソッドを使用し、クライアントからの接続を待ちます。

## メッセージの受信

```go
fmt.Print(string(buf[:n]))
```

コネクションオブジェクトのReadメソッドを使用し、クライアントからの送信メッセージを取得します。
バッファサイズを超える分は分割して受信することになるため、受信サイズ（戻り値n）が0になるまでループさせています。

## リスナ・コネクションのクローズ

```go
defer listener.Close()
defer conn.Close()
```

取得したリソースは忘れずにクローズします。
それぞれオブジェクト生成が成功した直後にdeferすることによって、確実にクローズが行われます。

# クライアントの実装

```go
package main

import (
    "fmt"
    "net"
)

func main() {
    conn, err := net.Dial("tcp", "123.45.67.89:1234")
    if err != nil {
        fmt.Printf("Dial error: %s\n", err)
        return
    }
    defer conn.Close()

    sendMsg := "Test Message.\n"
    conn.Write([]byte(sendMsg))
}
```

上記のプログラムは、先述のサーバプログラムに対してメッセージを送信するクライアントプログラムです。

## サーバへの発呼

```go
conn, err := net.Dial("tcp", "123.45.67.89:1234")
```

net.Dial関数を使用することで、接続待ちをしているサーバへの発呼を行うことができます。
サーバ側と同様、生成したコネクションオブジェクトは直後にdeferを用いてCloseされるようにしています。

## メッセージの送信

```go
conn.Write([]byte(sendMsg))
```

サーバ側でのメッセージ受信がReadメソッドで行われたのに対し、メッセージ送信はWriteメソッドで行います。
