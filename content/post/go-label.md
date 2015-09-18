+++
date = "2015-09-17T13:08:32+09:00"
draft = false
title = "Goのラベル"
author = "shanxia"
categories = ["Go言語の基本"]
+++

# Goのラベルについて
Go言語では、「break」文、「continue」文、「goto」文の宛先として、ラベルを使用することができます。
ラベルの有効範囲は、宣言した関数内のみとなります。書式は、他の言語のラベルと同様、次の様になります。
> ラベル名:

## break文でのラベル
通常のbreak文は、所属する最も内側の「for」文、「switch」文、「select」文から抜け出しますが、ラベルを使用すると、任意の文から抜け出すことができます。

```go
package main

import "fmt"

func main() {
FOR_LABEL:
	for i := 0; i < 10; i++ {
		switch {
		case i == 3:
			// for文からの脱出
			break FOR_LABEL

		default:
			fmt.Println(i)
		}
	}
}
```
これを実行すると、次の様に表示されます。
>0
>1
>2

## continue文でのラベル
break文と同様に、任意の「for」文の実行を、中断させることができます。

```go
LABEL1:
for i := 0; i < 10; i++ {
	for j := 0; j < 10; j++ {
		if i == 0 && j == 5 {
			// 1番目のforへcontinue
			continue LABEL1
		} else if i == 1 && j == 1 {
			// 2番目のforへcontinue
			continue
		}
	}
}
```

## goto文でのラベル
関数内のラベルが宣言された箇所へ移動します。

```go
for i := 0; i < 10; i++ {
	if i == 2 {
		// for文の外にあるLABELへ移動
		goto LABEL
	}
}
LABEL:

```
