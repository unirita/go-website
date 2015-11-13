+++
date = "2015-11-13T13:23:57+09:00"
draft = false
title = "日時処理"
author = "hondata"
catetories = ["便利な標準ライブラリ"]
+++

# timeパッケージ

Go言語で日時を扱うには、timeパッケージを使用します。


## 現在日時の取得

現在の日時を取得するには、Now関数を使用します。

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    fmt.Println(time.Now())
}
```

<pre class="output">
2015-11-13 14:01:02.8911399 +0900 JST
</pre>

Now関数を呼び出すと、Time構造体を返します。
この構造体はナノ秒単位の日時を表す構造体になっています。


## 指定日時の取得

特定の日時を表すTime構造体を取得するには、Date関数を使用します。

```go
package main

import "fmt"
import "time"

func main() {
	t := time.Date(2015, 9, 13, 12, 35, 42, 0, time.Local)
	fmt.Println(t)
}
```

<pre class="output">
2015-09-13 12:35:42 +0900 JST
</pre>

Date関数の引数は先頭から順に

1. 年
2. 月
3. 日
4. 時
5. 分
6. 秒
7. ナノ秒
8. ロケーション

の順に並んでいます。

### ロケーションについて

Date関数の第8引数にはロケーション（タイムゾーン）を表すLocation構造体を指定します。
先述のコードで使用したtime.Local変数には、システムのローカルロケーションが格納されています。
time.Local以外に、世界標準時を表すtime.UTC変数がデフォルトで利用可能です。

その他のロケーションを使用する場合は、FixedZone関数を使用して秒単位の時差とロケーション名からLocation構造体を作成します。

```go
loc := time.FixedZone("EST", -5*60*60) // アメリカ東部時間（-0500）
```

また、IANA Time Zone Databaseに準拠した名前を使用して、LoadLocation関数でロケーションを取得することも可能です。

```go
loc, err := time.LoadLocation("America/New_York")
if err != nil {
    // 指定した名前が発見できない場合のエラー処理が必要
}
```


## 文字列表現の取得

Time構造体の文字列表現は、String関数で取得することができます。
また、出力フォーマットを指定できるFormat関数を利用することもできます。

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    now := time.Now()
    fmt.Printf("フォーマット指定なし：%s\n", now.String())
    fmt.Printf("フォーマット指定あり：%s\n", now.Format("2006/01/02 Mon 15:04:05"))
}
```

<pre class="output">
フォーマット指定なし：2015-11-13 14:48:30.8232023 +0900 JST
フォーマット指定あり：2015/11/13 Fri 14:48:30
</pre>

### [豆知識]フォーマット指定について

Go言語のtimeパッケージでの日時フォーマット指定は他の言語と比べて特殊で、
「2006年1月2日15時4分5秒」という固定の日時を使用してフォーマット指定を行います。

あくまでも筆者の主観ですが、慣れるまでは違和感を感じるものの
慣れてくると特殊なフォーマット文字列を指定するよりもコードの可読性は高くなるように思います。

なお何故この日付なのかというと、アメリカで使われる日付のフォーマット順を使用すると、
01～06までが順に並ぶから、という理由であるようです。

<pre class="output">
01-02 03:04:05 PM '06
</pre>


## 日時のパラメータを取得

Time構造体には年・月・日など、各パラメータを取得するためのメンバ関数が用意されています。

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    now := time.Now()
    fmt.Printf("文字列表現：%s\n", now.String())
    fmt.Printf("年：%d\n", now.Year())
    fmt.Printf("月：%d\n", now.Month())
    fmt.Printf("日：%d\n", now.Day())
    fmt.Printf("曜日：%s\n", now.Weekday().String())
    fmt.Printf("時：%d\n", now.Hour())
    fmt.Printf("分：%d\n", now.Minute())
    fmt.Printf("秒：%d\n", now.Second())
    fmt.Printf("ナノ秒：%d\n", now.Nanosecond())
}
```

<pre class="output">
文字列表現：2015-11-13 15:17:01.9804355 +0900 JST
年：2015
月：11
日：13
曜日：Friday
時：15
分：17
秒：1
ナノ秒：980435500
</pre>

特殊な関数として、年月日を一度に取得できるDate関数、時分秒を一度に取得できるClock関数というものもあります。


## 日時に関する演算

Equal関数、Before関数、After関数を用いると、二つの日時を比較することができます。

```go
package main

import "fmt"
import "time"

func main() {
	base := time.Date(2015, 11, 1, 0, 0, 0, 0, time.Local)
    same := time.Date(2015, 11, 1, 0, 0, 0, 0, time.Local)
    before := time.Date(2015, 10, 31, 23, 59, 59, 0, time.Local)
    after := time.Date(2015, 11, 1, 0, 0, 1, 0, time.Local)

	fmt.Println(base.Equal(same))  // baseとsameが等しければtrue
	fmt.Println(base.Before(after)) // baseがafterよりも過去であればtrue
	fmt.Println(base.After(before)) // baseがbeforeよりも未来であればtrue
}
```

<pre class="output">
true
true
true
</pre>

Add関数を用いると日時に対する加減算ができます。

```go
package main

import "fmt"
import "time"

func main() {
	base := time.Date(2015, 11, 1, 0, 0, 0, 0, time.Local)
    fmt.Println(base)
    fmt.Println(base.Add(7 * time.Hour))
    fmt.Println(base.Add(30 * time.Minute))
    fmt.Println(base.Add(-5 * time.Second))
}
```

<pre class="output">
2015-11-01 00:00:00 +0900 JST
2015-11-01 07:00:00 +0900 JST
2015-11-01 00:30:00 +0900 JST
2015-10-31 23:59:55 +0900 JST
</pre>

Sub関数を用いると、日時同士の差を求めることができます。

```go
package main

import "fmt"
import "time"

func main() {
	base := time.Date(2015, 11, 1, 0, 0, 0, 0, time.Local)
    other := time.Date(2015, 10, 30, 20, 15, 32, 0, time.Local)
    fmt.Println(base)
    fmt.Println(other)
    fmt.Printf("二つの時刻の差は%s秒です。\n", base.Sub(other))
}
```

<pre class="output">
2015-11-01 00:00:00 +0900 JST
2015-10-30 20:15:32 +0900 JST
二つの時刻の差は27h44m28s秒です。
</pre>

### Duration型について

Time構造体のAdd関数に与える引数、Sub関数からの戻り値はint64型を元に再定義されたDuration型というユーザ定義型です。
この方は期間を表すための型で、timeパッケージにはあらかじめ以下の定数が定義されています。

```go
const (
        Nanosecond  Duration = 1
        Microsecond          = 1000 * Nanosecond
        Millisecond          = 1000 * Microsecond
        Second               = 1000 * Millisecond
        Minute               = 60 * Second
        Hour                 = 60 * Minute
)
```
