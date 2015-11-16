+++
date = "2015-11-12T13:47:01+09:00"
draft = false
title = "コマンドパラメータの処理"
author = "kazami"
categories = ["便利な標準ライブラリ"]
+++

# コマンドパラメータの処理方法

Goでコマンドパラメータを扱うには、osパッケージを利用する方法と、flagパッケージを利用する方法があります。

## osパッケージを利用する方法

osパッケージのos.Argsを扱う方法です。  
os.Argsはstring型のスライスで定義されており、コマンドパラメータが代入されます。  

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

	fmt.Printf("実行ファイル名: %s\n", os.Args[0])
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
[argument arg1 arg2 arg3]
実行ファイル名: argument
引数1: arg1
引数2: arg2
引数3: aeg3
</pre>

実行結果から分かる通り、os.Argsの最初には実行ファイル名が入ります。

## flagパッケージを利用する方法
コマンドパラメータにオプションを指定するなど、少し複雑な処理をしたい場合はflagパッケージを使用します。

まず、以下のコードを見てみましょう。

```go
package main

import (
	"flag"
	"fmt"
)

var (
	intOpt  = flag.Int("i", 1234, "help message for i option")
	boolOpt = flag.Bool("b", false, "help message for b option")
	strOpt  = flag.String("s", "default", "help message for s option")
)

func main() {

	flag.Parse()

	fmt.Println(*intOpt)
	fmt.Println(*boolOpt)
	fmt.Println(*strOpt)
}
```

実行

<pre class="output">
% ./argument -i 11 -b -s test
</pre>


実行結果

<pre class="output">
11
true
test
</pre>

---

では、コードの内容を解説します。

<pre class="output">
var (
    intOpt  = flag.Int("i", 1234, "help message for i option")
	boolOpt = flag.Bool("b", false, "help message for b option")
	strOpt  = flag.String("s", "default", "help message for s option")
)
</pre>

ここでは、オプションの定義を行っています。  
オプションの定義では、オプションの型、オプション名、デフォルト値、ヘルプメッセージ(後述)を定義します。  
  
細かく見てみましょう。  
<pre class="output">
intOpt  = flag.Int("i", 1234, "help message for i option")
</pre>

* flag.Intは、Int型の値をとるオプションを定義するflagパッケージの関数です
* 第一引数はオプション名です
* 第二引数はオプションのデフォルト値です。オプションを指定しない場合は、デフォルト値がオプションの値に入ります。
* 第三引数は、オプションの説明を記述しています。記述したメッセージは、flagパッケージの機能であるhelpオプションを使用した際に扱われます。※後述の「helpオプション」を参照


flag.Stringはflag.Intと同じ働きをしますが、String型の値をとるオプションを定義します。  

---

<pre class="output">
flag.Parse()
</pre>

flag.Parse関数を用いて、コマンドラインを解析し、定義したオプションにセットします。  

---

### flag.Bool関数について
flag.Bool関数では、定義したオプションが、実行時に指定されているかどうかを判断し、booleanの値を格納します。  
bオプションを指定した場合  

<pre class="output">
% ./argument -i 11 -b -s test
</pre>

実行結果

<pre class="output">
11
true
test
</pre>

bオプションを指定なかった場合(デフォルト値はfalse)

<pre class="output">
% ./argument -i 11 -s test
</pre>

実行結果

<pre class="output">
11
false
test
</pre>

---

### helpオプション
flagパッケージでは、実行時のオプションにhelpオプションを指定する事により、オプションの使用方法を表示する機能が標準で備わっています。

helpオプションを指定して実行

<pre class="output">
% ./argument -help
</pre>

実行結果
<pre class="output">
Usage of argument:
  -b    help message for b option
  -i int
        help message for i option (default 1234)
  -s string
        help message for s option (default "default")
</pre>

各オプションの説明には、オプションの定義の段階で定義したメッセージが表示されます。  

---

### オプションの指定方法
これまでの実行例では、オプションの指定方法は **-[オプション名]**　としてきましたが、  
以下のような指定方法も可能です。  
<pre class="output">
-i 11  
--i 11  
-i=11  
</pre>

flagパッケージの詳細な使用方法については、[公式ドキュメント](https://golang.org/pkg/flag/)に記載されています。  
