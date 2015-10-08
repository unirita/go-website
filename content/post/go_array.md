+++
date = "2015-10-06T15:16:00+09:00"
draft = false
title = "配列"
author = "shanxia"
categories = ["中級者向け"]
+++

# Goの配列について
「配列」とは、同じ型を持つ値（要素）を並べたものです。Go言語における配列は、C言語のものと似ていますが、C言語の配列がポインタで扱われるのに対して、Go言語では値として使用されます。
配列をC言語のポインタの様に使用するには、後述するスライスを利用します。

# 配列の書式
Go言語の配列は、C言語などとは異なり、角括弧の後に型を記述します。長さは宣言時に角括弧内に記述します。固定なので、後から変更はできません。

```go
// 配列の宣言方法
// [長さ] 型
[1]byte		// 1バイトのbyte型配列
[10]int		// 10バイトのint型配列
[12]struct {a, b rune}	// 構造体の配列
[2][8]rune	// 2次元配列
```

配列の長さを取得する場合は、「len」関数を使用します。

```go
// len関数の書式
長さ = len(配列型変数)
```

## 配列の使用方法
配列の要素にアクセスするには、配列の変数に角括弧を付け、その中にアクセスしたいインデックス番号を指定します。他の多くの言語と同様、配列のインデックスは「0」から始まります。
配列の使用例は次の通り。

```go
package main

import (
	"fmt"
)

func main() {
	// 配列の宣言
	var month [12]string
	month[0] = "January"
	month[1] = "February"
	month[2] = "March"
	month[3] = "April"
	month[4] = "May"
	month[5] = "June"
	month[6] = "July"
	month[7] = "Autust"
	month[8] = "September"
	month[9] = "October"
	month[10] = "Nobember"
	month[11] = "December"

	// 配列の長さの回数、ループして値を表示します。
	for i := 0; i < len(month); i++ {
		fmt.Printf("%d月 = %s\n", i+1, month[i])
	}
}
```

実行結果は次の通り。

>1月 = January
2月 = February
3月 = March
4月 = April
5月 = May
6月 = June
7月 = July
8月 = Autust
9月 = September
10月 = October
11月 = Nobember
12月 = December

配列にアクセスするfor文では、rangeを使用してアクセスすることも可能です。

```go
for i, m := range month {
	fmt.Printf("%d月 = %s\n", i + 1, m )
}
```

なお、配列に直接アクセスする場合は、長さを超えたインデックスにアクセスすると、ランタイムパニックが発生します。

```go
var month [12]string
i := 12
month[i] = "Undecimber" // ランタイムエラー
```

これを実行すると、次の様になります。

>panic: runtime error: index out of range

>goroutine 1 [running]:
main.main()
	/home/samples/main.go:23 +0x350
exit status 2

## 配列の初期化方法
配列の宣言とデータ格納を同時に行うには、いろいろな書き方があります。以下にサンプルを記述します。

```go
// 長さ5のint型配列。各要素はすべて0。
arr1 := [5]int64{}
// arr1と同じだが、指定していない要素はすべて0。
arr2 := [5]int64{1, 2, 3}
// 長さに...を指定すると、指定した要素数が長さとして使用される。
arr3 := [...]string{"One", "Two", "Three"}
```
