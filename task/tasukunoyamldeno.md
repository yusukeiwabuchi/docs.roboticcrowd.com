# タスクのYamlでの記述

### Yaml形式での記述

ここでは、Yaml形式でタスクがどのように記述されるかを説明します。基本的には、一般的なYamlの形式と同じものですが、 Robotic Crowdでは、[Digdag](https://www.digdag.io/)というOSSで拡張されたYamlを使っています。

例えば、[ロボットを動かしてみる](../robotic-crowdno/1demerurobotic-crowd.md) で紹介したYamlのはじめの2つのアクションで説明します。

```text
# Yahoo! Japan を開く
+open_browser_1:
  action>: OpenBrowser
  url: 'https://www.yahoo.co.jp/'

# 検索フィールドにミーアキャットを入力
+type_text_1:
  action>: TypeText
  browser: +open_browser_1
  selector: 'input#srchtxt'
  text: 'ミーアキャット'
  clearValue: false
```

2行目の、 `+open_brower_1` という部分が、タスク名になっています。`+` から始まるのが特徴的です。このタスク名の中に、パラメーターを渡していきます。

3行目の、`action>: OpenBrowser` という部分は、 `action>` というところで、このタスクが、アクションタスク（何らかのアウトプットを出すタスク）であることを表しています。 `OpenBrowser` というところは、ここで呼び出すアクションが、`OpenBrowser` というアクションであると定義しています。

4行目の、`url` というのは、アクションのパラメーターで、OpenBrowserアクションの場合は、urlというパラメーターが必須パラメーターとなっています。urlには、文字列でURLを設定します。

OpenBrowserというアクションの使い方をまとめてみます。

### OpenBrowser

#### 概要

ブラウザ（Chromium）を起動して、指定されたウェブサイトを開きます。アウトプットは、起動したブラウザのウェブソケットアドレスになります。

#### パラメーター

| 名前 | 型 | 概要 | 例 |
| :--- | :--- | :--- | :--- |
| url | 文字列 | 最初に開くウェブサイトのURL | https://yahoo.co.jp |

#### アウトプット

| タイプ | 型 | 概要 | 例 |
| :--- | :--- | :--- | :--- |
| Browser | 文字列 | 起動したブラウザのWebSocketアドレス | ws://127.0.0.1:52582/devtools/browser/be5e7b6e-ce64-4040-a7af-15ecc7b125f0 |

型というのは、Yaml形式上の型のことですが、アウトプットの説明のところの「タイプ」というものは、Robotic Crowd内で利用されている独自の分類で、**多くの場合タイプとは、特定の形式をした文字列のこと**です。上記の場合、ブラウザの接続先アドレスの形式をした文字列をBrowserタイプと呼んでいます。タイプは、ドラッグ＆ドロップで設定するヴィジュアルモードでのみ使われています。

OpenBrowserというアクションのアウトプットが、ブラウザの接続先アドレスとなっているので、このアドレスを再利用することで、同じブラウザを操作し続けることができるようになります。しかし、アウトプットの接続先アドレスは、非常に複雑な形をしておりとても使いこなせそうにないと思った方もいるのではないでしょうか。あとで説明しますが、アクションのアウトプットを再利用する方法はシンプルなので、この複雑な文字と格闘する必要はありません。

さて、7行目以降を見てみましょう。同じように、タスク名が定義され、TypeTextというアクションタスクが呼び出されるように定義されていることがわかります。`action>:` 以降の行は、呼び出すアクションによって異なるパラメーターとなります。TypeTextの場合は、browser, selector, text, clearValueが、必須パラメーターになっています。

### TypeText

#### 概要

ブラウザの指定の場所に文字を入力するアクション。出力は、OpenBrowserと同じくブラウザの接続先アドレス。

#### パラメーター

| 名前 | 型 | タイプ | 概要 | 例 |
| :--- | :--- | :--- | :--- | :--- |
| browser | 文字列 | Browser | ブラウザの接続先 | +open\_browser\_1 |
| selector | 文字列 | Text | 操作対象のCSSセレクタ | input\#srchtxt |
| text | 文字列 | Text | 入力する文字列 | ミーアキャット |
| clearValue | 真理値 | Boolean | すでに入力されている文字列を消去してから入力するかどうか。 | true |

#### アウトプット

| タイプ | 型 | 概要 | 例 |
| :--- | :--- | :--- | :--- |
| Browser | 文字列 | ブラウザの接続先 | ws://127.0.0.1:52582/devtools/browser/be5e7b6e-ce64-4040-a7af-15ecc7b125f0 |

browserというパラメーターは、ブラウザの接続先を設定しないといけないのですが、OpenBrowserがブラウザの接続先をアウトプットとして出すので、それを再利用するために、+open\_browser\_1 という文字列を設定すれば良いということになります。このように、アクションタスクは、1つのアウトプットを持つように設計されているため、アウトプットを再利用するには、タスク名を文字列で入力すればそのアウトプットを取得してくるようになっています。アクションタスクの場合、+から始まる文字列は、自動的にタスクのアウトプットを取得するようになっているので注意してください。

上記の例の設定だと、`input#srchtxt` という場所が指しているのは、検索ワードを入力するフィールドですので、そこに、ミーアキャットと入力しています。もとから値が入っている場合は、それを消去するような設定になっています。
