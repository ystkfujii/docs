# はじめに

* https://github.com/golang/go/wiki/CodeReviewComments
* 2022/3/17時点
* 逐語訳ではなく、意訳です。
* 📝

# Go Code Review Comments

* Goコードのレビュー時によくあるコメントを集めたものです。
** この資料を参照することで、説明を省くことができます。
** この資料はよくある間違い集ではなく、包括的なスタイルガイドではありません。
* link:https://go.dev/doc/effective_go[Effecrive Go]の補足資料としてご覧ください。
* テストに関するその他のコメントは link:https://github.com/golang/go/wiki/TestComments[Go Test Comments]で見つけることができます。
* このページを編集する前にissueを上げ、変更を話し合ってください。
** 編集合戦をする場ではありません。

## Gofmt

* link:https://pkg.go.dev/cmd/gofmt[gofmt]を使用すると、機械的にコードを整形することが可能です。
** link:https://pkg.go.dev/golang.org/x/tools/cmd/goimports[goimports]を使用すると、gofmtの機能に加え、必要に応じてimport文を追加/削除してくれます。
** 📝 vimの場合、link:https://github.com/mattn/vim-goimports[vim-goimports]のプラグインが便利
* 以降では、gofmtで機械的に判断できない点について記述します。

## Comment Sentences

* link:https://go.dev/doc/effective_go#commentary[Effective Go:Commentary]を参照してください。
* 宣言を文書化するコメントは冗長に見えても完全な文章であるべきです。
** link:https://pkg.go.dev/golang.org/x/tools/cmd/godoc[godoc]を使用した時にうまくフォーマットされます。
** コメントは宣言された名前で始まって、ピリオドで終わる必要があります。

....
// Request represents a request to run a command.
type Request struct { ...

// Encode writes the JSON encoding of req to w.
func Encode(w io.Writer, req *Request) { ...
....


## Contexts

* 情報を伝達する際に link:https://github.com/golang/go/blob/master/src/context/context.go[context.Context]が使用される場合があります。



* 基本的にContextは第一引数として指定する必要があります。

....
func F(ctx context.Context, /* other arguments */) {}
....

// TODO

* NGなこと
** 構造体のメンバにContextを追加すること
*** 各メソッドにctxパラメータを追加してください。
*** ただし、ライブラリ等のインターフェイスとシグネチャが一致しなければならないメソッドは例外です。
** カスタマイズしたContext typeを作成すること
*** または、関数のシグネチャにContext以外のインターフェイスを使用すること。

* 使い方
** Contextに追加する必要があるものであれば、Contextの値にデータを追加して伝搬させます。
** Contextはimmutableなので使いまわしても大丈夫です。

* 📝使用例 
** link:https://go.dev/play/p/fOHFnetmWrk[contextのset/get]
** link:https://github.com/kubernetes-sigs/controller-runtime/blob/release-0.11/pkg/log/log.go#L87-L102[controller-runtimeのlogger伝搬]
** link:https://go.dev/play/p/oiOnzLuFKfK[キャンセルシグナルの伝搬]

## Copying

* 他のパッケージから構造体をコピーする場合は注意をする必要があります。
** 予想外のエイリアシングが発生する場合があります。
* 例えば、link:https://github.com/golang/go/blob/master/src/bytes/buffer.go[bytes.Burffer]は[]byteスライスを持っています。
** Bufferをコピーすると、コピーのスライスが元の配列のエイリアスになり、その後のメソッド呼び出しに予想外の影響を与えます。
* 一般的にポインタを持っている
*** 📝たぶんこういうこと？ https://go.dev/play/p/6KUMLXpwgor


## Crypto Rand





