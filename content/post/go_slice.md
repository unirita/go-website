+++
date = "2015-10-05T16:06:22+09:00"
draft = false
title = "スライス1"
author = "shanxia"
categories = ["中級者向け"]
+++

# スライスとは[前編]
スライスは、配列と同様にインデックスを使って要素へアクセスするものです。配列と違うのは、スライスは要素データを参照型で保持します。スライスが持つデータは、配列の一部（または全部）と紐付け、間接的に参照します。
Go言語では、配列を直接扱うよりも、スライス経由の方が利便性が高いため、多くの場合でスライスを使用します。

## スライスの書式
スライス型は、配列と同様に角括弧を使用しますが、長さは指定しません。

```go
[]型
```

## スライスの使用方法
スライスを作成するには、「スライス式」を使用します。スライス式は、角括弧の内にスライスに含める要素の範囲をコロン（：）で区切って記述します。作成するスライスに含められる要素は、作成元の配列のインデックスの「下限値（0）」〜「上限値-1（配列の長さ）」までです。省略した際は、それぞれ括弧内の数値を指定したことになります。

スライスの使用例は次の通り。

```go
package main

import (
	"fmt"
)

func main() {
	// スライスの元となる配列を作成
	num := [5]int{1, 2, 3, 4, 5}
	// スライス型変数の宣言
	var slice1 []int

	// 配列全体
	slice1 = num[:]
	fmt.Println(slice1)

	// インデックス1〜4まで
	slice2 := num[1:4]
	fmt.Println(slice2)

	// インデックス4以降
	slice3 := num[4:]
	fmt.Println(slice3)

	// インデックス4以前
	slice4 := num[:4]
	fmt.Println(slice4)
}
```

実行結果は、次の通り。

>[1 2 3 4 5]  
[2 3 4]  
[5]  
[1 2 3 4]

## スライスを引数に渡す
最初に書いたとおり、スライスはデータをすべて参照型で保持します。そのため、大規模なデータをスライスに保持しても、メモリを圧迫することはありません。また、関数の引数として値を受け渡す場合は、常に参照渡しとなります。

```go
package main

import (
	"fmt"
)

func main() {
	// スライスの元となる配列を作成
	num := [...]int{1, 2, 3, 4, 5}
	// 配列をスライスとしてを関数に渡す
	plusOne(num[:])

	fmt.Println(num)
}

// 要素内の数字すべてを＋1
func plusOne(vals []int) {
	for i := 0; i < len(vals); i++ {
		vals[i] += 1
	}
}
```

実行結果は、次の通り。

>[2 3 4 5 6]

## キャパシティ
スライスが扱える要素数は、長さとして「len」関数で取得できます。そのほかにキャパシティという、スライスから新たにスライスを作成するときに指定する要素数の最大値を持っています。キャパシティは、「cap」関数で取得することができます。
スライスのキャパシティの例は次の通り。

```go
package main

import (
	"fmt"
)

func main() {
	// スライスの元となる配列を作成
	num := [5]int{1, 2, 3, 4, 5}

	// 配列の一部をスライス
	slice1 := num[1:4]
	fmt.Println("slice1=", slice1)
	fmt.Println("len=", len(slice1))
	fmt.Println("cap=", cap(slice1))

	// スライスの一部をスライス
	slice2 := slice1[1:4] // 長さを超過した、キャパシティ最大値まで可能
	fmt.Println("slice2=", slice2)
	fmt.Println("len=", len(slice2))
	fmt.Println("cap=", cap(slice2))

}
```

実行結果は、次の通り。

>slice1= [2 3 4]  
len= 3  
cap= 4  
slice2= [3 4 5]  
len= 3  
cap= 3

slice2では、slice1の長さを超えた参照先の配列へアクセスできます。

長くなってきたので、スライスは後編に続きます。
