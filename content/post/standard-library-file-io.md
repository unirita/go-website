+++
date = "2015-11-11T15:49:37+09:00"
draft = false
title = "ファイル入出力"
author = "hondata"
categories = ["標準ライブラリの活用"]
+++

# osパッケージ

Goでファイルへの入出力を行うには、osパッケージを利用するのが基本です。

## ファイルからの入力

ファイルからの入力を行うには、Open関数でファイルをオープンしてからRead関数でbyte配列へ読み込みを行います。
以下のコードはファイルの内容を標準出力するプログラムの一例です。

```go
package main

import (
    "fmt"
    "os"
)

const BUFSIZE = 1024 // 読み込みバッファのサイズ

func main() {
    file, err := os.Open(`/path/to/file`)
    if err != nil {
        // Openエラー処理
    }
    defer file.Close()

    buf := make([]byte, BUFSIZE)
    for {
        n, err := file.Read(buf)
        if n == 0 {
            break
        }
        if err != nil {
            // Readエラー処理
            break
        }

        fmt.Print(string(buf[:n]))
    }
}
```

## ファイルへの出力

ファイルへの出力を行うには、Create関数でファイルを生成・オープンし、Write関数で書き込みを行います。
以下のコードはファイルへ文字列を出力するプログラムの一例です。

```go
package main

import "os"

func main() {
    file, err := os.Create(`/path/to/file`)
    if err != nil {
        // Openエラー処理
    }
    defer file.Close()

    output := "testmessage"
    file.Write(([]byte)(output))
}
```

# ioパッケージ

ioパッケージには、入出力に使用する各種インタフェース型が定義されています。
代表的なものとしては、入力に使用するio.Readerインタフェースや出力に使用するio.Writerインタフェースがあります。

```go
type Reader interface {
        Read(p []byte) (n int, err error)
}
type Writer interface {
        Write(p []byte) (n int, err error)
}
```

os.Open関数やos.Create関数が返すos.File構造体はRead関数とWrite関数の両方を持っているため、
上記のインタフェースの両方を実装していることになります。

# io/ioutilパッケージ

io/ioutilパッケージには入出力に利用するユーティリティ関数が定義されています。

例えばioutil.ReadFile関数はファイルからすべてのデータを入力する関数です。
これを用いるとosパッケージの項に記載したファイルの内容を標準出力するプログラムは、以下の様に簡単に書くことができます。

```go
package main

import (
    "fmt"
    "io/ioutil"
)

func main() {
    data, err := ioutil.ReadFile(`/path/to/file`)
    if err != nil {
        // エラー処理
    }
    fmt.Print(string(data))
}
```

サイズが大きいファイルを扱うのには適しませんが、効果的に使用すればコードを簡略化することが可能です。

# bufioパッケージ

bufioパッケージはバッファ付きIOを扱うためのパッケージです。
バッファ付きIOを使用することでパフォーマンスが向上するケースがあるほか、行単位での読み込みなどを簡単に実現することができます。

後者の例として、bufio.Scanner構造体を利用したコードの例を示します。
この構造体は、一定のルールに基づいて入力を分割取得するための構造体で、デフォルトでは行ごとの取得を行うことができます。

```go
package main

import (
    "bufio"
    "fmt"
    "os"
)

func main() {
    file, err := os.Open(`/path/to/file`)
    if err != nil {
        // Openエラー処理
    }
    defer file.Close()

    sc := bufio.NewScanner(file)
    for i := 1; sc.Scan(); i++ {
        if err := sc.Err(); err != nil {
            // エラー処理
            break
        }
        fmt.Printf("%4d行目: %s\n", i, sc.Text())
    }
}
```
