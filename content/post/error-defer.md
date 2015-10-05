+++
date = "2015-10-01T10:58:40+09:00"
draft = false
title = "戻り値によるエラーハンドリング"
author = "shanxia"
categories = ["エラーハンドリング"]
+++

# 関数の戻り値によるエラーハンドリング
Go言語では、関数の戻り値に複数の値を返すことができます。これを利用してエラーハンドリングを行うことができます。というよりも、標準関数を含むほとんどで、2つめの戻り値にエラー情報を返しています。
エラー情報には、「errorインタフェース型」を使用します。これはGo言語内部のbuiltinパッケージに組み込まれている型で、Errorメソッドを利用して詳細情報を取得します。

```go
// エラーインタフェースの定義内容
type error interface{
  Error() string
}
```

error型の値を返す関数では、エラーが発生しないときは「nil」が返されます。次に戻り値によるエラーハンドリングの例を示します。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	// ファイルを開く
	file, err := os.Open("test.txt")
	// エラー判定
	if err != nil {
		// 失敗
		fmt.Println(err.Error())
	} else {
		// 成功
		fmt.Println("Successful!")
		file.Close()
	}
}
```

err.Error()の戻り値はstring型なので、以下の様なエラーメッセージが表示されます。

> open test.txt: no such file or directory

# 自作した関数のエラー処理
自分で関数を自作した場合も同様に、「error」型を使った戻り値をとることで、Go言語として一貫したエラー処理を行うことができます。
なのでできるだけ、この仕組みにあわせることをおすすめします。
以下に自作関数の例を示します。

```go
package main

import (
	"fmt"
	"os"
)

type myAppError struct {
	msg string //エラーメッセージ
}

func (e myAppError) Error() string {
	// エラーメッセージを返す
	return e.msg
}

func testOpen() (file os.File, err error) {
	file, err := os.Open("test.txt")
	if err != nil {
		// エラーが発生したので、myAppError構造体の値を作成し、エラー情報として返す
		return nil, myAppError{"ファイルが開けません - " + err.Error()}
	}
	// 成功した場合は、error情報をnilとして返す。
	return file, nil
}

func main() {
	file, err := testOpen()
	if err != nil {
		// エラー情報が戻ってきたので、エラーとして処理する。
		fmt.Println("失敗しました。 - " + err.Error())
		os.Exit(1)
	}
	// 後略
}
```
