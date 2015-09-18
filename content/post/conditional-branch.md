+++
date = "2015-09-18T15:18:39+09:00"
draft = false
title = "条件分岐"
author = "hondata"
categories = ["Go言語の基本"]
+++

# Go言語における条件分岐構文

Go言語では、ifとswitchの2種類の条件分岐構文を使用することができます。

## if文

if文の仕様は既存の言語とほぼ同じです。ただし、条件式を囲む丸括弧は不要です。

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    hour := time.Now().Hour()
    if hour >= 6 && hour < 12 {
        fmt.Println("朝です。")
    } else if hour < 19 {
        fmt.Println("昼です。")
    } else {
        fmt.Println("夜です。")
    }
}
```

なお、セミコロンで区切ることで条件判定の前処理を書くことが出来ます。
変数のスコープを限定したい時などに便利です。

```go
if hour := time.Now().Hour(); hour >= 6 && hour < 12 {
    fmt.Println("朝です。")
} else if hour < 19 {
    fmt.Println("昼です。")
} else {
    fmt.Println("夜です。")
}
```

## switch文

Go言語のswitch文は、一般的な言語と比べて高機能です。

### 変数の値で分岐する

当然ながら、一般的な言語と同様の使い方をすることが可能です。
なお、switch文で文字列を扱えない言語もありますが、Go言語では特に制約はありません。

```go
dayOfWeek := "月"
switch dayOfWeek {
case "土":
    fmt.Println("大概は休みです。");
case "日":
    fmt.Println("ほぼ間違いなく休みです。")
default:
    fmt.Println("仕事です・・・。")
}
```

breakが省略されていることに注目してください。
Go言語ではswitchの各caseの最後に何も書かれていない場合、自動的にbreakが補完されます。

逆に、意図的に次のケースへ処理を続けたい場合は明示的にフォールスルーをします。

```go
dayOfWeek := "月"
switch dayOfWeek {
case "土":
    fallthrough
case "日":
    fmt.Println("休みです。")
default:
    fmt.Println("仕事です・・・。")
}
```

カンマを使って複数の条件をまとめることもできます。
上記の例ならば、こちらの方がすっきりとコードを書くことができます。

```go
dayOfWeek := "月"
switch dayOfWeek {
case "土", "日":
    fmt.Println("休みです。")
default:
    fmt.Println("仕事です・・・。")
}
```

### 条件文で分岐する

switch文のもう一つの使い方として、if文のように条件文での分岐に使用することができます。

```go
hour := time.Now().Hour()
switch {
case hour >= 6 && hour < 12:
    fmt.Println("朝です。")
case  hour < 19:
    fmt.Println("昼です。")
default:
    fmt.Println("夜です。")
}
```

if文でも全く同じことが書けますが、各分岐の処理が短い場合はこちらの書き方の方がすっきりします。
