+++
date = "2015-10-06T13:42:38+09:00"
draft = false
title = "遅延実行"
author = "hondata"
categories = ["中級者向け"]
+++

# 遅延実行とは

Go言語では、deferキーワードを用いることで特定の処理を関数の一番最後に実行することができます。

```go
package main

import "fmt"

func main() {
    defer fmt.Println("A")
    fmt.Println("B")
}
```

上記のコード例では「B」が出力された後に「A」が出力されます。

また、deferを複数書くことgができます。その場合、最初にdeferされた行が一番最後に実行されます。

```go
package main

import "fmt"

func main() {
    defer fmt.Println("A")
    defer fmt.Println("B")
    defer fmt.Println("C")
    fmt.Println("D")
}
```

上記のコード例では「D」「C」「B」「A」の順に出力が行われます。

## 遅延実行の使いどころ

以下のコード例を見てください。

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    file, err := os.Open("hoge.txt")
    if err != nil {
        fmt.Println("File open error: ", err)
        return
    }

    buf := make([]byte, 1024)
    for {
        n, err := file.Read(buf)
        if n == 0 {
            break
        }
        if err != nil {
            fmt.Println("File read error: ", err)
            return
        }
        fmt.Print(buf[:n])
    }

    file.Close()
}
```

上記はGo言語でファイルの内容を標準出力する一般的なコードです。
このコードには1点問題があります。それはファイルのReadでエラーが発生するとファイルがCloseされない、という点です。

このような問題を解決するために、deferを使用します。

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    file, err := os.Open("hoge.txt")
    if err != nil {
        fmt.Println("File open error: ", err)
        return
    }
    defer file.Close()   // <- ここでCloseを遅延実行する

    buf := make([]byte, 1024)
    for {
        // 省略
    }

}
```

上記のようにすることで、deferが行われた以降の行のどこでreturnしても（またはpanicが発生しても）必ずfile.Closeが実行されます。
このように、deferはよくリソースの解放に使用されます。

## deferが実行されないケース

deferによる遅延実行は原則的に必ず行われますが、例外が1つだけあります。
それは、os.Exit関数を使用した場合です。

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    defer fmt.Println("A")
    fmt.Println("B")
    os.Exit(0)
}
```

os.Exitはdeferを無視してプログラムを終了されるため、上記の例では「B」は出力されますが「A」は出力されません。
os.Exitを使用する場合はこの点に注意しましょう。
