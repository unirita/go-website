+++
date = "2015-11-11T15:49:37+09:00"
draft = true
title = "実行時引数の処理"
author = "kazami"
categories = ["便利な標準ライブラリ"]
+++

# 実行時引数の処理方法

Goで実行時引数を扱うには、osパッケージを利用する方法と、flagパッケージを利用する方法があります。

## osパッケージを利用する方法

osパッケージのos.Argsを扱うのが一番基礎的な方法です。  
os.Argsはstring型のスライスで定義されており、実行時引数が代入されます。  

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	fmt.Println(os.Args)

	if len(os.Args) != 4 {
		fmt.Println("指定された引数の数が間違っています。")
		os.Exit(1)
	}

	fmt.Printf("実行プロセス: %s\n", os.Args[0])
	fmt.Printf("引数1: %s\n", os.Args[1])
	fmt.Printf("引数2: %s\n", os.Args[2])
	fmt.Printf("引数3: %s\n", os.Args[3])
}
```
実行
<pre class="output">
% ./argument arg1 arg2 arg3
</pre>

実行結果
<pre class="output">
[argument.exe arg1 arg2 arg3]
実行プロセス: argument.exe
引数1: arg1
引数2: arg2
引数3: aeg3
</pre>

実行結果から分かる通り、os.Argsの最初には実行プロセス名が入ります。

## flagパッケージを利用する方法
実行時引数にオプションを指定するなど、少し複雑な処理をしたい場合はflagパッケージを使用します。

flagパッケージの使用手順は、以下の通りになります。  
1. オプションの名前、型、初期値を定義する  
2. flagパッケージのParse関数を用いて実行時引数を解析する  

**1. オプションの名前、型、初期値などを定義する**  
以下のように、オプションの情報を変数に格納します。  

<pre class="output">
import "flag"
var flagvar = flag.String("flagname", "default", "help message for flagname")
</pre>
ここでは、値にstring型の文字列が入るオプションを定義しています。  
* filename の部分にはオプション名を指定します。  
* default の部分にはオプションのデフォルト値を指定します。  
* help message for flagname の部分にはオプションの使用方法を記述します。  


また、あらかじめ定義した変数に格納することも可能です。
<pre class="output">
var flagvar string
func init() {
	flag.StringVar(&flagvar, "flagname", "default", "help message for flagname")
}
</pre>

**2. flagパッケージのParse関数を用いて実行時引数を解析する**  
定義したオプションは、flag.Parse関数で使用できるようになります。
<pre class="output">
flag.Parse()
</pre>

以下は実行例です。  

コード  
```go
package main

import (
	"fmt"
	"flag"
)

func main() {
	var f = flag.Int("f", 1234, "help message for f")
	var b = flag.Bool("b", false, "help message for bool")
	var s = flag.String("s", "default", "help message for long")
	flag.Parse()

	fmt.Println(*f)
	fmt.Println(*b)
	fmt.Println(*s)
}

```

実行例1  
<pre class="output">
% ./flag -help
</pre>
helpオプションを指定すると、オプションのUsageが表示されます。
結果1  
<pre class="output">
Usage of ./flag:
  -f=1234: help message for f
  -s="default": help message for long
  -b=false: help message for str
</pre>

実行例2  
<pre class="output">
% ./flag -f 11 -s message -b
</pre>

結果2  
<pre class="output">
11
true
message
</pre>

なお、オプションの指定では、-fの他に--f --f=10といった指定も可能です。  

flagパッケージの詳細な使用方法については、[公式ドキュメント](https://golang.org/pkg/flag/#String)に記載されています。  

