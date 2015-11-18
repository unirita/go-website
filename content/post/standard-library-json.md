+++
date = "2015-11-18T15:36:11+09:00"
draft = false
title = "JSONのパース/生成"
author = "hondata"
categories = ["便利な標準ライブラリ"]
+++

# encoding/jsonパッケージ

GoでJSON形式を扱うには、encoding/jsonパッケージを使用します。

## パース処理

JSON形式の文字列をパースするには、あらかじめJSONのデータ構造に合わせて構造体を定義し、Unmarshal関数を使用します。

```go
package main

import (
    "encoding/json"
    "fmt"
)

type Country struct {
    Name string              `json:"name"`
    Prefectures []Prefecture `json:"prefectures"`
}

type Prefecture struct {
    Name string    `json:"name"`
    Capital string `json:"capital"`
    Population int `json:"population"`
}

func main() {
    jsonStr := `
{
  "name": "日本",
  "prefectures": [
    {
      "name": "東京都",
      "capital": "東京",
      "population": 13482040
    },
    {
      "name": "埼玉県",
      "capital": "さいたま市",
      "population": 7249287
    },
    {
      "name": "神奈川県",
      "capital": "横浜市",
      "population": 9116252
    }
  ]
}
`
    jsonBytes := ([]byte)(jsonStr)
    data := new(Country)

    if err := json.Unmarshal(jsonBytes, data); err != nil {
        fmt.Println("JSON Unmarshal error:", err)
        return
    }

    fmt.Println(data.Name)
    fmt.Println(data.Prefectures[0].Name)
    fmt.Println(data.Prefectures[1].Capital)
    fmt.Println(data.Prefectures[2].Population)
}
```

<pre class="output">
日本
東京都
さいたま市
9116252
</pre>

### タグについて

構造体のメンバ変数宣言の後ろに\`json:"xxx"\`という形式の文字列が付けられていますが、これはタグと呼ばれるものです。
タグは変数の性質には全く影響しませんが、reflectパッケージを用いることで変数につけられたタグを取得することができます。

encoding/jsonパッケージでは、タグを利用してJSON内のキー名と各変数の結びつけを行っています。
なお、タグを省略した場合は変数名と全く同じ名前のキーが自動的に結び付けられます。


## 生成処理

構造体のJSON文字列表現を生成するには、Marshal関数を使用します。

```go
package main

import (
    "encoding/json"
    "fmt"
)

type Country struct {
    Name string              `json:"name"`
    Prefectures []Prefecture `json:"prefectures"`
}

type Prefecture struct {
    Name string    `json:"name"`
    Capital string `json:"capital"`
    Population int `json:"population"`
}

func main() {
    tokyo := Prefecture{Name:"東京都", Capital:"東京", Population:13482040}
    saitama := Prefecture{Name:"埼玉県", Capital:"さいたま市", Population:7249287}
    kanagawa := Prefecture{Name:"神奈川県", Capital:"横浜市", Population:9116252}
    japan := Country{
        Name:"日本",
        Prefectures:[]Prefecture{tokyo, saitama, kanagawa},
    }

    jsonBytes, err := json.Marshal(japan)
    if err != nil {
        fmt.Println("JSON Marshal error:", err)
        return
    }

    fmt.Println(string(jsonBytes))
}
```

<pre class="output">
{"name":"日本","prefectures":[{"name":"東京都","capital":"東京","population":13482040},{"name":"埼玉県","capital":"さいたま市","population":7249287},{"name":"神奈川県","capital":"横浜市","population":9116252}]}
</pre>

JSON文字列としてはこれで十分に書式通りですが、可読性のために改行やインデントを加えたい、というケースがあります。
その場合はIndent関数を使用し、インデント済みの文字列をbytes.Bufferに書き込みます。

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
)

type Country struct {
    Name string              `json:"name"`
    Prefectures []Prefecture `json:"prefectures"`
}

type Prefecture struct {
    Name string    `json:"name"`
    Capital string `json:"capital"`
    Population int `json:"population"`
}

func main() {
    tokyo := Prefecture{Name:"東京都", Capital:"東京", Population:13482040}
    saitama := Prefecture{Name:"埼玉県", Capital:"さいたま市", Population:7249287}
    kanagawa := Prefecture{Name:"神奈川県", Capital:"横浜市", Population:9116252}
    japan := Country{
        Name:"日本",
        Prefectures:[]Prefecture{tokyo, saitama, kanagawa},
    }

    jsonBytes, err := json.Marshal(japan)
    if err != nil {
        fmt.Println("JSON Marshal error:", err)
        return
    }

    out := new(bytes.Buffer)
    // プリフィックスなし、スペース4つでインデント
    json.Indent(out, jsonBytes, "", "    ")
    fmt.Println(out.String())
}
```

<pre class="output">
{
    "name": "日本",
    "prefectures": [
        {
            "name": "東京都",
            "capital": "東京",
            "population": 13482040
        },
        {
            "name": "埼玉県",
            "capital": "さいたま市",
            "population": 7249287
        },
        {
            "name": "神奈川県",
            "capital": "横浜市",
            "population": 9116252
        }
    ]
}
</pre>
