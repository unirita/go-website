+++
date = "2015-11-19T09:26:25+09:00"
draft = false
title = "XMLのパース/生成"
author = "hondata"
categories = ["便利な標準ライブラリ"]
+++

# encoding/xmlパッケージ

GoでXML形式を扱うには、encoding/xmlパッケージを使用します。

## パース処理

XML形式の文字列をパースするには、あらかじめXMLのデータ構造に合わせて構造体を定義し、Unmarshal関数を使用します。

```go
package main

import (
    "encoding/xml"
    "fmt"
)

type Group struct {
    Name      string    `xml:"name"`
    Companies []Company `xml:"company"`
}

type Company struct {
    Name    string  `xml:"name"`
    Website Website `xml:"website"`
}

type Website struct {
    Name string `xml:",chardata"`
    URL  string `xml:"url,attr"`
}

func main() {
    xmlStr := `
<?xml version="1.0" encoding="UTF-8"?>
<group>
  <name>ABCグループ</name>
  <company>
    <name>ABC株式会社</name>
    <website url="http://abc.com">ABC公式ウェブサイト</website>
  </company>
  <company>
    <name>ABCソリューション株式会社</name>
    <website url="http://abc.com/sol">ソリューション事業について</website>
  </company>
</group>
`
    data := new(Group)
    if err := xml.Unmarshal([]byte(xmlStr), data); err != nil {
        fmt.Println("XML Unmarshal error:", err)
        return
    }
    fmt.Println(data.Name)
    fmt.Println(data.Companies[0].Name)
    fmt.Println(data.Companies[1].Website.Name)
    fmt.Println(data.Companies[1].Website.URL)
}
```

<pre class="output">
ABCグループ
ABC株式会社
ソリューション事業について
http://abc.com/sol
</pre>

JSONを扱う時と同様、XMLタグ名と構造体メンバ変数の結び付けには変数タグを使用します。

JSONと違う点として、XMLタグにはタグ内のデータ以外に属性を持たせることができますが、これはattrオプションを指定することで取得できます。
先述のコードではWebsite構造体において属性の取得をおこなっているので、指定方法はそちらを参考にしてください。


## 生成処理

構造体のXML文字列表現を生成するには、Marshal関数を使用します。
高い可読性が必要な場合は、インデント付きの出力を行うMashalIndent関数を使用することもできます。

```go
package main

import (
    "encoding/xml"
    "fmt"
)

type Group struct {
    Name      string    `xml:"name"`
    Companies []Company `xml:"company"`
}

type Company struct {
    Name    string  `xml:"name"`
    Website Website `xml:"website"`
}

type Website struct {
    Name string `xml:",chardata"`
    URL  string `xml:"url,attr"`
}

func main() {
    head := Company{
        Name: "ABC株式会社",
        Website: Website{Name: "ABC公式ウェブサイト", URL: "http://abc.com"},
    }
    sol := Company{
        Name: "ABCソリューション株式会社",
        Website: Website{Name: "ソリューション事業について", URL: "http://abc.com/sol"},
    }
    data := new(Group)
    data.Name = "ABCグループ"
    data.Companies = []Company{head, sol}

    // インデントなし
    noIndent, err := xml.Marshal(data)
    if err != nil {
        fmt.Println("XML Marshal error:", err)
        return
    }
    fmt.Println(string(noIndent))

    fmt.Println("----------------")

    // インデントあり
    withIndent, err := xml.MarshalIndent(data, "", "    ")
    if err != nil {
        fmt.Println("XML Marshal error:", err)
        return
    }
    fmt.Println(string(withIndent))
}
```

<pre class="output">
&lt;Group&gt;&lt;name&gt;ABCグループ&lt;/name&gt;&lt;company&gt;&lt;name&gt;ABC株式会社&lt;/name&gt;&lt;website url=&quot;http://abc.com&quot;&gt;ABC公式ウェブサイト&lt;/website&gt;&lt;/company&gt;&lt;company&gt;&lt;name&gt;ABCソリューション株式会社&lt;/name&gt;&lt;website url=&quot;http://abc.com/sol&quot;&gt;ソリューション事業について&lt;/website&gt;&lt;/company&gt;&lt;/Group&gt;
----------------
&lt;Group&gt;
    &lt;name&gt;ABCグループ&lt;/name&gt;
    &lt;company&gt;
        &lt;name&gt;ABC株式会社&lt;/name&gt;
        &lt;website url=&quot;http://abc.com&quot;&gt;ABC公式ウェブサイト&lt;/website&gt;
    &lt;/company&gt;
    &lt;company&gt;
        &lt;name&gt;ABCソリューション株式会社&lt;/name&gt;
        &lt;website url=&quot;http://abc.com/sol&quot;&gt;ソリューション事業について&lt;/website&gt;
    &lt;/company&gt;
&lt;/Group&gt;
</pre>
