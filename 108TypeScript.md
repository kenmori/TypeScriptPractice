<!--more-->

#【TypeScript】TypeSript中級者になる為に知っておくと良い108個のこと

[author](https:kenjimorita.jp/aboutme)

徹底的にやるTypeSript108個のこと。
仕事でもっと使いこなしたい方と自分に向けた記事

[TypeScript練習問題集](https://gist.github.com/kenmori/8cea4b82dd12ad31f565721c9c456662)


<img src="https://kenjimorita.jp/wp-content/uploads/2020/03/typescript.png" alt="" width="300"  class="size-full wp-image-21406" />



108個炙り出すことをした

もちろん、自分が知らないのはこれだけしかないのではなく、

今回はTSへの理解を深めるスタートという位置付けになりました

まとめきれてないのでご覧いただくにはガチャガチャしていますが

## 1. Excess Property Check

```ts
interface SquareConfig {
color?: string;
width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
return {color: "red", area: 1}
}


let mySquare = createSquare({ colour: "red", width: 100 }); //これだとエラーだが

let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig); //これでもいける


// config.opacityを関数内で参照する時エラー
```

この場合 `colour` はSquareConfigに含まれていないからerror
余分にあるpropertyとなる
余分なpropertyを許可する場合

関数への引数は余剰は認めるが使う時エラー

[playground](https://www.typescriptlang.org/play/?ssl=11&ssc=1&pln=12&pc=1#code/JYOwLgpgTgZghgYwgAgMoEcCucoQMID2IMwA5sgN4CwAUMvcggQDYFQD8AXMgM5hShSAbloNkAd2AATMAAsuyEJgC2AI2gi6DANoAHKAV0A5OMojc+AkKQC63OCACemgL61aMTCARhgRRrhwkBjYuAAUTMRk3CE4+EQkpACU3BSMLGwW-IJCyHFw3Epq0MgulKIMkYkAdIaIwGCOFfS4YJhQIJTprFDcAES4Un0ANHmB3ACMpc3ItG407jTMEGDIyo6xuMgAvAEQQRCbEGFpkjKykwAMl6N1CA2O3JfVAKyleTxoWHGEUclCQA)

## 2. 1つのkeyしか受け付けない型

[playgrounnd](https://www.typescriptlang.org/play/#code/PTAEDECcHsFtQBYBckAcDOAuE6kEMBjAa2gDcBTSAMwBtoB3AOgLmD2AFYAGAZgHYOAJgAcANgCwAKCQBPVOVABVAHYBLaMoAq0AJLKkldOQJJ1ygDyKAfKAC8oABSLQ5AB4HlAE3Sg8ymaAA-I5EmEoAlHY2pNCqnqBhyuQUkJFuHt6ODqGgqspUlKA6kbbRsZ6RwToJoEkpANxSUiAQMLBhyGhYOPjEZJS0DMys7Bw8AJwcHAAsPACMUrLyRegqZuaaNvYA2poAui7u5F4+22sa2noGkEYm65sHwVR4NEY1SJAAruSNks1gAAlKAp6AoWLByABCRZyBQAZTyAHMaOQANLkGQbLYrc4WIgY6BUUCbQ4ZHwfb5BWrJQphADeAF9ScdMpoqXVacTfv8lOg8IjyJgpFRPso7hpQFQNsyTqAAErGaCQTzmXCQJEAGl8-isVgc0AARgArMII5TItEYrGROlSUD20AsZToaAoxh0REOOmgQ1G0AM8K-BlNSRUL0B+qgFqUGA3RDA6Ghr2gVxhDj+wPC5Op0AcLUBMKiDOR6OQWM+BAJoA)

[stackoverflow](https://stackoverflow.com/a/50375286)

```ts
type UnionToIntersection<U> = (U extends any ? (k: U) => void : never) extends ((k: infer I) => void) ? I : never;

// Uはなんでもいい。Uが渡されていればUを引数に取るFunction型を返し、次の処理、UがIにキャプチャされてI(U)となり、Uがtypeになる


var fff: UnionToIntersection<{ a: string }> = { a: "1" }
```

次のここ
[stackoverflow](https://stackoverflow.com/a/53955431])

```ts
type IsUnion<T> = [T] extends [UnionToIntersection<T>] ? false : true;

var fff: IsUnion<{a: "a"}> = false
// error
```

## 3. inferがunionTypeを引数にとれない??

[playground](https://www.typescriptlang.org/play/?ssl=3&ssc=44&pln=2&pc=1#code/C4TwDgpgBAqgdgSwPZwCpIJJ2BATgZwgGNhk4AeGAPigF4oAKGKCADxzgBN8oBDOEFAD8jANYAuWAEo6NAG5IEnKJLgQ5eGWw7dGDCVARwAZnigYZteYs4yRGFVDUbcAbgBQ7gDYRgfSfBk6Fg4BMSkKOQARLxRUAA+UFEARlE09DFRQA)
このこと

値はunionTypeにできないから。どちらかを実行時に指定しなくてはいけないから?

inferで"a"は通って、"b"はneverが変わる意味がわからない
-> わかった
下の4番の決まり事のせい。
inferが最初の値 "a"型にしてしまうので次に型計算?されるbを受け付けなくなる

let a: UnionToIntersection<"a" | "a"> = "a"

なら行ける。
つまりaは"a"しか受け付けないし、1つのリテラル型しか返さない

## 4. ジェネリクスの型は合成できない

[stackoverflow](https://stackoverflow.com/questions/39905523/why-isnt-the-type-argument-inferred-as-a-union-type)

```ts
function compare<T>(x: T, y: T): number {
return 2
}
// Could infer T: string | number here... but that'd be bad
compare('oops', 42);
```

これはerror。先に型が決まってしまう
TypeScript in general will not synthesize a union type during generic inference. The reason, in simplified terms, is that it's not desirable to do inference like this:
推論中は共用型をつくらない

## 5. なぜ関数の型を使っているのか

```ts
type UnionToIntersection<U> = (U extends any ? (k: U) => void : never) extends ((k: infer I) => void) ? I : never;
```
関数の引数は反変で、関数のUnionTypeはその引数をintersectionにする

## 6. 共変

https://kenjimorita.jp/typescript-subtype-vs-supertype/

関数Aが関数Bに代入できる時
アリティがAの方が少なく、その引数がAの方がスーパータイプで、Bの戻り値の方がスーパータイプ
の場合

## 7. 引数に割り当てられたtypeが自動的にgenericsの Tに割り当てる

タイミングについてわからなかった
「TypeScriptは、関数の引数から型を自動的に取得してTに割り当てます。」
なるほど。
TypeScript will then automatically grab the type from the function arguments and assign it to T.
https://medium.com/better-programming/typescript-generic-objects-1eecc13dca93

省略できるよ
https://medium.com/@rossbulat/typescript-generics-explained-15c6493b510f

## 8 extendsを使いたいときは

もちろん、より制限したい時だけど、ちがう感じで言っていないか
開発者が欲しい「最低限の形状」をextendsのスーパータイプにする

This is where we’re going to utilize TypeScripts’ keyword extends. extends will make sure our generic type is at least a given shape. For mapAnyProductData,

https://medium.com/better-programming/typescript-generic-objects-1eecc13dca93

なるほどそうすることで元データの変更なしで型を変更できる...

## 9. angled brackets アングルブラケット

`<>`
このこと

## 10 return typeがgenericsである場合、angled bracketsの省略はできず明示しなければならない

## 11. 型変数、型パラメータ、ジェネリックパラメータ。呼び名が多いけど..

TypeScriptのドキュメントは「Type変数」

## 12. class をインスタンスする時のangled typeは必須

```ts
let myObj = new ClassName<T>{}
```

## 13 推論の限界

https://learning.oreilly.com/library/view/effective-typescript/9781492053736/ch01.html#ch-intro

```ts
type State {
name: string
capital: string
}
```

型注釈をつけることでプロパティ名の間違えを明確にしてくれる
// Property 'capital' does not exist on type
                 //         '{ name: string; capitol: string; }'.
                 //         Did you mean 'capitol'?

この提案自体のプロパティ名が違う場合に
実際はcapitolではなくcapitalが正しいがここではそれまでは指摘してくれない

## 14 TypeScript Compiler APIとは

https://katashin.info/2018/02/24/221

## 15 なぜこれがエラーになるのかを知りたかった

```ts
function test<T extends { [key: string]: string }>(map: T) {
    const key: keyof T = "hello"
    map[key] = "hi there"
}
```

[TypeScript/issues](https://github.com/microsoft/TypeScript/issues/34591)

ジェネリクスで

## 16 TypeScirpt 3.81のインデックスシグネチャの動き

WIP

## 17. TypeScript 3.9の変更点は?

https://devblogs.microsoft.com/typescript/announcing-typescript-3-9-beta/

if conditionの中でfunction callがされていない場合のerrorに続いて、三項演算の中におけるそれもサポートした

## 18 unionTypeのindex signatureが絡んできた時の理解

WIP

## 19 どのようにgenelicsはcompilerで解決されるか

WIP

## 20. Tを同じ型として扱うにはどうしたらいいか

[stackoverflow](https://stackoverflow.com/questions/51248339/typescript-same-generic-type-as-resolved-type-of-previous-parameter)

```ts
function add<T extends (number | string)>(a: T, b: T): T {
if (typeof a === 'string') {
return a + b;
} else if (typeof a === 'number') {
return a + b;
}
}
```

関数の中のgeneric parameterの型は絞り込むことはできない。なのでaをテストした時、bをコンパイラに伝えなくてはならない。さらに返値の方もコンパイラに伝えなくてはならない

```ts
function add<T extends (number | string)>(a: T, b: T): T {
if (typeof a === 'string' && typeof b === "string") {
return a + b as T
} else if (typeof a === 'number' && typeof b === "number") {
return a + b as T
}
throw Error("not Support")
}
```

## 21. 要素ないを取得してunionTypeを作っている説明が理解し切れてなかった

[TypeScript/issues](https://github.com/microsoft/TypeScript/issues/28046)
ここの答えのReadonlyArryのところ。それじゃなくちゃいけない理由など
理解した
[playground](https://www.typescriptlang.org/play/index.html?ssl=8&ssc=1&pln=9&pc=1#code/GYVwdgxgLglg9mABAZygJxmA5gGRlAUzQEMAbZAHgBVECAPQsAE2RXUywD4AKAOn+JosyAFyIqAbQC6ASjGSpiAN6I0BKCDRJBwgNyIAvgFgAUFACeABwKIA8mAK3g1WgwLNW4ANZg4AdzBpTkQAXnFXRhZEbkxgIkQAJRlpRAB+RMQxBwA3Il1TUwB6QsQIBFREbLIQAlYwiQAiAEEGgBpEBoAhBsVUDGxpQGsGQCsGQGMGQDMGQHUGQD8GQDEGIpKysAqq0hq6xEaW9q6exGJWJdQF8QALGwtrRAByNWImBFJzTea2ju6pa8QYVluCe8e5i+xGYpRBvigiAARjYDsgYFgHExEFA4CjzogALYgKDEKGkC5WGzXby+ALSL53B5gJ4oomAewZfGAALRU5mAunWQaAJyVAFEMgGV9caASIZADEMw0AIgwnQDKDCLAEkMgEYowCaDIN6fZHMBZQl-kx7E9BorANEMUsAFgwnS42NVOFz0SKsLUAmnmJpoEjmCik-xgTjBMI0G3uKL26lPZ2uiixeIAUQJmPcUCoROC6WjBFjYHjRMyiByeROxVK5Uhq3WoRe23eewOBeWUBORyL1Vqpb6HDwhBI5G41ya13a1061xk+RM+fNiAAYnA0WFLc5zXBgJVG8hOMOzZnZ9a3B5EtrAaHiG6PQFvaW-dvA3vHQe3RG0IgU2mM9Ykw+Y3GE1csgRcmhh-WlzWJswhbbA2yIMhkG4V4dm6GRTDHSdpzsBwrXnRdi1qVcgA)

## 21. type guards比較はinも便利

```ts
type NotHumman = Animal | Bird

const b = (b: NotHumman) => {
    if ("run" in b) {
        b.run
    } else {
        b.fly
    }
}
```

https://gist.github.com/kenmori/8cea4b82dd12ad31f565721c9c456662
問39を修正

## 22. 配列がもつ要素にアクセスする方法で見たことなかったこれ

```ts
const type = <const>[
'room',
'room_with_gifter',
'user_send'
];

type Activity = {
id?: string;
type: typeof type[number];
}
```

## 23. インデックスシグネチャへのプロパティアクセス方法のいろいろ

[stackoverflow](https://stackoverflow.com/questions/32968332/how-do-i-prevent-the-error-index-signature-of-object-type-implicitly-has-an-an)

## 24. ThisParameterTypeとOmitThisParameterType

[stackoverflow](https://www.typescriptlang.org/docs/handbook/utility-types.html#thisparametertype)


## 25 inferを複数回使うところ

[stackoverflow](https://artsy.github.io/blog/2018/11/21/conditional-types-in-typescript/)

## 26. tsserverのこと

[stackoverflow](https://github.com/microsoft/TypeScript/wiki/Standalone-Server-%28tsserver%29)

```ts
node_moduels/typescript/lib/tsserver.js
```

## 27: unknown[]

任意の型の配列またはタプル
また

```ts
let a: any
let b: unknown
```

未定義という意味ではanyよりunknownを使った方が型安全

## 28 Genericsがbindするタイミング

WIP

## 29 distributive conditional type

ということば。
32のこと

## 30 type-festというutilityライブラリの実装を見ていると
とりあえずunknownにしといて型変数だけ作り、関数の引数の型にして、次のconditional中でその関数の引数の方を抽出するテクニックがあるのだな
と感じる

https://github.com/sindresorhus/type-fest/blob/master/source/union-to-intersection.d.ts#L48

## 31 type-driven-developmentとは

型を先に作って詳細を後から作ること

## 32 これがわかりやすい

[stackoverflow](https://stackoverflow.com/questions/55382306/typescript-distributive-conditional-types)

下は理解不足だった頃

nakid type と non nakid type
型パラメータが別の型(配列、タプル、関数、プロミス、その他のジェネリック型)にラップされずにラップされずに存在すること
条件付きの型(conditional type)の際に、nakid typeはユニオンに分散され、それぞれ評価される

[stackoverflow](https://stackoverflow.com/questions/51651499/typescript-what-is-a-naked-type-parameter)
nakid パラメータtypeのことをチェックするconditional-typeをdistributive conditional typeという
https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#distributive-conditional-types
distributive conditional typeのTはここの構成要素を差す

## 33 共変と反変

関数の引数のみ反変

関数の変性
A <:B になるときは
関数Aが関数Bとそれより低いアリティをもち次の条件を満たす場合AはBのサブタイプ(AはBに割り当て可能)

- Aのthis型が指定されていない。または「Aのthis型 >: Bのthis型」である
- 対応するそれぞれの引数
について Aのパラメータの型 >: Bのパラメータの型」である
- Aの戻り値の型 <: Bの戻り値の型

## 34 割り当て可能なという意味

assignability
型Bが要求しているところで別の型Aを使用できるかどうかについてのTypeScriptのルール
A <;B なら使うことができる
列挙型以外

## 35 excess property checkingの深い理解

オブジェクトリテラルに対して、targetの型にないプロパティをチェックすること

これがあるからプロパティの誤字をエラー起こしてくれるが
「フレッシュ(Freshness)なオブジェクトリテラル」を型アサーションや、一回別の変数に割り当てると
TypeScirptは過剰なプロパティチェックをしなくなる
3.8の確認

過剰なプロパティチェックはオブジェクトリテラルを直接渡した場合、判定されるがなぜ代入するとそれが「撤退する」のかとsubtypeになっていればそれでもいいのではないかと思っていた。
なぜこんかことをしているか
-> 可読性の観点からだという
`some({a: "a", b: "b"})`
上のbが余分だとして、
直接リテラルを渡した場合、
このように書くと、開発者はbを渡しているので、
someはbを求めていて、引数として必要で、関数の中で使われる
と思うが、
実際はbは求められておらず、
使われてもいない
これを注意喚起してくれている
 -> リテラルという誰もが誤解する形で渡している場合、
エラーにしている
https://basarat.gitbook.io/typescript/type-system/freshness#freshness

`object literal may only specify known properties`

明示的に過剰なプロパティチェックを許可するという意味で、インデックスシグネチャで明示的にすることができる <- なるほどと思った
これが、UnionTypeで、過剰でかつ、プロパティ値の型が違う
場合、今回3.8ではチェックする
ようになった

## 36 APIの戻り値ではルックアップ型が便利

- APIの型を部分的に型を切り出して名前つけて、みたいなのをやらなくて済む
- 自動生成される
ような型はなおさら

APIResponse["user"]["friendList"][number]
こうすると配列の型がとれる。number。
タプルの場合は[0]とか

## 37 Object vs object vs { }

- strictNullChecksが無効なら
それぞれに null、undefinedが代入できる
有効ならエラー

- Object
全ての値が割り当て可能

- {}
empty Object
Objectと同じ

object
non-primitiveな値が代入可能
boolean、number、string、symbolはダメ

## 38 freshnessを調べたら

フレッシュネスバーガーが出てきた。
TypeScriptでも出てくる。
フレッシュネスバーガーがTypeScriptでサイトを作り、それをメインページで報告するとさらに上にきそう。「TSバーガー」でも作って

## 39 Exact Typeというのが議論されていること

[TypeScript/issues](https://github.com/microsoft/TypeScript/issues/12936)

- 式から新しい型を作成している時、余分な値が入ってくるのを避けるため。新しいプロパティが入ってくるのを避けるため

```ts
type User = { username: string, email: string }
const user3: User = Object.assign({ username: ' x ' }, { email: ' y ', foo: ' z ' })
const user4: User = { ...{ username: ' x ' }, ...{ email: ' y ', foo: ' z ' }}
```

これは現在エラーにならない
これをエラーにしたいという議論

## 40. リリースされた3.9.0 betaのこと

- Promise.allとPromise.raceの型でnullとundefinedが入った場合の推論を改善した
- awaited Operater(Promiseのawaite時の型)においてはまだ機能設計をする必要があるのでブランチを切った(testだけ入れた)
   https://github.com/KDE/syntax-highlighting/commit/1f1f03198aa761500ce58b1525f15b7b1918e393#diff-1114302f2932621cc9fd427ef812fae7R283
- material-uiやstyled-componentsなどのパッケージを使った時のコンパイルスピードが改善された (unionType,intersection, conditional type, mappded typeにおける)
- //@ts-expect-error
- 条件式に書かれている関数が呼び出されない場合、エラーを吐くようになった。if文のみ。三項演算の場合もいけるようになった
- エディタの改善 -> CommonJSモジュールを使用したJavaScriptファイルを自動的にimportされるようになった。以前はimport * as Hoge fromなどのESmoduleで常にしていたが、モジュールタイプが違う場合も自動で検出するようになった

など
https://devblogs.microsoft.com/typescript/announcing-typescript-3-9-beta/

## 41. 数値型インデックスの戻り値の型は文字列型のインデックスシグネチャの戻り値型のサブクラスである必要がある

何を言っているかわかった
[playground](https://www.typescriptlang.org/play/index.html#code/C4TwDgpgBAggdgSwLYEMA2UC8UDeBYAKCmKjhSQgC4oBnYAJwTgHMBuQgX0NEigBEA9syy5CJKACN6ECABNqdRi3YEOUAGSxEqNIUJNgEegDMUAY2gA5AcADyAaxQhRREgG0AHgoZNmAXWpBNjF3L1IAVyQJIwCtZHQVDiA)
こういうことか
number型のインデックスシグネチャはJSでは文字列に変換されるためその安全性を考慮して
string型のプロパティが返す型。例の場合Dogのサブタイプでなければならない理解
つまり、
[playground](https://www.typescriptlang.org/play/index.html?ssl=17&ssc=1&pln=18&pc=1#code/C4TwDgpgBAggdgSwLYEMA2UC8UDeBYAKCmKjhSQgC4oBnYAJwTgHMBuQgX0NEigBEA9syy5CJKACN6ECABNqdRi3YEOUAGSxEqNIW7hoAOQHAA8gGsUIEfiIkA2gA8FDJswC61QczEPnpAFckCQh6Ty1kdE49AgB6eKhAZQZAGIZAKwZASIZABwYBc31eCysbX2InFyUPMrdiqFLA4NDwgCIAMxRWxuiCGPjYqEAghkB1BkB5BkB7BkTAMQY86AKQACYiuxL-RTdwlZYoAB8oFraUDsWa-zggkLDqXfbOwgTAfQZAAwYJ1MBohkALBhTAcwY+wBgVQA0GQHhDQDOKlMoDMAMwLcS1dYVOpnLY7VrtFRcLoEABuKHoUBQNjUKBooMsIDBMRQ9gAjO4RJdZO0oPFAAAMJ3q9EA1gyATQY3nDQgjLvtAEkMgCEbQCBDMlnjkgA)

## 42. ネストされたindexシグネチャに注意の意味

```ts
type B = {
name: string
}

// ネストされた同じ名前のプロパティにたいして、これだと検知できない
type A = {
name?: string
[ind: string]: string | B | undefined
}
// errorになって欲しい
const a: A = {
naame: "fafa"
}
```

[playground](https://www.typescriptlang.org/play/index.html?ssl=20&ssc=1&pln=21&pc=1#code/C4TwDgpgBAQlC8UDeBYAUFAdgQwLYQC4oBnYAJwEtMBzdAX3XQHompBZhkE6GQCYZBVBkBiGQPoMgGBVAGgyBYFUCySoDsGQOsMgW4ZAiwyAxhkDFDIGsGAYBEGQOoMgMwZAgAyBlBj6ADBkAWDIBxLQKfugcwZAsgyArBk3pQkKAEEEydFjwQA-ESklDRQPgDaVAAmQeRU1AC6sSHUUAA+sOlQAK6YURAAZlQQUfTMrBBkZAD2ZGoOgMYMuoBM1trOaADG1ZikUNhEnoioGL5+RABEBdhT42VojGgsUIC6DHyASQyAmgyA0QwOgJEMLuDQ7gBMXsNQvviBJHGhPlgQpNfnF1CRecnxSTcpWXAZuXyRUwJXuDDQ4KW1nsW3QXR6wD6xwGpyG9xwuHwEymMzmQA)

## 43. interfaceにあるRecord`<string, string[]>`はtypeで拡張するとよさそう

[playground](https://www.typescriptlang.org/play/?ssl=10&ssc=1&pln=11&pc=1#code/C4TwDgpgBAShDGB7ATgEwOoEtgAsCSAIlALywIqoA8AzsMpgHYDmANFLfcwNoC6AfFABkUAN4BYAFBQomVAC52dRk0kBfSZIA2EYFAAeCuEjRZchEqNVQAhtRsMQGiXoB0siwHJrAI3genrj7wFlxe1KgAZh48TkA)

{ } この場合のanyは仕方ないのか??

## 44

[TypeScript/issues](https://github.com/microsoft/TypeScript/issues/35981)

このこと

## 45 Anders Hejlsberg

https://en.wikipedia.org/wiki/Anders_Hejlsberg

TypeScriptのコアメンバー

## 46 コンパイルのこと

TSコントリビューターになるまでの軌跡
https://dev.to/remojansen/learn-how-to-contribute-to-the-typescript-compiler-on-github-through-a-real-world-example-4df0

TypeScriptの深いところを学んだ人がこれいいよっていうリンクが貼られている記事
Douglas Crockfordという人の
parserのことASTのこと、それを生成するところの説明(ビデオ)
https://www.youtube.com/watch?v=Nlqv6NtBXcA


どのようにcompilerが動くか (Vincent Ogloblinsky)
https://www.youtube.com/watch?v=WkJagE7b5U0

有名なAndersHejlsbergさんへのインタビュービデオ
https://www.youtube.com/watch?v=jBddlEWNsps

Grammatically Rooting Oneself With Parse Trees
https://res.cloudinary.com/practicaldev/image/fetch/s--x7QWZlJP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyKjaulmzLsRxN4JBvy5Q2w.jpeg


TypeScript アーキテクチャの概要
https://github.com/Microsoft/TypeScript/wiki/Architectural-Overview


Compiler Internals
https://github.com/Microsoft/TypeScript/wiki/Compiler-Internals

一度これしたほうがいいよとのこと。
ソースコードからUMLdiagrams を生成する
https://github.com/remojansen/TsUML

## 47. エラー処理の種類。使い分け
null
- cons 簡単
- pros 具体的なエラーを返せない。「なんらかの理由で」。その後nullチェックをする必要があり、操作のネストや連鎖を行いたい時に冗長になる

例外をスロー
- try-catchの中で特定の失敗モードを処理できるようになる。失敗に関するデータを得られる
- エラーの種類をサブタスク化して種類ごとにインスタンスを投げることでなぜ失敗したかがわかる。ログも解析できる
- 名前があるのでドキュメントもかける。

例外を返す
- 関数の返値型にユニオンタイプ。関数利用者にどんなエラーがあるか伝わる。再スローすることを強制できる
- 欠点はすぐにネストになること

Option型
- Haskell, Ocaml、Scala, Rustに由来するもの
- 値ではなくコンテナ(値が入っている)を返す
- エラーが発生した理由を利用者に伝えない欠点
-> ここ理解しようとすると深い

## 48 シグネチャの名前
https://www.bookstack.cn/read/ts-spec/spilt.9.spilt.3.spec.md#5.2.2

{
 func1(num: number): number // メソッドシグネチャ
 func2: (num: number) => number //Function シグネチャ
 func3 : { (num: number ): number } // オブジェクトタイプリテラル
}
これらは全て同じ

## 49. 違いはあるのか

[playground](https://www.typescriptlang.org/play/index.html?ssl=10&ssc=1&pln=10&pc=50#code/JYOwLgpgTgZghgYwgAgLIE8CS5rycgbwFgAoZcgeguUGaGQH4ZBrhkHaGQBoZBZhkEGGQY4ZTzkrkgKoZAawyAOhkDlDIHqGQBMMgfoZAJQyB1hkB2DIFDFQC4KgcwZA-vKB4hkAxDIGiGADTJARamAHU0AWEYD-nU5cDF2oAAo0xDAIAdF958AFAA8ALhAAVwBbACNoU3Rg8KioAEo4yOgAbmRSAF9SUgA3OChkMPQAeRAUQLQsHFhEFABeZADjdETkBoA+ZH9kAGpkdDSfARgQkAQwYAB7EEBo9XzC4vQAFQB3aeQqgJSEmN3odq7kUNSippa2ju7egaHMklHxyZmQZDB0AAcUYABnZBCvwgABNkKBkGMJlNZu8vigADbASBQODwoA)
全く同じ

[stackoverflow](https://stackoverflow.com/questions/32043487/difference-between-call-signature-and-function-type)

 なぜ書き方に違いがあるか

## 50. なぜ `type K = keyof { } // never`なのか

- keyがないため

## 51. 互換性のない型アサーションでエラー

WIP

## 51

filterでは型を絞り込むことはできない。ワンライナーの書き方

```ts
type User = { name: string }
type WithIdUser = { name: string, id: number }
type WithIdOreNotUser = WithIdUser | User
type WithIdOreNotUserList = WithIdOreNotUser[]
const users = [{ name: "a", id: 1 }, { name: "b" }]

users.filter((e) => "id" in e ) // error
```

[playground](https://www.typescriptlang.org/play/index.html#code/C4TwDgpgBAqgzhATlAvFA3lAdgQwLYQBcUcwiAllgOZQC+AsAFCiRQDq5wAFgJIAm8JKgzZ8REmUpUANFHJ9iWAK54ARkIbNw0Dt34B5RBAByAe2CDkaXbwEJkAH1j2mLHZ1uGT5ywBlypMI2BkZmFvYA2gC6TADGpliBSvZwwhGYuATEAEQ42bLyxACMdLIZYjmq2XQxjHEJgTjCyUhwAHQAZuQANsBIABT9EACUqAB8UNny1ZRQI1AA9AvsHiHe4Uj+pEz1icBQqs0pnT19iIPiwXxeYZZQw8TQASt6dkIoE1N8M1hz9214HBgIbjOZteSjJbYFTqRDRJhQ0zINxQKhKHCIPhQJhAA)

## 52 インデックスシグネチャで余分なプロパティを許容するにはundefinedも型に入れる。その後 unionTypeになる

[stackoverflow](https://stackoverflow.com/questions/47037807/how-does-index-signature-in-typescript-works)

[playground](https://www.typescriptlang.org/play/index.html#code/MYewdgzgLgBAZiEAuGBvARgQwE4vYgGwFNMwAaGLALz0JLAF8YBeNAWACgZKcUpsArkQqdu1FHEwEIwmKJgBHAQA8JUmTAD0mmEWzYQ2CkWX9MMAA4GLeqAE8YYELBM3gUIgBNODANycEEAA6LGx-DkCQzCpwyKVlXy0dPQMjRxAYAEtPEhgAdwALTFh4rIgYCAELCxAZTxgoDPQiAMQgpRVE7V19QwonLJzzQuLFARUyiqqauoamlo5ObWWV1bX11c5OUEhYQIAmFAxeSjpSCnFTkGJzmABtAGsUaGxMsABzAF1aa-oYAB8YAIwDk4G8vExWKgkjBMJ56sDQeDvFweLgGoJZPJLpJpFjUfE1HiYQMUoYfLFEPsomEYfhfqRWiBqdQujp6TcwEzqfE2VdOQCgSCiGCwF5ue1xgk6WcwILESLkVtFhwNutmgQQHlJhYCJg7O8DIi1StlfYbDAAGKCTKwVgAIgAgtViPbBfaAPLYUjvIj2s12C2OljsVHcO5vHKqCr8N5fFDWgS2nzKnbQWEoYNQ+TcTAoJ2Owtu7oAUV62BTKrTsHQmZD0LzMCdLr9MAY23A6fQ3drWbQjftJaIRBL9vbHGrlHQIfQd3tkgXmEX9s+JOcPVSMEAGtqACnVAOoMgCsGQAiDJxuzCFaLPIBzBkA9gzHju7GD1VjoIKYd-mbqJ5MqzjmohWjaUAAEIhs2uqtoCnreh8foBkG+z1jm9yRiYzyxh83yAUmIGVg+XaHI6iFQou+bOhBY74TWADMSBEfWpFNheyJjjCgDKDGxV6ABUMgCXDIAPwyANYMB6AMYMgBmDIA8gyADEMgDRDMJgC+DCeHDoNRQSLsqGwgA8Jp-oGAGOtRSFhihwrRi8cZYd+IHysKopeDCcIIjZSocEwgLQox9rMWKniUb+qqrIY6w6RaADSRAOA6mBulB6D+hw-4wAAwoZ3DhqhyhZHKYV2J8AD8CZAXhE6drA8IpalA7ka6RWTvCz5Pp4b5UQ1njEQ1c7Lqu3RktgQA)

## 53. Object.keyで推論されないことがある

const config: {name: string, age: number} = { name: "mori", age: 90 }
Object.keys(config).map(e => e)

これは仕様によるため
[TypeScript/issues](https://github.com/Microsoft/TypeScript/issues/12870#issuecomment-266637861)

[TypeScript/pullRequest](https://github.com/Microsoft/TypeScript/pull/12253#issuecomment-263132208)
JSは任意のプロパティを追加できる。keyofは同じものを返さない可能性があるから

アサーションを使って解決する
[playground](https://www.typescriptlang.org/play?#code/MYewdgzgLgBAbjAvDA3gKBpmBDAXDARgBoMsAjfAJjQF800B5MgKwFNgoA6Aa1YE8IACjgBKTgCdWAEwCuwVoMHZgwGQFsZAG2xQQ4ojFXjJYKCKQA+VKUzLVG7bvGcADjIgALYQG0jJqAC6IgDcNjCSUDLiYDgq6lo6eqE0Bt5BoWgA9Nk5uTm6eXn0oJCwcJRI1lg4+MRhFDDUNBlw2OLw2JoyrBCVgkxsHDz8QuXm2L0AgsbYfAA8vHwgAGYwUHwurCvwlBZikrLyinbxjnoGfqym5ohW6NUnDonObp7ClL5R-ulhEVExjwSTmSqQCOF6gnWm22cG8i22UK2qzgQTSISAA)

## 54. WIP

[TypeScript/issues](https://github.com/microsoft/TypeScript/issues/30805)

## 55. unknownとunknownをintersection したらunknownになるとかここらへん

https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#new-unknown-top-type

```ts
type Test<T> = unknown extends T ? true : false;
const b:Test<boolean> = false // とにかくfalseになる
```

[playground](https://www.typescriptlang.org/play/index.html#code/C4TwDgpgBAKhDOwA8MB8UC8UCuA7A1rgPYDuuUEAHsBLgCbyxQD8UwATttAFxQBmAQwA28CAG4AsACgAxkVyIoAI15xESJUSJCIA3OiyCR0APQmogCwZA1gyBpBkDyDEdFXAVgyBohmmhIsACwAGTFD4ECBEfFB6IGJQUGZQiOwAlrgA5lAAPlC42AC2ShDs6XEgudoe4NAw3gCMAUEhYXiEpLhRMea4EABu+UA)

## 56 初めて見た。keyof を引数の型でつかう

```ts
type User = {
status: {
login: boolean,
point: number
}
}

const getKeys = (keys: keyof User["status"]) => {
return keys
}
const keys = getKeys("login") // "login" | "point"
```

## 57

variableに対してのreadonlyはas const
propertyに対してはreadonly

## 58。オプショナルの賢い使い方

なるほど、ベースのを変えるのではなく、第二引数にそれ用のオプショナルの型を受け取りmergeする
[playground](https://www.typescriptlang.org/play/index.html#code/LAKAlgdgLgpgTgMwIYGMYAIAK8DOB7CdAb1HXQiQFsYAudHKOSAcwG5T0lnbyBXSgEbx2IAL6hQUAJ4AHDJiRwoYJABtscfIQC8WRcrUAeDVoB8I0Al4QUygul4yAJklgmCACjmaCAFTwAqs6uPO4QADScUIxgArywOHQKSirquAQAlMQccDBQvHCEROgAdGXo3lr+QS6wkWUlrjFxCeiiIuIgoKp5FekQSf3ouiQgZBTUdADkAFJ4ABaEACJ4MFPhHFw8AByg7RJdIAD0R+hQ82A46ADueHAA1ldIVzAAHnK2ME6gKAQMUYwcABGJL6VJhYbZMacbh0ADMuzEFhAlXsukctRgYS8-UiTU0QIyrCA)

## 59 enumとconst enum どちらが使いやすいか

書き出されるコード

```ts
enum A {
a = 1
}

A.a

const enum B {
a = 1
}

B.a

"use strict";
/// A.a
var A;

(function (A) {

A[A["a"] = 1] = "a";

})(A || (A = {}));

A.a;

/// B.a
1 /* a */; // enumの定義すら書き出されない
```

## 60

[TypeScript/issues](https://github.com/microsoft/TypeScript/issues/24300)
intersection Typeは全てを満たさないといけないが、indexsignatureを使っている場合、過剰なプロパティチェックが走り、エラーにする。回避方法

```ts
interface I {
[key: string]: string;
}

interface C {
foo?: boolean;
bar?: number;
}

type T = I & C;

// this does not work:
let t: T = {
asdf: 'foo',
foo: true,
bar: 3,
};
// this does work:
let tt: T = {};
tt.asdf = 'foo';
tt.foo = true;
tt.bar = 3;
```

https://qiita.com/kuy/items/f342fbd1737f557cf42a

[playground](https://www.typescriptlang.org/play/?ssl=19&ssc=1&pln=24&pc=1#code/JYOwLgpgTgZghgYwgAgJLIN4FgBQz-IDaA1hAJ4BcyAzmFKAOYC6Vt9IDA3LgL666hIsRCgDCmXAWQwA9jID8VAEZyANhDghueAkrhRFyEAFcAtkuja+OXGDIAHFABVkAXjTIAZMlHbcAen9kMAALYGpkABMZCAiQGTBkAHcZKGIKXHVEsCoXd2wdfDhqSJgqAHJZGXKAGkkCKqo6Ywg6wuQ9KCoAZjaeTmR+HEDgsIjo2OTU9MyIbJzkPMx+2zAAOmLSt2RKuXLtMHWq7eaIA-XO7e7tIA)

## 61 No overload matches this call.。アサインしようとしている型はオーバーロードしている型のどれにも当てはまらない

```ts
type A = (x: number) => string;
type B = (x: string) => number;
declare var x: A & B;
x(1); // error
x({a:"w"}); // error

No overload matches this call.
  Overload 1 of 2, '(x: number): string', gave the following error.
    Argument of type '{ a: string; }' is not assignable to parameter of type 'number'.
  Overload 2 of 2, '(x: string): number', gave the following error.
    Argument of type '{ a: string; }' is not assignable to parameter of type 'string'.(2769)
```

## 62 any型はinterfaceで作られたboolean型のneverタイプに割り当てられない。3.5

https://devblogs.microsoft.com/typescript/announcing-typescript-3-5/#improved-excess-property-checks-in-union-types

これの影響らしい
「構成要素の各タイプが個別にチェックされる」

[playground](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgCrIN4FgBQzkDOYUAXIcaAOYDcu+ARgPaMA2ZTrEcIteyHLAEztmLLjzrIAJmQAicSLwC+uXGACeABxQBpCOoIB5GKi0QAPKgA0yAKoA+ZAF5MyANoBrZKGQf9jGDQAXTJUTyDkCAAPSBApAjtkAH5fZDIQCAA3aGQlT39A1CDeXARGECJvEGAwAGUwBQhQ50xJIlJkAHIpRs6rSQEyYgBXCH6+AWFkeBYCMckZZAyAd2R5SAAKAEpxpRKcKQgEFjgoFDEwXzI9A2NTbUsbAXF7anwAenfkC6vkACIBH9kAAff6TP6qHA-DweMh+dQBNC8UA1eqNTweCIuP6QIhAz6AAAZAMoMRMAQAxoMxdHEQPGdbwJECMS5wAgEYCUEBwehiZBgRi8ymdDLZKCdAB0uBRdQakHCLU6uLAnTeyE+FO0VMVfzpwAZTOQLLZHK5PL5Ao1nWe3HFkuq0vRmJaIwgKrVjA8QA)

[TypeScript/issues](https://github.com/microsoft/TypeScript/issues/31663)

[playground](https://www.typescriptlang.org/play/?ssl=22&ssc=55&pln=22&pc=61#code/JYOwLgpgTgZghgYwgAgCrIN4FgBQzkDOYUAXIcaAOYDcu+ARgPaMA2ZTrEcItOAvrlwJGIIslDAwAZTBxIZdAF5MdcqWQByACZyIGgDSqObZMQCuEQ-164WEMMgDWZRxACejGGl4TpsyADajgC6yMoakEQagji4QA)

## 63 type T2 = string & boolean; // never

なぜか。

## 64 3.4以下では特定の過剰なプロパティは許可されていた

```ts
///////////1//////////////
type Point = {
x: number;
y: number;
};

type Label = {
name: string;
};

const thing: Point | Label = { // 3.5.1からはここで解決しなくてはいけない。(Source(代入元のobjectリテラル)にnameに違う型だった場合
x: 0,
y: 0,
name: true // uh-oh!
};

thing.name // TypeScript 3.3は参照する際に解決しないといけなかった。上ではエラーにならない
```

[playground](https://www.typescriptlang.org/play/index.html?ssl=1&ssc=1&pln=27&pc=3#code/PQ14IztoLAFABcCeAHApgAgAoHsCWAdopgLyYDeCmNmAHgFyaECuAtgEboBOA3NbWRNWnHv3gBfcQhQZMAGQCGXADZlKAmoUVt0TAM6JuRAObipCBAGNchQ5kQALU0zxESAHwXL0a8hUwQTABmADoAVlCIQGkGQEiGQHsGQGUGRMBzBkBjyMAvG0B1BkArBkB5BkAzBnjAEQZARQYc4sAgBgAKAGVcFm4rdGrAY7lAU0VAYUVAOwZcDgArdCtEQCuGQDGGQEuGQGuGAEpAawZtXTnAVQTAMQZAaPVAAwZAYwZAfQZAFg1ACBVNeiYABgAaE6FMS5PFvQduFiwglkcAWlxHAEIEC3gMmchBMoQegWAmAAKmh0LUrMZUCQwsF4oAh5UA5o6ATQZANEMgGy0uaZXLFQAWDGUctE9pVAFByKXigAqGCaAH4Y5jlYhUgA)

3.5以前はtarget側がUnionでかつ、Source側がオブジェクトリテラルを渡す場合、両方どちらかがsubTypeならその余分なプロパティの型チェックまでは行っていなかった。
(参照するときにエラー)
3.5からは余分なプロパティの型チェックまでアサイン時にする

non-disciminated union・・・

## 65  genelics引数の型がunion Typeの場合の理解

[playground](https://www.typescriptlang.org/play/index.html#code/PQ10GYE4PYLbjAWAFAEsB2AXAplCAhgMY4AEAEjgDZUwCyBADo5gOYBMpA3qqaQOQBBKowAWBfgC5SAZyxQ2vAQCEcWCdIwBXOACM8qAL6pURGBjmlR1WpwC8pADwAVUjgAeuDABMZpANY4AJ4wEBQ29EwsGBwAfAAUBFCs0s4AlNKUNJHMbOwA2s4AuqR2saSoPCh8fGYWWKRYkq4OSawV1TV1lgSZEQy5MQXFpaRtpCBuULBQSrXmPX3ZA9Ec+QBEwmIE6yWtyRPApDD+c6TdMFQ4AHS0rIlpSmhhiQd27wJb4vxppFBqWigGFI6wALOx1mM-HIFDFDgIYWx+KQ0H4MDAGgQZDI0KwMARdFdGjBGkFGGR+BgcAA3PD8a5Kf5YQHA8FQ0jaPR4eGUnT6KDI1EcjFQnF4glErAkrBkilU2kCowAbhMKGs2XY8U2InE60eKFViHAUqNptAqtMCwa6toAGZRi43J4cD4-IEQmEsrQVnkEm1UhlwssonlCntypUni9xu8HEIdRJfkyWSDwZCsUHvSGhmGeYiYoK0SKsWL8YSyFLSeSBPK6QzOsmgaQ2RmvTlVsMSpNeVyBSii5jsbiy5LpbKazS6crLfVSLpRjaYJrtds9RaUGbQDAoJujarutaIqCHa4PF5fAFgqFM+3fa8UqR0kss4M1iMyh0qnxG8CuPHtlIIIEAm6wADQqGoGikAAjIY+RtEU04oAe5wLkeWpfDsaRAA)

[stackoverflow](https://stackoverflow.com/questions/58538933/typescript-type-string-is-not-assignable-to-type-never-in-return-value-of-f)

```ts
declare function takeA(val: 'A'): void;
export function bounceAndTakeIfA<AB extends 'A' | 'B'>(value: AB): AB {
if (value === 'A') {
takeA(value);
return value;
}
else {
return value;
}
}
と同じ
type Common = { id: number };
type A = { tag: 'A' } & Common;
type B = { tag: 'B' } & Common & { foo: number };

type MyUnion = A | B;

const fn = (value: MyUnion) => {
    value.foo; // error, good!
    if ('foo' in value) {
        value.foo; // no error, good!
    }
    if (value.tag === 'B') {
        value.foo; // no error, good!
    }
};

const fn2 = <T extends MyUnion>(value: T) => {
    value.foo; // error, good!
    if ('foo' in value) {
        value.foo; // error, bad!
    }
    if (value.tag === 'B') {
        value.foo; // error, bad!
    }
}
```

- タイプがジェネリックである場合、ラップされて絞り込めない。
ナローイングが機能するためにはunion Typeでなくてはならない
union型の引数をもつジェネリックを解除するとうまくいく
- ネストされたプロパティによる絞り込み
ネストされたプロパティでもナローイングは発生しません
テスト(type ガード)によりフィールドのタイプを絞り込むことはできるが、
ルートオブジェクトは絞り込めません

[stackoverflow](https://stackoverflow.com/questions/50870423/discriminated-union-of-generic-type)

## 66 unionをkeyofしたさいに共通のキーがない場合 never

```ts
const str = 'hi';
const obj = {};
const complexObj = {
name: 'complexObject',
innerObj: {
name: 'InnerObject',
},
};

let strUnion: typeof str | string; // type: string
let objUnion: typeof obj | string; // type: string | {}
let complexUnion: typeof complexObj | string; // type: string | { ... as expected ... }

let strTyped: keyof typeof str; // type: number | "toString" | "charAt" | ...
let objTyped: keyof typeof obj; // type: never (which makes sense as there are no keys)
let complexObjTyped: keyof typeof complexObj; // type: "name" | "innerObject"

let strUnionTyped: keyof typeof strUnion; // type: number | "toString" | ...
let objUnionTyped: keyof typeof objUnion; // type: never (expected: number | "toString" | ... (same as string))
let complexUnionTyped: keyof typeof complexUnion; // type: never (expected: "name" | "innerObject" | number | "toString" | ... and all the rest of the string properties ...)
let manuallyComplexUnionTyped: keyof string | { to: string, innerObj: { name: string }}; // type: number | "toString" | ... (works as expected)
```

[playground](https://www.typescriptlang.org/play?#code/MYewdgzgLgBNBOMC8MDkALAlqg3AKFElhACMArZGAbwF99DoZQBbABwBsBTADwHlzKVPDBEwwAQ2acAXGhYce-Mp2BRUAGmGjMYMJ3hLZQ0SbGSZaAJK79SlWs0mamunjxdYCAKphM4WVAAnqycIABmcFCIAD6R8DoA5jimKaIA9GkwQSGyCInunMTkPn5gAcGhEaQUsXlgSampGVkVuVGJMLG0BbDyXNwl-i0h4UwgbP1KnXGJySLN2RZ1CdNUMAB0mzDiEDA8IaqcACYbWzRuHnEAKhVHsgDWnIGji6MIc41NmYuyYACuzBI+mmACIoCAAMrteog0HAdDieAAQSgsNim3WPRg1RuITuMEezwiryq5A+n1MC1aYk4ADdgQAKADuWHhMGY4keuwgnEgnG2uyg6H0-MR-LAIAJTwgAEosX1FORcccHk8XhVRgq+GT0t9qSCJFI0TAQTo9AZyPYQRdCnFBmBlfjCeqRhFvL5wOTdcMLP9AcDYmDIdCEsaMVjqvbHaqiT7RpGPWAvfM9TkafTEAz9vYVWIAUCYibwVD4jDphiYAyIOYBTN6jK5ZctVHbjGXZUxhMePaPlS03oM5Xs4d8QbzMbTTYLcpVMa-QXQcWQ2GtuIwCdxOx2FlhTB4JxGC9d8sYKx4CAQvAoJgD6d1o3bRz-pv2IEAMLjBQDRPRqWxk9dFkIBtKWCTqDAZq2OQRhmFIIEdDQdApj6vz5gGRbBqBK7rJWTIgPA9y7DsezcAcUDHDKQA)

let manuallyComplexUnionTyped: keyof string | { name: string, innerObj: { name: string }};
は
let manuallyComplexUnionTyped: (keyof string) | { name: string, innerObj: { name: string }};
であり
let manuallyComplexUnionTyped: keyof (string | { name: string, innerObj: { name: string }})
ではないため、すべてのkeyを取得できない

## 67 union全てのkeyを取得するにはconditional Typeを使う

```ts
type AllUnionMemberKeys<T> = T extends any ? keyof T : never;
let objUnionTyped2: AllUnionMemberKeys<typeof objUnion>; // = (keyof string) | (keyof {})
let complexUnionTyped2: AllUnionMemberKeys<typeof complexUnion>; // = (keyof string) | (keyof {name: ...,innerObj: {}})
```

なぜなら
型パラメータにunionな場合、
conditional typeがnaked typeとしてそれぞれ評価されるため

// 前述の32のことを言っている
チェックされる型(T)がネイキッド型パラメーターであるconditional typeは、distributive conditional typeと呼ばれる。

naked type・・・型パラメータが別の型(配列、タプル、関数、プロミス、その他のジェネリック型)にラップされずにラップされずに存在すること
distributive conditional typeとはcheck されるタイプがジェネリックパラメータ型で且つ、nakeked typeの
場合
[stackoverflow](https://stackoverflow.com/questions/55382306/typescript-distributive-conditional-types)

## 68 これが通らない理由も同じ

```ts
type VoidableCallback<EventValue> = EventValue extends void ? () => void : (val: EventValue) => void;
type Action = { type: 'add'; n: number } | { type: 'multiply'; n: number };
declare function fn<T>(): VoidableCallback<T>;
const callback = fn<Action>();
callback({ type: 'add', n: 1 });
```

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAag9gSwCYEMBGAbCBhFGNooDGA1gDwCiAbhAHbAx4CuEAfFALxTV0PPQQAHsDpIAzlCqIkUAPxQAFAEpO7KcigAuRVTzae9RhhYqOa6QG4AsAChQkKAEEiwBHFqcoAbyj2I2gHIUJCQAiyhabVomAFs0CAAnKABfKAAfb19wfygAmKYMVzAMEDCIqNj4pOTrGyQIIgwUBOgAMyZaFzcPVtoyABVWZW14ZHQsXHxCUgHWWqJ3MWAoIjwCYhJPXrJnV3chpXm16ZIFHz9A4NCAGnKoAEYUw6gAehe4Tdo4AHcgA)

callbackで呼び出している箇所は
関数のシグネチャがunionTypeの場合、パラメータは全てのシグネチャのunionTypeに互換性がある必要があるため
パラメータは全ての可能なパラメータ型の共通部分ではなくてはならない

早い修正は

```ts
type VoidableCallback<EventValue> = [EventValue] extends [void] ? () => void : (val: EventValue) => void;
type Action = { type: 'add'; n: number } | { type: 'multiply'; n: number };
declare function fn<T>(): VoidableCallback<T>;
const callback = fn<Action>();
callback({ type: 'add', n: 1 }); //ok now
```

distributive conditional typeにさせないこと(タプルにしてnon naked typeにする)

## 69 型パラメータでないunionTypeはdistributive conditional typeではなく、分配がされずtsは両方の型を同時に満たす互換性を求めてのシグネチャの型が推論される

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAqgdgSwPZwPIDMBiBXOBjYZOAZygF4oAKSgQwCcBzALijmwFsAjCOgSnIB8UGnBD8APlVqMWxYHQRwG-MkJFiAsAChtAel1QACvRrsIwHqQAmEPABsThFFEVQ7CTgDoIxAKyerT2BiPQNQSCMTMws6YgAeABUoCAAPCzgrUkpPHPoGYhZ1FTVRIQok1PTMqhzPPIKXOHQeI2LhUSgAfiMoFjgIADceAG5tbXDoBPAIKwB1BGAAC2M6U3NLckjV6Ms4+CIMHHwnEiF9KABtNi4eAF0oSQu5BSVb8emoKcgrABEEOlswDsIE2+xQh1wBCIpEqEAyWVq9RYimadFagnaIO6hl6rEGLXOVw43Do9wAZJdnooGG8dFo8Cg5MIWF8ZvMlis1jFSBQLgBGe7nOBIZJ0OhIOjaBkkYBQTgs6a-f6A4GbfmCgysqAAcmuJO1LlIwtlNGIxAQDDgNE4dmgwBFEx1-SGdG1nkoACYAMwej28bRAA)

[string] | [number]になるのは引数の型だから
[TypeScript/issues](https://github.com/microsoft/TypeScript/issues/35819)

## 70 抽出されるようすがわかりやすい説明 [keyof T]

```ts
type KeysMatching<T, V> = {[K in keyof T]: T[K] extends V ? K : never}[keyof T];

KeysMatching<Thing, string> ➡

{[K in keyof Thing]: Thing[K] extends string ? K : never}[keyof Thing] ➡

{
  id: string extends string ? 'id' : never;
  price: number extends string ? 'number' : never;
  other: { stuff: boolean } extends string ? 'other' : never;
}['id'|'price'|'other'] ➡

{ id: 'id', price: never, other: never }['id' | 'price' | 'other'] ➡

'id' | never | never ➡

'id'
```

[stackoverflow](https://stackoverflow.com/questions/54520676/in-typescript-how-to-specify-only-keys-of-a-generic-object-whose-values-are-stri)

## 71 discriminated unionとは

Unionのそれぞれの型にtype(タグ)をプロパティとしてつけて、それで識別すること
シングルトンタイププロパティ

```ts
const enum CarTransmission {
Automatic = 200,
Manual = 300
}
type Motorcycle {
type: "motorcycle"; // discriminant
make: number; // year
}

type Car {
type: "car"; // discriminant
transmission: CarTransmission
}

type Truck {
type: "truck"; // discriminant
capacity: number; // in tons
}
```

## 72 なぜ TypeScriptはvoid とundefinedを区別しているか

[playground](https://www.typescriptlang.org/play?#code/MYewdgzgLgBAZiEMC8MBuICWATGAGAbhgHpiYBXMbAUzkzGuwChRJYAjAQwCcV5LgUTOAAUASgDeAXyKl0WZq2gwuALz5du42WQw4gA)

- voidはその真偽を評価させることはできないところがundefinedとの違い
- functionのcallbackで使う場合promptやalertなど実行するのみで何も返さない型のreturn型にvoidではなくundefinedを指定すると、明示的にundefinedを返さなくてはいけない。voidに指定すると、返す型に「意味がない」、「単に無視して」とTSに伝えることができる

## 73

要素なしの配列(never[])にspreadingする

```ts
const arrayOfArrays = [1, 2,3]
[].concat(...arrayOfArrays) // no
new Array<number>().concat(...arrayOfArrays)
```

[playground](https://www.typescriptlang.org/play/index.html#code/CYUwxgNghgTiAEYD2A7AzgF3rGUCeA8gGYCCMueaAXPCgK4C2ARiDANoC6nA3AFC+cAdMhRgoGABSDpOfMTIU0ASn4oQAd3gL8AHnrNWAPglLhqMZOmDZhUuXzLuQA)

## 74 Type 'null' does not satisfy the constraint '{}'.(2344)

[playground](https://www.typescriptlang.org/play?ssl=9&ssc=1&pln=10&pc=1#code/JYOwLgpgTgZghgYwgAgOrDACwMIHsQJQSQAKUuADgDwAqyEAHpCACYDOyA3gL4B8nAKGTIE+QsQgAuZDQDcA7gNCRYiFOiwA5AK4AbXXABGuiGUq1e9JhFYcNOMUVLlqNS52SKBokGzDIYXFxpezwCJ1MXKhA9XUsAXi4hEUcJaRj9BXkfP2RDOCgQjEwdfSMTM2oMuOREwWEfcUh02KyBARhtAjBgfDy4AC8LAApA4LRisKbI8zcASi5kZLGAOkaIlYUgA)

{}はnullとundefinedを除外する不明なものとしてであり、どんなタイプでも受け付けるという意味で制約するところ(superTypeとして)に使えない

https://levelup.gitconnected.com/getting-to-know-and-love-typescripts-meta-types-5e17a8856b17

## 75 Numeric Separators

`1_000_000_000`は`1000000000`とコンパイルされる

[playground](https://www.typescriptlang.org/play/?ssl=1&ssc=14&pln=1&pc=1#code/IwfQDB6RZA
https://medium.com/articode/some-lesser-known-typescript-features-d067e29797d0)

## 76 抽出したり、　unPackしたりする

WIP

## 77 この人stackoverflow内で解決する人としてよく見かける

[stackoverflow](https://stackoverflow.com/users/125734/titian-cernicova-dragomir)

## 78 LiteralUnionという便利があるらしい

現状リテラルなUnionTypeにプリミティブな値も加えると、補完機能が効かなくなる

```ts
type Pet = 'dog' | 'cat' | string;
const a:Pet = "dog" // string
// ライブラリ、type-fest(https://github.com/sindresorhus/type-fest)のLiteralUnionを使うと解決する

export type Primitive =
| null
| undefined
| string
| number
| boolean
| symbol;

export type LiteralUnion<
LiteralType extends BaseType,
BaseType extends Primitive
> = LiteralType | (BaseType & { _?: never });

const b: LiteralUnion<Pet, Primitive> = "dog"
b // "dog"
```

[playground](https://www.typescriptlang.org/play/?ssl=19&ssc=11&pln=3&pc=7#code/C4TwDgpgBAChxQLxQOQBMD2BzFUA+qAxgIbAoDcAUJYRgHYDOCxAXLPElAESZZdQB6AVCYAnAJZ0s1CAA8wGUQlCRYEgLbjg4gG7RElAJAE6AVwA25owVN00EAGaSIaayOASpbs+oBGEUTdfDAxzCGI6NwYQP1CqSjkFJSgVaAAZLQDicwBVOnF6AB4jDOAs8wAVcGg5MrsGKAAhYgYIKsgAGiNm1vaa2Tq0BpgNLV0ISigpqAA+TlLyvvwoAAoetuqoADIoAG8oAH0AfjY6CD1RKABfAEp42kYEXzYF0Wy8grpCuGAOtXFNNo9HNkDxsFxKL5BMIwXwgA)

## 79. Object.valuesが推論効かない -> target2017にして

// 一回keyを挟む方法もある
[stackoverflow](https://stackoverflow.com/questions/42966362/how-to-use-object-values-with-typescript)

```ts
const data = {
  a: "first",
  b: "second",
};

const values = Object.keys(data).map(key => data[key]);

const commaJoinedValues = values.join(",");
console.log(commaJoinedValues);
```

[playground](https://www.typescriptlang.org/play/?target=7#code/MYewdgzgLgBAJgQygmBeGBvBAuARADgFMA2Q-AJgFZKBaBUgThoBZjziaHiAGD54OFUrdqAMwDsARlwAaGACM8k3sADMxSappwG4vqtWS687sG2V8kynsLjgCaQF8A3AChQkWADcEAGwCuhBBoMADy8gBWhMBQAHQ+AUEAFIjIAJSxALYIAA5JAB5oAHww+bEQ-vLQAE5J3HJlvoRgAOZQABYwNDDMaWluHtAwoJnZAFIgAJZghHAAan6BwegJS7ERU2BJAOQy2-3u4BAgTbG+IC1JI+ObswuJEAeurgD0by9QIO-fL8+D3otCOQQklwlEYrEANaEACeEBSSAQaRgCGCAEFqtUEDCADzQmEgUQwKAwnKEQnwRFFDLZPKEYqU5AAbUIAF00q4gA)

## 80. typescriptのOption全部説明できる?? -> できない -> 復習

[ドキュメント以外の解説](
https://medium.com/javascript-in-plain-english/typescript-configuration-options-tsconfig-json-561d4a2ad4b)

- checkJs・・・allowJSと一緒に使うと、JSのファイルに対してもエラーを検出してくれる
など

WIP

## 81. eventで得た型をより汎用的にする方法

https://qiita.com/Takepepe/items/f1ba99a7ca7e66290f24

汎用的にしたければ型を抽象的にして、厳密にしたければ絞り込む。それはイベントの型も一緒

## 82

https://github.com/sindresorhus/type-fest の中で読めないところを列挙する

## 83 v3.8.3からエラーになった

[stackoverflow](https://stackoverflow.com/questions/51324284/typescript-intersection-type-and-function-signature-not-throwing-expected-error)

indexSignatureは全てのintersectionの型を一つ一つチェックするようになった(今まではstringならok)

[playground](https://www.typescriptlang.org/play/index.html#code/C4TwDgpgBAogHgQwLZgDYQIJQLxQN4BQUUCAXFAM7ABOAlgHYDmA3AQL4EGiSyIroAhHPiJQARuXoBXJGIjVWHLuGjxkaCAGFhhYgGNyYgPZH0Ceos7dVfDRWFr+mKADJRjjULfEP6Ta04AMyl6PWBaI3ooQJMACjAEYGB5enI8KABtAGsIEHIqOiYAXXyaBkYoNgBKEX1IilMIADpUI0Z4xOTqeirLAj164HEEanJfCHtcdLIoAHIYo1mAGnFyAEYVg2iEVApoNmYoAgXYsRHegHoLqGAAC3loClujKVQAE3Foc1hqaiNqKD3agQUTEMHEK6UBh6GzqQQkegfcbaN6RWZDJCJPS3G73KBICB3IwfCi0Rj0RJSYGcIA)

3.8.3以前のTypeScript振る舞い
[TypeScript/issues](https://github.com/Microsoft/TypeScript/issues/24970#issuecomment-397448205)
この例題のokとされているところがわからなかった 84へ

## 84. Intersection をinterfaceと一緒に使うということ

[stackoverflow](https://stackoverflow.com/questions/52681316/difference-between-extending-and-intersecting-interfaces-in-typescript)

## 85. Denoとは

- セキュアなJSとTSの実行環境
https://deno.land/
https://deno.land/std/manual.md

https://dev.classmethod.jp/articles/deno-init/

## 86 nullであてっも通ってしまう

https://qiita.com/mangano-ito/items/5583783cd88ea5f4deb4#null-%E3%82%82%E9%80%9A%E3%81%99
ここのこと

## 87 Apparernt Member(明らかなメンバー)とは

https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md#3111-apparent-members
- subtype、supertypeの中にあり、互換性のあるメンバー

## 88 型引数の推論が決定されていく順序、決まり

https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md#4152-type-argument-inference

```ts
function choose<T>(x: T, y: T): T {
    return Math.random() < 0.5 ? x : y;
}
var x = choose(10, 20);     // Ok, x of type number
var y = choose("Five", 5);  // Error
```

左から推論され、stringがTになった後、5(number)はstringでも他のタイプのsuertypeではないため推論は失敗してErrorが起きる

これを説明してみる

```ts
function zip<S, T, U>(x: S[], y: T[], combine: (x: S) => (y: T) => U): U[] {
    var len = Math.max(x.length, y.length);
    var result: U[] = [];
    for (var i = 0; i < len; i++) result.push(combine(x[i])(y[i]));
    return result;
}

var names = ["Peter", "Paul", "Mary"];
var ages = [7, 9, 12];
var pairs = zip(names, ages, s => n => ({ name: s, age: n }));
to
function zip<string, number, U>(x: string[], y: number[], combine: (x: string) => (y: number) => U): U[] {
    var len = Math.max(x.length, y.length);
    var result: U[] = [];
    for (var i = 0; i < len; i++) result.push(combine(x[i])(y[i]));
    return result;
}

var names = ["Peter", "Paul", "Mary"];
var ages = [7, 9, 12];
var pairs = zip(names, ages, s => n => ({ name: s, age: n }));
```

推論をつかってSとTが順番に決定され、関数内のそれぞれの型が決定されると、

```ts
function zip<string, number, {name: strinng, age: number}>(x: string[], y: number[], combine: (x: string) => (y: number) => {name: string, age: number}): {name: string, age: number}[] {
    var len = Math.max(x.length, y.length);
    var result: {name: string, age: number}[] = [];
    for (var i = 0; i < len; i++) result.push(combine(x[i])(y[i]));
    return result;
}

var names = ["Peter", "Paul", "Mary"];
var ages = [7, 9, 12];
var pairs = zip(names, ages, s => n => ({ name: s, age: n }));
```

Uが決まり、
結果zipは

```ts
var pairs = zip<string, number, { name: string; age: number }>(
    names, ages, s => n => ({ name: s, age: n }));
```

全て推論された状態

## 89. base constraintの決定のされ方

https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md#361-type-parameter-lists

## 90. TS3.5の変更点

- Tが解決されなかったら{}とされていたものがunknown (https://github.com/microsoft/TypeScript-wiki/blob/master/Breaking-Changes.md#-k-string-unknown--is-no-longer-a-wildcard-assignment-target)
- Tがunknownになったこで{[ind: string]: unknown} がなんでもokではなくなった

3.5以前代入可能

```ts
declare function someFunc(): void;
declare function fn(arg: { [k: string]: unknown }): void;
fn(someFunc);
```

3.5以降

```ts
Argument of type '() => void' is not assignable to parameter of type '{ [k: string]: unknown; }'.
Index signature is missing in type '() => void'.
```

targetがUnionの場合過剰なプロパティの型チェックは行われなかったのが行われるようになった
(Unionの場合それぞれの型チェックの際にプロパティが存在しさえすればよかったが、その型までチェックされてなかった)
[playground](https://www.typescriptlang.org/play/index.html?ts=3.5.1#code/C4TwDgpgBAggthATgSwMYEMB2UC8UDeAUFCVAB4BcUmArnAEZIDchAvi4aJFAFLphZcBYqRBUAzsBSYA5i3aFCAeiVQAzADoArIFO5QLJKgAwZAFgwB7ANaBABk27AsmnGkiE4kBADIVQnMkqGAA2VeEhoggA+vPyCeESk5FQADAA0IiRiUFI00CqS0jKA5gyAQgyA0QyAngyAsgzOgKoJgGIMgOsMgLcMgIsMgGMMgMUMgLAqgPYMgDiWgF+KgKoMgDEMBa6siipQABbAwGDiFCoyyMDjNPQa7nBKcGiO4iYAZsBKACrgEADKqChgwAC0AO7IZshK9D4m9JvokkhKAEKIEHQT1kNwAwuMsDIIOINHAACYAYjuTjM6EcNEwcPEo1UgAA5QBCZoAQt0AVgyAY8jAF42gDVvACMylUAG1kJiJFImTIALpUDFmTAmO6YQDWDIAIhgKFMpnFOsAQKAwmAATEIoqRKNQ6IxECxoozmVAsmzOVBubz+WwWO5PMBvHL-NKgvKoKE+AJsJEkjEoAk3Sk0hA2HSoASSWK5f6vlLArK3B4vGA1DaIyEws7FW6VZ7ot7EOk2FAwwEZVhFEA)

## 91 3.4のバグを3.5以降修正

```ts
type A = {
s: string;
n: number;
};

const a: A = { s: "", n: 0 };
function write<K extends keyof A>(arg: A, key: K, value: A[K]): void {
//2. ここでなんらかの上程で文字列が入ってもokになっている..
arg[key] = "hello, world";
}
write(a, "n", 1); //1. nはnumberで1を渡しているが...
```

3.5以上は

```ts
Type '"hello, world"' is not assignable to type 'A[K]'.
Type '"hello, world"' is not assignable to type 'string & number'.
Type '"hello, world"' is not assignable to type 'number'.
```

https://github.com/microsoft/TypeScript-wiki/blob/master/Breaking-Changes.md#fixes-to-unsound-writes-to-indexed-access-types

## 92 Grammar Ambiguities とは

WIP

[issue](https://github.com/Microsoft/TypeScript/issues/1121)

## 93　`const foo = <T, >(x: T) => x`

tsxだとこのような書き方ができる
`extends {}`　だという

[stackoverflow](https://stackoverflow.com/questions/32308370/what-is-the-syntax-for-typescript-arrow-functions-with-generics)

## 94 any型かどうかを知る型定義

[stackoverflow](https://stackoverflow.com/questions/61624719/how-to-conditionally-detect-the-any-type-in-typescript)

## 95 TypeScriptとbable

おさらい

- Babel7(webpackのbabel-loader)がts-loaderにとって変わった。babel-loaderがTSをトランスパイル
https://devblogs.microsoft.com/typescript/typescript-and-babel-7/

- 型チェックはtsc

https://iamturns.com/typescript-babel/

使う場合。以下はコンパイルされない(2020/5/6)
- namespace
- bracket style assrtion. i.e `<Foo>x`
- 複数マージされたenums i.e `enum merging`
-レガシースタイルなimport export `i.e. import foo = require(...) and export = foo`

https://babeljs.io/blog/2020/03/16/7.9.0
Babel 8は 2020年4月予定?

## 96 keyof unionTypeは共通のkeyを返す

```ts
interface Foo {
    foo: string;
}
interface Bar {
    bar: string;
}
type Batz = Foo | Bar;
type AvailableKeys = keyof Batz; // never, has not common key

// 共通のが欲しい場合
////fix////

type Batz2 = Foo | Bar;

type KeysOfUnion<T> = T extends any ? keyof T: never;
// AvailableKeys will basically be keyof Foo | keyof Bar
// so it will be  "foo" | "bar"
type AvailableKeys2 = KeysOfUnion<Batz>;

//Unionの単純なkeyが機能しない理由は、
// keyofが常に型のアクセス可能なキーを返すためです。これは、共通キーのみになるunionの場合です。
// KeysOfUnionの条件付きタイプは、実際にはユニオンの各メンバーを取得してそのキーを取得するため、
//結果はユニオンの各メンバーに適用されるkeyofのユニオンになります。
```

## 97 Transform union type to intersection type

反変を利用したUnionTypeからintersectionTypeにする方法

```ts
type UnionToIntersection<U> =
  (U extends any ? (k: U)=>void : never) extends ((k: infer I)=>void) ? I : never
```

[stackoverflow](https://stackoverflow.com/questions/50374908/transform-union-type-to-intersection-type/50375286#50375286)

なんで関数の引数に渡しているんだと思った。
反変が起こる箇所(関数の引数)はintersectionTypeになる


```ts
type X = (1 | 2 | 3) extends (infer I) ? [I] : never;

```


[stackoverflow](https://stackoverflow.com/questions/54936474/typescript-how-to-explain-this-interaction-between-extends-and-function-type)

## 98 convariance(共変) and contravariance(反変)のわかりやすい説明

[stephanboyer.com](https://www.stephanboyer.com/post/132/what-are-covariance-and-contravariance)

```ts
* A ≼ B means A is a subtype of B.
* A → B is the type of functions for which the argument type is A and the return type is B.
* x : A means x has type A.
```

だとして
Dog → Dog
Animal → Greyhound
が引数は反変、返り型は共変
strictFunctionTypesは引数の型を厳密にする

型変数が推論されるinferの位置が共変位置にある場合Unionに推論される
型変数が推論されるinferの位置が反変位置(関数の引数)にある場合intersectionに推論される

[pullrequest](https://github.com/Microsoft/TypeScript/pull/21496)

## 99 条件型の解決するか遅延するかの条件とは?

WIP

## 100 いろいろなtypeを調べるのにもう少し簡潔な書き方

inferは実際には1つめで変数にして、2つ目のif文で使う
[stackoverflow](https://stackoverflow.com/questions/54599951/typescript-generically-infer-union-type-member-based-on-a-string-literal-proper)

[playground](https://www.typescriptlang.org/play/index.html?ssl=1&ssc=6&pln=1&pc=12#code/C4TwDgpgBAIhBuBLAxtAvFAMgQzMA9mFAD6wQDOA1gUaQAoAW+AdhANwCwAUN4s8BABOAM2yosuGlADe3KFFCQAXFADkOPIVWcu85PgCu-GIMTwIAIWwhyK5gYC2AIyE75DbOQAqh5AwDKyIIQEMwqTvj4ADYQ2Mw6AL7cvPxCouJwVFKyugrgECqqmdRablD6RsAmZpbWtlD2zq7cSTxcfAIiYtCMLNA58ooFar2s2nJQHt6+AUEhYVAR0bHxLclcQ2RIqADywgA8XvlQEAAeAswAJuRbKBAA2qpDqgC6AHxQaBNw29BnF9coAAKPjCISwACUUAA-N8TudQoCfncYd9Hs8XvCATcjpAYbAoHYEOCieZBISGsTBDpuJsAEoUAxRYCfW67A7qSRaN5sKDcIA)

## 101 tupleのkeyが欲しい時

```ts
type TupleKeys<T extends any[]> = Exclude<keyof T, keyof []>
type A = TupleKeys<[string, string, string]> // "0" | "1" | "2"
```

[playground](https://www.typescriptlang.org/play/index.html?ssl=1&ssc=1&pln=3&pc=64#code/C4TwDgpgBAKgrmANhA0hEBnAPDKEAewEAdgCYZQCGxIA2gLoB8UAvFAKL4DGicpEWANboA9gDNYAGijCQ4qA0YBYAFCrQkKAEFWsBMjSYstDMABOAS2IBzaacs275q9aZQA9O6gAiAAzeoAB8fAEYA4O8AJm8gA)

arrayが持つproperyを除外するとインデックスのキーになるということ

## 102

型の移り変わりの説明わかりやすいし面白い
[stackoverflow](https://stackoverflow.com/a/59463385)

```ts
IntersectItems<[{ a: 1 } | { b: 2 }, { c: 3 }]> =
Unfoo<Intersect<Values<Foo<[{ a: 1 } | { b: 2 }, { c: 3 }]>>>> =
Unfoo<Intersect<Values<{0: { foo: { a: 1 } | { b: 2 } }, 1: { foo: { c: 3 } }}>>> =
Unfoo<Intersect<{ foo: { a: 1 } | { b: 2 } } | { foo: { c: 3 } }>> =
Unfoo<(({ foo: { a: 1 } | { b: 2 } } | { foo: { c: 3 } }) extends any ? ((x: T) => 0) : never) extends ((x: infer R) => 0) ? R : never> =
Unfoo<(({ foo: { a: 1 } | { b: 2 } } extends any ? ((x: T) => 0) : never) | ({ foo: { c: 3 } } extends any ? ((x: T) => 0) : never)) extends ((x: infer R) => 0) ? R : never> =
Unfoo<(((x: { foo: { a: 1 } | { b: 2 } }) => 0) | ((x: { foo: { c: 3 } }) => 0)) extends ((x: infer R) => 0) ? R : never> =
Unfoo<{ foo: { a: 1 } | { b: 2 } } & { foo: { c: 3 } }> =
({ foo: { a: 1 } | { b: 2 } } & { foo: { c: 3 } })["foo"] =
({ a: 1 } | { b: 2 }) & { c: 3 } =
{ a: 1 } & { c: 3 } | { b: 2 } & { c: 3 }
```

## 103 `interface`ではできないこと

- オブジェクトにたいしてツリー構造を作ること
`type Tree<T> = T & {parent: Tree<T>}`

- 変数を制限して少数の値のみに割り当てる型
`type Choise = "A" | "B" | "C";`

- conditional typeを使ったNonNullableのようなもの
`type NonNullable<T> = T extends null | undefined ? never : T;`

## 104 enumのkeyをGenericsで取得する

```ts
`enum MyEnum { A, B, C };`
`type AnyEnumKeysAsStrings<TEnum> = keyof typeof TEnum;`
// 'T' only refers to a type, but is being used as a value here.(
type MyEnumKeysAsStrings = AnyEnumKeysAsStrings<MyEnum>;
```

[playground](https://www.typescriptlang.org/play/index.html#code/KYOwrgtgBAsgngUXNA3lAggGigIWwYSgF8BuAWACgAXOAB2AxEWQGlg4BndDgZSoCcAliADmHADwAVAHxQAvFADW7APYAzKDXrqokygHp9UAOSTjUFSAA2cKP2Brg-DppVQAhprrBsAIzBUUIIuvsDCIlBgHMAAJh4ungBu7lZgDAAWTsAAdAAUlFBQlFoM8EiQbJzcfEKiLgroTOUQlVy8AuESZcjS5BSUQA)

stackoverflow

## 105 road map TypeScript

https://github.com/microsoft/TypeScript/issues/36948
6月までのroadmap

## 106 DDD with TypeScript

[An Introduction to Domain-Driven Design - DDD w/ TypeScript](https://khalilstemmler.com/articles/domain-driven-design-intro/)

## 107 本来存在しないpropertyはneverにする

```ts
// key1がtrueのとき、key3が存在し、key1がfalseの場合、key3が存在しないをどのように表現するか
interface Sample {
    key1: boolean;
    key2?: string;
    key3?: number;
};

// type aliasを使う。
type Sample3 = { key1: true, key2?: string, key3: number } | { key1: false, key2?: string }

// neverにしてあることに注意
type Sample2 = { key1: true, key2?: string, key3: number } | { key1: false, key2?: string, key3: never }

```

[playgrounnd](https://www.typescriptlang.org/play/#code/PTAEGsFME8EZBkGALgJwK6UHYMgLBkLIMhABimgGZ5ANbUAp1QdQYCYEAzAQwBsBnTQFg1AIFRuLKsCsGQCIMgJIZAlgwZAEQyAxBkDWDIAsIwH-OgTQZA0QyBpBgCwAKACWAO0SRkjAMaRQAZQYBbAA5NzAb22hXEWgC5QAIwD2vhwY9AG4XN0IAJgB+LxYUfQBzUK03d2IY0D1Ua28jZIBfZO1EaFtzKzsHIlAAXlBHQlgvFHQAGjTo2Pi9BPbCIi8snKN80AAferSm0EZWSD6YTtA45ETQfO0QUBKy0GYdBhZhQH95KUAgBj1IADcjGVVAZQYsGUALm0B4Q2LS8pt7SAjaycazTQ8w6GRWiQWxEG2VyyHW4wBnhmzDYkKW4J6kIGmWuRnW2iAA)

## 108 this.hoge[key] = hoge[key]がnverに対してアサインしようとしている理由

```ts
interface Opts {
  onFrame: () => void;
  onAudioSample: null;
  emulateSound: boolean;
  sampleRate: number;
}

class NES {
    constructor(opts: Opts) {
        this.opts = {
            onFrame() { },
            onAudioSample: null,
            emulateSound: true,
            sampleRate: 44100,
        }
        if (typeof opts !== "undefined") {
            let key: keyof Opts
            for (key in this.opts) {
                if (typeof opts[key] !== "undefined") {
                    // Type 'number | boolean | (() => void) | null' is not assignable to type 'never'.
                    // Type 'null' is not assignable to type 'never'
                    this.opts[key] = opts[key];
                }
            }
        }
    }
    opts: Opts
}
```

[playground](https://www.typescriptlang.org/play/?ssl=1&ssc=1&pln=67&pc=1#code/JYOwLgpgTgZghgYwgAgPIAcwGdkG8CwAUMsgPYgBiUcAthAFzIAUAlMgLwB8yAbqcABMA3ERLkAggFcBwUgGVa6ADYNkISUqUjiyCDQ1xIc0pJADGAI1KkVcENpJZFKgEqHV6mhejaAvkSIEJTgsHAA5AFE5PFESZARyLDAoSQQwUigmUkwsRgxsNgIdOLiwAAtgLAA6bOwOGOKSpvIqWghWPGRfABpYpuaQKRl5Zw8NJV7G-pI9AyMTM0ZkyQhJ6aanGmUIN0hGABZ9gEYABhO16f9CPqbgGGYwAE90CFJ72pwAQnZ2ZAAiUwCCAwUAQAR-Qo3dYqMDIADWEEejARjzeaByUOmMAyzBRyFAyHKlRqOUhU3WcTuD2er3eOQA2iiALrIb6-AFmYGg8Fkil85AAegFyAA5qRYdAoMgytAIJj+aUKtUPozESzfirmQ4FXErgq9ZcbgaxDk8hjCL4gA)

`this.opts[key]` と `opts[key]`が同じなため推論できない

`opts[key]`は `Function`, `null`, `boolean`, `number`

`this.opts[key]`はkeyに応じて`Function`, `null`, `boolean`, or `number`を受け取る必要があるが、分からない

keyがunionTypeで推論が効かない場合、それぞれの共通のsuperTypeに推論されて`never`になり、それを要求している

[stackoverflow](https://stackoverflow.com/questions/61671898/typescript-error-type-number-is-not-assignable-to-type-never)

`Object.assign`で解決できる

```ts
class NES2 {
    constructor(opts: Opts) {
        this.opts = {
            onFrame() { },
            onAudioSample: null,
            emulateSound: true,
            sampleRate: 44100,
        }
        if (typeof opts !== "undefined") {
                this.opts = Object.assign({
                onFrame() {},
                onAudioSample: null,
                emulateSound: true,
                sampleRate: 44100,
            }, opts);
        }
    }
    opts: Opts
}
``

or

```ts
(this.opts[key] as Opts[typeof key]) = opts[key];
```

[playground](https://www.typescriptlang.org/play/?ssl=44&ssc=35&pln=41&pc=12#code/JYOwLgpgTgZghgYwgAgPIAcwGdkG8CwAUMsgPYgBiUcAthAFzIAUAlMgLwB8yAbqcABMA3ERLkAggFcBwUgGVa6ADYNkISUqUjiyCDQ1xIc0pJADGAI1KkVcENpJZFKgEqHV6mhejaAvkSIEJTgsHAA5AFE5PFESZARyLDAoSQQwUigmUkwsRgxsNgIdOLiwAAtgLAA6bOwOGOKSpvIqWghWPGRfABpYpuaQKRl5Zw8NJV7G-pI9AyMTM0ZkyQhJ6aanGmUIN0hGABZ9gEYABhO16f9CPqbgGGYwAE90CFJ72pwAQnZ2ZAAiUwCCAwUAQAR-Qo3dYqMDIADWEEejARjzeaByUOmMAyzBRyFAyHKlRqOUhU3WcTuD2er3eOQA2iiALrIb6-AFmYGg8Fkil85AAegFyAA5qRYdAoMgytAIJj+aUKtUPozESzfirmQ4FXErgq9ZcbgaxDk8hjCFchVbrTabVAIFgbDwILbXW73R7PURPT7fX7-TbAsFQshInIAEwNEoJEBJFJpDJZU3ogpR6ZE5U5epFBUtah0Dq4LoXPkSaSyBRbFSMdSaEsU2bBeaApYpVbyjajXaqQ6nc4d423e5MJ4vNEfVk-f6ArkgMEQtN8jMkuq-HM6sSUfPtQrFjvrMvDSvbGvjesKxvuYwtwlt8-8zbbbsHY5ne98qqfj773UDo03D4zWwIgrm9AMhQycCoIDIMQnCKIAGZF3iRJlgTTJAJTLBeXTJUVxwNcf03VoC13HoiLIQZyxGKsxjrCjL2bRZbxWd9O1o59kF7N8-2uckSCpEcaXHLM2WnTkQTnHlkOhCBYRRZFETRfIsAo7EpSYPECWXD4cI3QTR1pMgGWZSd2RnST5z0jc4iFZAQmQCj1hHPDNTVeycBU+lDLRZk2A1Ey1W1DdB3WULf0aQdMJUkCiCAA)

## ref

https://dev.to/achimoraites/typescript-tips-and-tricks-4fnh (TypeScript Tips and Tricks)
https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#new-unknown-top-type
https://dev.to/busypeoples/notes-on-typescript-mapped-types-and-lookup-types-i36
https://itnext.io/5-typescript-tips-to-improve-your-applications-e882d69592bd
https://levelup.gitconnected.com/getting-to-know-and-love-typescripts-meta-types-5e17a8856b17
https://itnext.io/typescript-extract-unpack-a-type-from-a-generic-baca7af14e51
https://qiita.com/Takepepe/items/f1ba99a7ca7e66290f24
https://qiita.com/vvakame/items/e7bbaff54db8fbf986bb

## まとめ

- stack overflowの悩んでいるところを解決するようになった
- なぜ~なのかという目線で調べると深いところまで行けそう
- わかりやすい回答をする人の回答一覧をみると理解深まる
  - [jcalz](https://stackoverflow.com/users/2887218/jcalz)
  - [titian-cernicova-dragomir](https://stackoverflow.com/users/125734/titian-cernicova-dragomir)