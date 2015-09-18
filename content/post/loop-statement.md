+++
date = "2015-09-18T16:08:22+09:00"
draft = true
title = "繰り返し"
author = "hondata"
categories = ["Go言語の基本"]
+++

# Go言語における繰り返し構文

Go言語で繰り返し処理を書く場合はfor文を使用します。他の多くの言語に存在するwhile文はありません。
代わりに、Go言語のfor文はかなり高機能になっています。

## 一般的なfor

```go
for i := 1; i < 100; i++ {
    if i / 2 != 0 {
        fmt.Println(i)
    }
}
```

セミコロンで区切った3つの文を使用することで、初期化式・反復条件・反復時の処理を持つ一般的なfor文を書くことができます。

## 無限ループ

```go
i := 0
for {
    i++
    if i >= 100 {
        break
    } else if i / 2 == 0 {
        continue
    }
    fmt.Println(i)
}
```

forキーワードの後ろに何も書かずにブロックを開始すると、無限ループになります。
他の言語で言うwhile(true)やfor(;;)と同じ動きをすると考えてください。

breakやcontinueでループからの脱出や次のイテレーションの開始が出来るのも、一般的な言語と同様です。

## コレクション内の要素のイテレーション

```go
dayOfWeeks := [...]string{"月", "火", "水", "木", "金", "土", "日"}
for arrayIndex, dayOfWeek := range dayOfWeeks {
    fmt.Printf("%d番目の曜日は%s曜日です。\n", arrayIndex + 1, dayOfWeek)
}
```

rangeキーワードを使用することで、配列をはじめとするコレクション内の要素を順番に取り出して処理をすることができます。
この書き方を用いて、他の言語におけるforeach文相当の働きをすることができます。
