+++
date = "2015-10-06T15:09:02+09:00"
draft = false
title = "インタフェース"
author = "hondata"
categories = ["中級者向け"]
+++

# Go言語におけるポリモーフィズム

Go言語は厳密にはオブジェクト指向プログラミング言語ではありませんが、
インタフェースを用いることでポリモーフィズムを実現することができます。
例えば以下のコードを見てください。

```go
package main

import "fmt"

type Dog struct {}
func (d *Dog) Cry() {
    fmt.Println("わんわん")
}

type Cat struct {}
func (c *Cat) Cry() {
    fmt.Println("にゃーにゃー");
}

func MakeDogCry(d *Dog) {
    fmt.Println("鳴け！");
    d.Cry();
}

func MakeCatCry(c *Cat) {
    fmt.Println("鳴け！");
    c.Cry();
}

func main() {
    dog := new(Dog)
    cat := new(Cat)
    MakeDogCry(dog)
    MakeCatCry(cat)
}
```

犬と猫の構造体を用意して、それぞれ鳴かせてみました。でもこのコードは冗長です。鳴かせるための関数が重複しています。

「鳴く」という動作は（実際は例外はいるかもしれませんが）動物ならばみんなできる動作なので、
これをインタフェースに括り出してみましょう。

```go
package main

import "fmt"

type Animal interface {
    Cry()
}

type Dog struct {}
func (d *Dog) Cry() {
    fmt.Println("わんわん")
}

type Cat struct {}
func (c *Cat) Cry() {
    fmt.Println("にゃーにゃー");
}

func MakeAnimalCry(a Animal) {
    fmt.Println("鳴け！");
    a.Cry();
}

func main() {
    dog := new(Dog)
    cat := new(Cat)
    MakeAnimalCry(dog)
    MakeAnimalCry(cat)
}
```

Animalインタフェースを用いて鳴かせる関数を一つにまとめることができました。

## ダックタイピング

Animalインタフェースを作成した際、Dog構造体やCat構造体には何も変更を加えていません。
にも関わらず、DogやCatはMakeAnimalCry関数において「Animalインタフェースを実装した」とみなされています。

インタフェースの実装に明示的な宣言が必要なJavaやC#と違い、
Go言語ではインタフェースが必要とするメソッドをすべて実装した時点で、自動的にそのインタフェースを実装したとみなされます。

このような、振る舞いから型を決める仕組みは**ダックタイピング**と呼ばれ、（仕様は多少違いますが）Ruby等の言語でも採用されています。

## interface{}型について

もう一度、Animalインタフェースの定義に注目してみましょう

```go
type Animal interface {
    Cry()
}
```

これはtypeキーワードによる型定義になっていて、「Animal」は新しい型名です。
すなわち、以降の部分は「Cryメソッドを実装したインタフェース」を表す型名になっています。

同様に、「何のメソッドの実装も要求インタフェース」を表す型名も存在します。

```go
interface{}
```

このinterface{}型は実装の条件が無いので全てのオブジェクトはinterface{}型を実装していることになります。
これを利用すると、「どんな型も引数に取ることができる関数」や、「どんな型も返すことができる関数」を作ることが出来ます。

例えばfmt.Println関数はどんな型も引数に取ることができる関数の一つです。

```go
// Go言語ソースコードより抜粋
func Println(a ...interface{}) (n int, err error) {
	// 省略
}
```

### 型アサーション

interface{}型は何の振る舞いも要求しないため、そのままではメンバメソッドの呼び出しやメンバ変数へのアクセスをすることが出来ません。
これを解決するためには、interface{}型を別の型とみなして扱う必要があります。
そのために、Go言語には**型アサーション**という仕組みが用意されています。

```go
func MakeSomeoneCry(someone interface{}) {
    fmt.Println("鳴け！");
    a, ok := someone.(Animal)
    if !ok {
        fmt.Println("動物では無いので鳴けません。")
        return
    }
    a.Cry()
}
```

上記は先述のMakeAnimalCry関数をさらに拡張し、どのような型も受け取れるようにしたものです。
3行目が型アサーションの構文になっていて、interface型の変数someoneをAnimal型とみなして変数aに格納しています。

someoneがAnimal型とみなせる場合（Cryメソッドを持つ場合）、変数aにはAnimal型のsomeoneが格納され、二つ目の戻り値okはtrueになります。
そうでない場合は変数aへは値が代入されず、二つ目の戻り値はfalseになります。
