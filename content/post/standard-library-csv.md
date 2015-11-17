+++
date = "2015-11-17T14:46:44+09:00"
draft = false
title = "CSVのパース/生成"
author = "hondata"
categories = ["便利な標準ライブラリ"]
+++

# encoding/csvパッケージ

GoでCSV形式を扱うには、encoding/csvパッケージを使用します。

## パース処理

CSV文字列をパースして文字列スライスを得るには、Reader構造体を使用します。
Reader構造体のRead関数を使用することで、データを1レコードずつ取得することができます。

```go
package main

import (
    "encoding/csv"
    "fmt"
    "io"
    "strings"
)

func main() {
    lines := []string{
        "りんご,Apple,バラ科",
        "みかん,Orange,ミカン科",
        "すいか,Watermelon,ウリ科",
    }
    csvStr := strings.Join(lines, "\n")

    r := csv.NewReader(strings.NewReader(csvStr))
    for {
        record, err := r.Read()
        if err == io.EOF {
            break
        }
        if err != nil {
            // 読み込みエラー発生
            fmt.Println("Read error: ", err)
            break
        }

        fmt.Printf("日本語名[%s] 英語名[%s] 科分類[%s]\n", record[0], record[1], record[2])
    }
}
```

<pre class="output">
日本語名[りんご] 英語名[Apple] 科分類[バラ科]
日本語名[みかん] 英語名[Orange] 科分類[ミカン科]
日本語名[すいか] 英語名[Watermelon] 科分類[ウリ科]
</pre>

データ量が少ない場合は、全レコードを一度にパースするReadAll関数を使用するとより簡単です。

```go
package main

import (
    "encoding/csv"
    "fmt"
    "strings"
)

func main() {
    lines := []string{
        "りんご,Apple,バラ科",
        "みかん,Orange,ミカン科",
        "すいか,Watermelon,ウリ科",
    }
    csvStr := strings.Join(lines, "\n")

    r := csv.NewReader(strings.NewReader(csvStr))
    records, err := r.ReadAll()
    if err != nil {
        // 読み込みエラー発生
        fmt.Println("Read error: ", err)
        return
    }
    for _, record := range records {
        fmt.Printf("日本語名[%s] 英語名[%s] 科分類[%s]\n", record[0], record[1], record[2])
    }
}
```

## 生成処理

文字列スライスからCSV文字列を得るには、Writer構造体を使用します。
Writer構造体のWrite関数を使用することで、データを1レコードずつCSV文字列として出力することができます。

```go
package main

import (
    "bytes"
    "encoding/csv"
    "fmt"
)

func main() {
    records := [][]string{
        []string{"りんご", "Apple", "バラ科"},
        []string{"みかん", "Orange", "ミカン科"},
        []string{"すいか", "Watermelon", "ウリ科"},
    }

    buf := new(bytes.Buffer)
    w := csv.NewWriter(buf)
    for _, record := range records {
        if err := w.Write(record); err != nil {
            // 書き込みエラー発生
            fmt.Println("Write error: ", err)
            return
        }
        w.Flush() // Flush関数を呼び出したタイミングで実際の出力が行われる
    }
    fmt.Println(buf.String())
}
```

<pre class="output">
りんご,Apple,バラ科
みかん,Orange,ミカン科
すいか,Watermelon,ウリ科
</pre>

データ量が少ない場合は、全レコードを一度に出力するWriteAll関数を使用するとより簡単です。

```go
package main

import (
    "bytes"
    "encoding/csv"
    "fmt"
)

func main() {
    records := [][]string{
        []string{"りんご", "Apple", "バラ科"},
        []string{"みかん", "Orange", "ミカン科"},
        []string{"すいか", "Watermelon", "ウリ科"},
    }

    buf := new(bytes.Buffer)
    w := csv.NewWriter(buf)
    if err := w.WriteAll(records); err != nil {
        // 書き込みエラー発生
        fmt.Println("Write error: ", err)
        return
    }
    // WriteAll関数は内部でFlushを行っているため、Flush関数の呼び出しは不要
    fmt.Println(buf.String())
}
```
