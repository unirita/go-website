+++
date = "2015-10-13T13:08:27+09:00"
draft = false
title = "HTTP通信"
author = "hondata"
categories = ["通信"]
+++

# HTTPサーバの実装

Go言語でHTTP通信をするには、net/httpパッケージを使用します。
ソケット通信と比べるとかなりシンプルに実装を行うことができます。

```go
package main

import (
    "fmt"
    "net/http"
)

var todos []string;

func main() {
    todos = make([]string, 0);
    http.HandleFunc("/todos", showToDo)
    http.HandleFunc("/todos/new", addToDo)
    http.ListenAndServe(":80", nil)
}

func showToDo(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintln(w, "<html>")
    fmt.Fprintln(w, "<head><title>ToDo List</title></head>")

    fmt.Fprintln(w, "<body>")
    fmt.Fprintln(w, "<h1>ToDo List</h1>")

    fmt.Fprintln(w, "<ul>")
    for _, todo := range todos {
        fmt.Fprintf(w, "<li>%s</li>\n", todo)
    }
    fmt.Fprintln(w, "</ul>")

    fmt.Fprintln(w, "<h2>Add ToDo</h2>")
    fmt.Fprintln(w, `<form method="post" action="/todos/new">`)
    fmt.Fprintln(w, `<input type="text" name="todo">`)
    fmt.Fprintln(w, `<input type="submit" name="Add">`)
    fmt.Fprintln(w, `</form>`)

    fmt.Fprintln(w, "</body>")
    fmt.Fprintln(w, "</html>")
}

func addToDo(w http.ResponseWriter, r *http.Request) {
    receiveValue := r.FormValue("todo")
    todos = append(todos, receiveValue)
    http.Redirect(w, r, "/todos", 303)
}
```

上記は簡易なToDoリストアプリケーションです。
プログラムを起動し、ブラウザから http://localhost/todos へアクセスすることで、
ToDoリストの表示とフォーム入力によるリストへの値追加を行うことができます。

このプログラムの実装について、順を追って説明します。

## ハンドラ関数の定義

showToDo関数およびaddToDo関数はハンドラ関数と呼ばれるもので、HTTPリクエスト発生時に呼び出される具体的な処理が書かれています。
ハンドラ関数は、必ず以下の決まった形で定義します。

```go
func 関数名(w http.ResponseWriter, r *http.Request) {
    // 実行する処理
}
```

### http.ResponseWriterインタフェース

ハンドラ関数の第一引数には、http.ResponseWriterインタフェース型のオブジェクトが渡されます。
このオブジェクトはHTTPレスポンスヘッダへの値セットや、HTTPレスポンスボディへの出力に使用します。

先述のコードでは、http.ResponseWriterインタフェース型のオブジェクトがio.Writerインタフェースを実装していることを利用し、
fmt.Fprintln/Fprintfを用いて出力を行っています。

```go
fmt.Fprintln(w, "<html>")
fmt.Fprintln(w, "<head><title>ToDo List</title></head>")
```

### http.Request構造体

ハンドラ関数の第二引数には、http.Request構造体型のオブジェクトが渡されます。
この構造体にはHTTPリクエストの内容が格納されています。

先述のコードでは、http.Request構造体からリクエストパラメータ（フォーム入力値）を取得しています。

```go
// 入力フォーム：<input type="text" name="todo">
receiveValue := r.FormValue("todo")
```

### リダイレクト

Webアプリケーションでは、データ登録処理等の後に別ページへのリダイレクトを行うことが良くあります。
これを実現するには、http.Redirect関数を使用します。

先述のコードでは、ToDoの追加処理完了後にToDoの表示処理へリダイレクトを行っています。

```go
http.Redirect(w, r, "/todos", 303)
```

## ハンドラ関数の登録

main関数内でhttp.HandlerFunc関数を使用することで、
アクセスパスとハンドラ関数の結びつけを行っています。

```go
http.HandleFunc("/todos", showToDo)
http.HandleFunc("/todos/new", addToDo)
```

## サーバの起動

全てのハンドラ関数の登録が完了したら、サーバの起動を行います。

```go
http.ListenAndServe(":80", nil)
```

# HTTPクライアントの実装

net/httpパッケージを使用することで、HTTPリクエスト送信も簡単に行うことができます。
以下に最もシンプルなGETリクエストの例を記載します。

```go
package main

import (
    "fmt"
	"io"
    "net/http"
)

func main() {
	res, err := http.Get("https://golang.org")
	if err != nil {
		fmt.Println("Request error:", err)
		return
	}
	defer res.Body.Close()

	buf := make([]byte, 256)
	for {
		n, err := res.Body.Read(buf)
		if n == 0 || err == io.EOF {
			break;
		} else if err != nil {
			fmt.Println("Read response body error:", err)
			return
		}
		fmt.Println(string(buf[:n]))
	}
}
```

上記のコードはGo言語の公式サイトの内容を標準出力するプログラムです。

## GETリクエストの送信

```go
res, err := http.Get("https://golang.org")
```

http.Get関数を使用することで、HTTP GETリクエストを簡単に送信することができます。
同様にPOSTリクエストを送信するhttp.Post関数や、より詳細にリクエストの設定ができるhttp.Client#Do関数も用意されています。

## レスポンスの取得

レスポンスはhttp.Get関数の戻り値として取得することができます。
レスポンスから内容を取得するにはBodyメンバへアクセスします。
これはio.Readerインタフェースを実装しています。

```go
n, err := res.Body.Read(buf)
```

なお、Bodyメンバは使い終わったら必ずCloseしてください。リソースリークの原因になります。

```go
defer res.Body.Close()
```
