+++
date = "2015-11-13T15:56:17+09:00"
draft = false
title = "文字列処理"
author = "hondata"
categories = ["便利な標準ライブラリ"]
+++

# stringsパッケージ

stringsパッケージは文字列操作を行う関数を提供します。


## 文字列が他の文字列を含むか調べる

文字列が、別の文字列を含んでいるか調べるにはContains関数を使用します。

```go
package main

import "fmt"
import "strings"

func main() {
    fmt.Println(strings.Contains("abcdefg", "cde"))
    fmt.Println(strings.Contains("abcdefg", "hij"))
}
```

<pre class="output">
true
false
</pre>

より特殊なケースとして、文字列が別の文字列で開始しているかを調べるにはHasPrefix関数を、
文字列が別の文字列で終端しているかを調べるにはHasSuffix関数を使用します。

```go
package main

import "fmt"
import "strings"

func main() {
    fmt.Println(strings.HasPrefix("abcdefg", "abc"))
    fmt.Println(strings.HasPrefix("abcdefg", "bcd"))
    fmt.Println(strings.HasSuffix("abcdefg", "def"))
    fmt.Println(strings.HasSuffix("abcdefg", "efg"))
}
```

<pre class="output">
true
false
false
true
</pre>


## 大文字・小文字の変換

ToUpper関数、ToLower関数を使用することで、文字列を大文字・小文字に変換することができます。

```go
package main

import "fmt"
import "strings"

func main() {
    base := "aBcDeF"
    fmt.Println(strings.ToUpper(base))
    fmt.Println(strings.ToLower(base))
}
```

<pre class="output">
ABCDEF
abcdef
</pre>


## 文字列のトリム

指定した文字を文字列の先端から取り除くにはTrimLeft関数、末尾から取り除くにはTrimRight関数、両端から取り除くにはTrim関数を使用します。

```go
package main

import "fmt"
import "strings"

func main() {
    base := "!!!?!??   abcdef???!!!"
    fmt.Println(strings.TrimLeft(base, "!"))
    fmt.Println(strings.TrimRight(base, "!?"))
    fmt.Println(strings.Trim(base, "!? "))
}
```

<pre class="output">
?!??   abcdef???!!!
!!!?!??   abcdef
abcdef
</pre>


## 文字列の置換

文字列置換を行うにはReplace関数を使用します。
引数は左から順にベース文字列、置換前の部分文字列、置換後の部分文字列、最大置換回数です。
ベース文字列中の全ての部分文字列を置換する場合は、最大置換回数に-1を指定します。

```go
package main

import "fmt"
import "strings"

func main() {
    base := "abcabcabcabc"
    fmt.Println(strings.Replace(base, "bc", "yz", 2))
    fmt.Println(strings.Replace(base, "abc", "xyz", -1))
}
```

<pre class="output">
ayzayzabcabc
xyzxyzxyzxyz
</pre>

## 文字列の分割

セパレータを用いて文字列をスライスに分割するにはSplit関数を使用します。
また、分割後の要素数を限定する場合はSplitN関数を使用します。

```go
package main

import "fmt"
import "strings"

func main() {
    base := "ab::cd::efg::hij"
    fmt.Println(strings.Split(base, "::"))
    fmt.Println(strings.SplitN(base, "::", 3))
}
```

<pre class="output">
[ab cd efg hij]
[ab cd efg::hij]
</pre>


## 文字列スライスの結合

スライスの各要素の間を文字列でつなぎ、1つの文字列に結合するにはJoin関数を使用します。

```go
package main

import "fmt"
import "strings"

func main() {
    array := []string{"C:", "work", "abc.txt"}
    fmt.Println(strings.Join(array, "/"))
}
```

<pre class="output">
C:/work/abc.txt
</pre>
