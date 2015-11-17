+++
date = "2015-11-13T11:13:27+09:00"
draft = false
title = "設定ファイルのライブラリ"
author = "kazami"
categories = ["外部ライブラリの利用"]
+++

# 設定ファイル
設定ファイルは、アプリケーションや、コンピュータの設定上の条件を記述したファイルです。  
設定ファイルには数多くの形式があり、Go言語で設定ファイルを扱う処理を記述する場合、便利な外部ライブラリを使用することが出来ます。  

以下では、設定ファイルを扱う際に便利な外部ライブラリを紹介します。


## TOML形式の設定ファイルを扱う
TOML形式とは、GitHubのTom Preston-Werner氏が考案した設定ファイル形式です。詳細は[公式ページ](https://github.com/toml-lang/toml)を参照してください。  

外部ライブラリは、GitHubで公開されているものを使用します。  
[ライブラリのページ](https://github.com/BurntSushi/toml)

ライブラリの取得には、以下のコマンドを実行してください。 
 
<pre class="output">
go get github.com/BurntSushi/toml
</pre>

以下では、ライブラリの簡単な使用例を記載します。　　

設定ファイル(example.toml)  

<pre class="output">
[server]
host = "localhost"
port = "3306"

[[server.slave]]
weight = 1
ip = "10.0.0.1"

[[server.slave]]
weight = 5
ip = "10.0.0.2"

[[server.slave]]
weight = 3
ip = "10.0.0.3"

[[server.slave]]
weight = 2
ip = "10.0.0.4"

[db]
user = "root"
pass = "pass"
</pre>


使用例(設定ファイルのパラメータの値を取得する)  

```go
package main

import (
    "fmt"
    "github.com/BurntSushi/toml"
)

type Config struct {
    Server ServerConfig
    Db     DbConfig
}

type ServerConfig struct {
    Host  string        `toml:"host"`
    Port  string        `toml:"port"`
    Slave []SlaveServer `toml:"slave"`
}

type DbConfig struct {
    User string `toml:"user"`
    Pass string `toml:"pass"`
}

type SlaveServer struct {
    Weight int    `toml:"weight"`
    Ip     string `toml:"ip"`
}

func main() {
    var config Config
    _, err := toml.DecodeFile("config.tml", &config)
    if err != nil {
        panic(err)
    }
    fmt.Printf("Host is :%s\n", config.Server.Host)
    fmt.Printf("Port is :%s\n", config.Server.Port)
    for k, v := range config.Server.Slave {
        fmt.Printf("Slave %d\n", k)
        fmt.Printf("  weight is %d\n", v.Weight)
        fmt.Printf("  ip is %s\n", v.Ip)
    }
    fmt.Printf("DB Username is :%s\n", config.Db.User)
    fmt.Printf("DB Password is :%s\n", config.Db.Pass)
}
```

順番に処理を説明します。  
まず、設定ファイルの各テーブル([server]等)に合わせた構造体を定義しています。  
構造体には、テーブルのパラメータを格納するためのメンバ変数を定義し、横に`toml:"設定ファイルのパラメータ名"`と記述することで設定ファイルのパラメータにマップしています。  
次に、外部ライブラリのtoml.DecodeFile関数を用いて設定ファイルを読み込み、第二引数に渡した構造体にパラメータの値を格納しています。  

実行結果は以下のようになります。

<pre class="output">
Host is :localhost
Port is :3306
Slave 0
  weight is 1
  ip is 10.0.0.1
Slave 1
  weight is 5
  ip is 10.0.0.2
Slave 2
  weight is 3
  ip is 10.0.0.3
Slave 3
  weight is 2
  ip is 10.0.0.4
DB Username is :root
DB Password is :pass
</pre>


