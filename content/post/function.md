+++
date = "2015-10-05T15:53:30+09:00"
draft = false
title = "関数・メソッド"
author = "hondata"
categories = ["中級者向け"]
+++

# 関数の定義

Go言語において関数の定義をするには、funcキーワードを使用します。
以下のコードではDisplayHelloという新しい関数を定義し、main関数内で呼び出しています。

```go
package main

import "fmt"

func main() {
    DisplayHello()
}

func DisplayHello() {
    fmt.Println("Hello!")
}
```

## 引数の指定

関数名の直後の丸括弧内に、引数リストを記述することができます。
引数は引数名・型名の順番に、スペース区切りで指定します。
複数の引数を指定する場合は引数名・型名の組同士をカンマで区切ります。

```go
package main

import "fmt"

func main() {
    DisplaySum(2, 5) // 7が表示される
}

func DisplaySum(left int, right int) {
    fmt.Println(left + right)
}
```

### 可変長引数

型名の前に...を用いることで、可変長引数を持つ関数を定義できます。
可変長引数は、関数内では配列のように動作します。

```go
package main

import "fmt"

func main() {
    DisplaySumAll(2, 5, 8, 11) // 26が表示される
}

func DisplaySumAll(values ...int) {
    sum := 0
    for _, value := range values {
        sum += value
    }
    fmt.Println(sum)
}
```

## 戻り値の指定

引数リストの後ろに型を指定することで、関数から戻り値を返すことができます。

```go
package main

import "fmt"

func main() {
    fmt.Println(Sum(2, 5)) // 7が表示される
}

func Sum(left int, right int) int {
    return left + right
}
```

### 戻り値を複数返す関数

戻り値の型を複数指定することで、複数の値を返す関数を定義できます。

```go
package main

import "fmt"

func main() {
    result, remainder := Div(19, 4)
    fmt.Printf("19を4で割ると%dあまり%dです。\n", result, remainder)
}

func Div(left int, right int) (int, int) { // 複数指定の場合は戻り値を丸括弧で囲む
    return left / right, left % right
}
```

## 関数のスコープ

変数や構造体と同様に、関数の名前が小文字から始まる場合はその関数は他のパッケージから使用できません。

# レシーバ変数とメソッド

関数名の前に**レシーバ変数**を指定することができます。

```go
package main

import "fmt"

type LoopNum int

func main() {
    var three LoopNum = 3
    three.TimesDisplay("Hello") // 「Hello」と3回表示される
}

func (n LoopNum) TimesDisplay(s string) {
    for i := 0; i < int(n); i++ {
        fmt.Println(s)
    }
}
```

レシーバ変数を指定した関数のことを、その変数の**メソッド**と呼びます。

Go言語はオブジェクト指向プログラミング言語ではありませんが、
構造体とそのメソッドを用いることである程度オブジェクト指向「らしく」プログラミングを行うことができます。

```go
package main

import "fmt"

type SavingBox struct {
    money int
}

func NewBox() *SavingBox {
    return new(SavingBox)
}

func (s *SavingBox) Income(amount int) {
    s.money += amount
}

func (s *SavingBox) Break() int {
    lastMoney := s.money
    s.money = 0
    return lastMoney
}

func main() {
    box := NewBox()
    box.Income(100)
    box.Income(200)
    box.Income(500)

    fmt.Printf("貯金箱を壊したら%d円出てきました。", box.Break())
}
```

なお、上記のコードにおいてIncomeメソッドやBreakメソッドは構造体メンバの値を書き換える必要があるため、
レシーバにはポインタ型を用いています。
