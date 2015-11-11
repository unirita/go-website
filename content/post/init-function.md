+++
date = "2015-11-11T14:32:35+09:00"
draft = false
title = "init関数による初期化処理"
author = "hondata"
categories = ["中級者向け"]
+++

# init関数

Go言語では、初期化処理を行うための関数としてinit関数を定義することができます。

```go
func init() {
    // 初期化処理
}
```

init関数はパッケージのロード時（main関数の実行前）に自動的に呼び出されます。
ただし、呼び出しは変数の宣言より後に行われます。

```go
package main

import "fmt"

var testVar int = showAndReturn("Declare", 1)

func init() {
    testVar = showAndReturn("Init", 2)
}

func main() {
    testVar = showAndReturn("Main", 3)
}

func showAndReturn(timing string, i int) int {
    fmt.Println(timing, ":", i)
    return i
}
```

上記のプログラムの出力は、以下の様になります。

<pre class="output">
Declare : 1
Init : 2
Main : 3
</pre>

## パッケージのinit関数のみを使用する

init関数は各パッケージごとに定義することができ、パッケージをimportすることで実行されるようになります。

```go
import "github.com/hoge/fuga"
```

まれに、init関数を実行するためだけにパッケージのインポートが必要になる場合があります。
このとき上記のようにimport文を書くと、パッケージのメンバを明示的に利用するコードが無いことからビルドエラーとなります。

以下の形でパッケージのインポートを行うことで、この問題を回避することができます。

```go
import _ "github.com/hoge/fuga"
```
