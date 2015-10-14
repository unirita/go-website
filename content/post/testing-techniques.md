+++
date = "2015-10-08T16:14:57+09:00"
draft = false
title = "テスティングに関連するテクニック"
author = "hondata"
categories = ["Goのテスト"]
+++

# スタブ・モックについて

残念ながら、Go言語本体にはスタブ・モックの仕組みはありません。
しかし、依存性の注入を行うことでスタブ程度であれば比較的簡単に実現ができます。
例として、以下の製品コードとテストコードを見てみましょう。

[GoCutoサーバント session.go（製品コード）](https://github.com/unirita/cuto/blob/master/src/cuto/servant/remote/session.go)  
[GoCutoサーバント session_test.go（製品コード）](https://github.com/unirita/cuto/blob/master/src/cuto/servant/remote/session_test.go)

session.go内で定義されているSession構造体では、別のマシンとの通信処理を行っています。
このような処理は非常にテストが難しいため、以下の様に通信処理を行う部分を関数型のメンバdoJobRequestとして切り出しています。

```go
type Session struct {
	// 別メンバの定義は省略
	doJobRequest   func(req *message.Request, conf *config.ServantConfig, stCh chan<- string) *message.Response
}
```

そして、コンストラクタ関数NewSession内でdoJobRequestへ実際に通信を行う関数を代入しています。
製品コードはこのロジックを必ず通るように書かれています。

```go
func NewSession(conn net.Conn, body string) *Session {
	s := new(Session)
	s.Conn = conn
	s.Body = body
	s.doJobRequest = job.DoJobRequest   // <- ここで別パッケージの通信用関数を代入している
	s.startHeartbeat()
	return s
}
```

一方、テストコード（session_test.go）では固定値を代入するスタブメソッドを用意しています。

```go
func doTestRequest(req *message.Request, conf *config.ServantConfig, stCh chan<- string) *message.Response {
	res := new(message.Response)
	res.NID = req.NID
	res.RC = 1
	// 省略

	return res
}
```

そして、テストコード中でNewSessionを使わずにSessionオブジェクトの生成をすることで、
テスト用のスタブメソッドをセットしています。

```go
session := Session{Conn: conn, Body: reqMsg, doJobRequest: doTestRequest}
```

# TestMainの活用

テスト実行前後に準備処理・終了処理が欲しくなるケースがあります。
そのような時は、TestMain関数を定義してテストのメインルーチンを置き換えることができます。

```go
import "testing"
import "time"

func TestMain(m *testing.M) {
    time.Local = time.FixedZone("JST", 9 * 60 * 60)

    testRC := m.Run()
	os.Exit(testRC)
}
```

例えば上記のコードをテストコードに追加することで、
テスト実行時のローカルタイムゾーンをJST(+0900)に固定することができます。
このように、テスト全体にかかわる設定やデータ準備が必要な場合に使用すると非常に便利です。

# 再帰的なテスト実行

go testコマンドにはパッケージディレクトリ名を引数として指定しますが、
プロジェクト内のすべてのパッケージを一度にテストする際にいちいちパッケージ名を指定するのは面倒です。
以下の様にコマンドを実行すると、指定ディレクトリ以下のすべてのパッケージをテストすることができます。

<pre class="output">
go test ディレクトリ名/...
</pre>

ディレクトリ名を指定しない場合は、GOPATHに含まれるすべてのパッケージがテスト対象となります。

<pre class="output">
go test ...
</pre>

# Jenkinsとの連携

Jenkins上でGo言語のテスト成否を取得するのには、特に特別な設定は不要です。
Jenkinsからgo testコマンドを実行することで、自動的にテスト成否がビルド成否と結びつきます。
