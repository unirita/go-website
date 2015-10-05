+++
date = "2015-10-05T15:10:37+09:00"
draft = true
title = "構造体"
author = "hondata"
categories = ["中級者向け"]
+++

# Goの構造体型

Go言語では、複数の要素を組にした構造体型を使用することができます。

```go
package main

import "fmt"

func main() {
    var vector struct {
        X int
        Y int
    }

    vector.X = 2
    vector.Y = 5
    fmt.Println(vector) // {2 5}
}
```

上記のコードでは二つのint型の要素を組にして、2次元ベクトルを表す構造体型を使用しています。
ここで、typeキーワードを使用すると以下の様にコードを改善することができます。

```go
package main

import "fmt"

type Vector struct {
    X int
    Y int
}

func main() {
    var v Vector

    v.X = 2
    v.Y = 5
    fmt.Println(v)
}
```

また、上記のコードでは変数vectorの宣言後に個別にメンバの値をセットしていますが、次のように一度に初期化することも可能です。

```go
package main

import "fmt"

type Vector struct {
    X int
    Y int
}

func main() {
    v := Vector{X: 2, Y: 5}
    fmt.Println(v)
}
```

## 構造体の可視性

構造体のメンバのうち、小文字から始まる名前を持つものはパッケージ外からアクセスできません。

```go
package main

import "test"

/* testパッケージ内に以下の構造体定義があったとする。
type Vector3 struct {
    X int
    Y int
    z int
}
*/

func main() {
    var v test.Vector3
    v.X = 2  // OK
    v.Y = 5  // OK
    v.z = 8  // エラー
}
```

また、構造体名が小文字から始まる場合、パッケージ外でその構造体を作成することはできません。

```go
package main

import "test"

/* testパッケージ内に以下の構造体定義があったとする。
type vector3 struct {
    X int
    Y int
    Z int
}
*/

func main() {
    var v test.vector3 // エラー
}
```

なお、構造体と同パッケージの関数の戻り値としてならば、小文字から始まる構造体をパッケージ外から使用することができます。

```go
package main

import "test"

/* testパッケージ内に以下の構造体定義があったとする。
type vector3 struct {
    X int
    Y int
    Z int
}

func NewVector() vector3 {
    var v vector3
    return v
}
*/

func main() {
    v := test.NewVector()
    v.X = 2  // OK
    v.Y = 5  // OK
    v.Z = 8  // OK
}
```
