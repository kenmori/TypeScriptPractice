# TypeScript 練習問題集(Practice TypeScript and Playground) latest update(2020/8/26)

<img src="https://kenjimorita.jp/wp-content/uploads/2019/02/8a154126e82bbd3957478cedded330b3.png" width="400" />

- [author](https://kenjimorita.jp/aboutme)
- [【TypeScript】仕事で使える!TypeSript中級者になる為に知っておくと良い108個のこと](https://kenjimorita.jp/typesript-check-108/)

**TypeScript and Playground練習問題集更新情報**

WIP

```text
- 問題を追加(2020/8/25)
- 問題を追加(2020/8/16)
- 問題を修正(2020/5/10)
- 問題を追加(2020/5/6)
- 問62、63、64を追加(2020/4/29)
- 問2の問題を修正(2020/4/27)
- 説明・例文を追加(2020/4/26)
```

- こちらは [@bukotsunikki](https://twitter.com/bukotsunikki)自身が学習するための問題集です。
- 以前[JavaScript問題集](https://gist.github.com/kenmori/1961ce0140dc3307a0e641c8dde6701d)を作りましたがそれのTypeScript版です。
- [わたしについて](http://kenjimorita.jp/aboutme)  [![Twitter URL](https://img.shields.io/twitter/url/https/twitter.com/bukotsunikki.svg?style=social&label=Follow%20%40bukotsunikki)](https://twitter.com/bukotsunikki)
- 随時更新して行きますのでスターつけていただけると励みになります。
- 最初から答えが見えているのは都度操作させないためです。

ちょっとやってみましょう

**問0**

こちら

```ts
const greeting = (value) => "hello!" + value
```
関数`greeting` は `"hello!world!"`など、任意の文字列返す関数です。
引数`value`に型注釈してください

> ここまでが「問題」です。
> 以下「答えの一例」になります。

```ts
const greeting = (value: string) => "hello!" + value
```

では早速始めましょう

---



**問1**

こちら

```ts
interface Foo {
    bar: string;
    baz: number;
}
```

Fooが持つプロパティ全てoptionalにしてください

```ts
type PartialFoo = Partial<Foo>;
```

**問2**

こちら

```ts
type Foo = {
    name?: string;
    age?: number;
}
```

Fooが持つプロパティ全て必須にしてください

```ts
type RequireA = Required<Foo>;
```

**問3**

こちら

```ts
type Foo = {
    name?: string;
    age?: number;
}
```

の`Foo`から`name`だけを取得したtypeを作ってください

```ts
type Picked = Pick<Foo, "name">
```

**問4**

こちら

```ts
type Foo = {
    name?: string;
    age?: number;
}
```

`Foo`から`age`を省略した型を作ってください

```ts
type Omited = Omit<Foo, "age">;

// Omited {
//    name?: string | undefined;
// }
```

**問5**

こちら

```ts
const user = { name: "kenji", age: 98 };
```

のuserに推論される型は何ですか。またその理由を教えてください。

```ts
{ name: string, age: number }

JavaScriptのオブジェクトはconstであれ(freezeしない限り)書き込みが可能です。
それそれのプロパティ値はあとで書き込めるようにwindeningされ、それぞれのプロパティの型はプリミティブ型になります。
これをそれぞれのプロパティをリテラル型にするには
as constか型注釈をすることです。(下記playground)
```

- [as const](https://www.typescriptlang.org/play/index.html#code/MYewdgzgLgBArhApgJxgXhgbxmAhgW0QC4YAiAa0TACsBLUgGhlwHNiYBOADgF9mIYoSFABQQA)
- [型注釈](https://www.typescriptlang.org/play/index.html#code/MYewdgzgLgBArhApgJwFwwN5gIYFtHoBEA1omAFYCWhANDNgOYEwCcAHAL4wC8mMO+IqQrU6jZuw4AoIA)

**問6**

`T extends U ? X : Y` はどのような意味になりますか

```ts
// Conditional types

T extends U ? X : Y;
// TがUに代入可能ならXを、そうではない場合Yを返す
// T型がU型と互換性あるならXを、そうでない場合Yを返す
// T型がU型のサブタイプならXを、そうでない場合Yを返す
// T型がU型の部分型ならXを、そうでない場合Yを返す

// ()=> void extends Functionは互換性がある
// "hello!" extends String

```

**問7**

下記

```ts
interface Part {
  name: string,
  age: number,
  add(): number
}
```

メソッド名だけ取り出した型を作ってください

```ts
interface Part {
  name: string,
  age: number,
  add(): number
}

const obj = {
  name: "kenji",
  age: 99,
  add: () => 1 * 2
}

type FunctionPropertyNames<T> = {
  [K in keyof T]: T[K] extends Function ? K : never
}[keyof T]

type result = FunctionPropertyNames<Part>

```

[playground](http://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgApymZBvAUMg5EOAWwgC5kBnMKUAcwBp9C56KiBXEgI2mcLI4AE2EAKAJSUQ3PlFwBfXLgQB7EDWSqeAK2QBeHCwLEylAEQBrCCB3BzA1u0oBOF44IjhlSQYB8yACMyABUyABMispgAJ4ADigAYpwgCGDA6qhQqgmYMQBypBBUADwAKgGGeIIA2gDSyKDI1jGqMMhlALqUZfWdyBAAHpAgwlTIyanp6sgA-MgN0hAAbtCKNS1tHZ3R8ShQxZwANliGk2kZIFk50LGFZKXomH5AA)

**問8**
neverとはどんな型ですか

```ts
・絶対にreturnされない関数
・常にthrowされる関数

例えば

function foo(x: string | number): boolean {
  if (typeof x === "string") {
    return true;
  } else if (typeof x === "number") {
    return false;
  }
  return fail("Unexhaustive!"); //ここはreturnされないので neverが返っている。boolean型が返る関数なのでError
}

function fail(message: string): never { throw new Error(message); } // 常にthrowされるのでnever型が返る


// 1. 型推論の結果、取り得る型が無い状態になった時の変数・メンバーに付けられる型

const bool = true
if(bool){
    const a = bool; // boolean
} else {
    const b = bool; // never
}

// 2. return文が無く、かつ(無限ループなどで)関数末尾に到達しない関数/アロー関数に対して推論される戻り値の型
```

 **問9**

これは

```ts
(...args: any[]) => any
```

 どういう意味ですか？

 ```ts
// 関数ならなんでもOK
 ```

 Type inference in Conditional types

 **問10**

これは

 ```ts
 type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;

 ```

 なにをする型か説明してください(とくに`infer`)

 ```ts
// Tが関数ならその関数の戻り値をRにキャプチャしてそれを返す
```

  **問11**

 非同期の中身を取る型を書いてください

 ```ts
type ResolvedType<T> =
T extends Promise<infer R> ? R :
T extends Observable<infer R> ? R :
T;
```

**問12**

Nullableな型を作ってください

```ts
type PropNullable<T> = {[P in keyof T]: T[P] | null};

interface User { name: string, age: number, money: null }

const obj:PropNullable<User> = { name: "kenji", age: 99, money: null }
```

**問13**

こちら

 ```ts
let createObj = (obj) => {
  let o = {}
  for(const key in obj){
    o[key] = String(obj[key]);
  }
  return o;
}

const anotherFun = createObj;
```

のcreateObj型を定義してください

 ```ts
let createObj = <T extends unknown>(obj:T) => {
  let o = {} as {[P in keyof T]: string}
  for(const key in obj){
      o[key] = String(obj[key]);
  }
  return o;
}
const anotherFun = createObj;


////or////
let createObj = <T, >(obj:T) => {
  let o = {} as {[P in keyof T]: string}
  for(const key in obj){
    o[key] = String(obj[key]);
  }
  return o;
}
 ```

 [playground](https://www.typescriptlang.org/play?ts=3.8.3&ssl=15&ssc=6&pln=9&pc=4#code/ATA2FMBdgYwJ3AQ0uA8gIwFbALzADwAqANAHwAUA9lgFyECUupwA3gFAicTSW6sC+wRAGdWAbQAKwAJYA7YAGtwAT0oAzYIQC6NYMMhw5Ac34dOwNZTjkYlWfsUqZ86pnrtz5ymKXKtfAGUDYyosHxUtegBuM05TTwRIAFc4FxiQUzYAeiywKFgEZDQsPiJgcAAPFFkAE1Ek2QVZSgB3WQpXOkYcZnYcz25gXjwWQRFxKTlHVQ1tXX1DWRNs3PNLa1t7aF9nIax3Fc9Ob19-PCDFo1DMcL9ow-NTfvNElLSH002HRGbIAAtwHAAGINPjwJAoDCYIA)

 **問14**
 TODO

 ```ts
 neverはunion型の中では消えるを問題にする
 ```

 **問15**
 こちらの

 ```ts
 arr(["a", 1]);
```

どんな要素の配列が渡されてもいいような型を作ってください。

 ```ts
 let arr = <T extends any[]>(...rest: T) => {
    return rest
}

arr(["a", 1]);
```

**問16**

`widening`とはなんですか説明してください。

```ts
// 型推論によってリテラル型を変数に代入した際にプリミティブ型に拡張されること
// 例えば、

let a = "a" //string

// constでは再代入はできないので`a`型というリテラル型になるが、letは再代入可能なので推論は拡張されプリミティブ型になる

let a: "a" = "a" //"a"

// このように型注釈をつけることでa型というリテラル型になる(型注釈はwideningより優先される)
```

**問17**

下記

```ts
let a;

if (Math.random() < 0.5) {
    a = 123;
}

console.log(a); // a は number | undefined 型
```

aがunion型になるのはなぜか

a宣言時に初期化されていない & 型注釈されていないことでif文がtrueになるまでundefined、その後aを参照すると`number` と `undefined`の可能性があるから。
初期化なし、型注釈なしの変数はコンテキストによって型が変わる(アンチパターン)

**問18**

WIP

**問19**

こちら

```ts
let a = 1
const num = a && "hoge";
```

型推論は何ですか

```ts
0 | "hoge"
// a && b is equivalent to a ? a : b
// aはnumberで、それがfalseになる場合は0。なので 0 | "hoge"
// 仮にaの初期値が""(空文字)の場合、stringなので、falseになる場合は、"" | "hoge"になることを確認してください
```

**問20**
type narrowing(型の絞り込み)とはなんですか

```text
基本的にはUnion型の型情報をどれかに絞り込むこと。
コンパイル時のタイプチェッカーに実行時の値に関する型情報を伝えること
```

絞り込む方法はいくつかあります

- Type predicates
- Instanceof Narrowing
- typeof Narrowing
- Subtyping


[Type Narrowing in TypeScript](https://medium.com/@jackhmwilliams/type-narrowing-in-typescript-44a6757c5a64)
[Widening and Narrowing in Typescript](https://sandersn.github.io/manual/Widening-and-Narrowing-in-Typescript.html)

**問21**
- 「return文はあるけどreturnせずに関数が終了する場合がある」 ->  例: string | undefined
- 「return文がない」 -> void
これらの使い分けを教えてください

voidは返す値をコンパイラに「無視してください」と伝える場合に使います。
実際には、runtime時、undefinedとして値は返っています。

undefinedはundefinedという意味のある値を返し、呼び出し元で使う場合

[72 なぜ TypeScriptはvoid とundefinedを区別しているか](https://kenjimorita.jp/typesript-check-108/)

voidはその真偽を評価させることはできないところがundefinedとの違い
functionのcallbackで使う場合promptやalertなど実行するのみで何も返さない型のreturn型にvoidではなくundefinedを指定すると、明示的にundefinedを返さなくてはいけない。voidに指定すると、返す型に「意味がない」、「単に無視して」とTSに伝えることができる

[playground](https://www.typescriptlang.org/play#code/MYewdgzgLgBAZiEMC8MBuICWATGAGAbhgHpiYBXMbAUzkzGuwChRJYAjAQwCcV5LgUTOAAUASgDeAXyKl0WZq2gwuALz5du42WQw4gA)

**問22**
[contextual typeがある状態](https://qiita.com/uhyo/items/6acb7f4ee73287d5dac0)というのはどういう状態のことですか

```ts
TypeScriptは引数の型注釈で型を宣言しなくてはいけない、が、

型推論の時点で期待される型があらかじめわかっている状態(contextual tyipingがある状態)なら書かなくても良い

type Func = (arg: number) => number
const double: Func = function (num) { return num * 2}

numは本来型注釈を書かなくてはいけないが、文脈からdoubleに入る関数はFunc型でなくてはいけないことがわかっている。
これがcontextual typingがある状態

・文脈からわかる型。
・関数の引数や戻り値を文脈から推論させること。
・型推論の時点で期待される型があらかじめ分かっている場合を「contextual typeがある」という

これは以下のときに重要になる
・callback関数を別の変数に割り当てると、contextual typeがなくなる
・型引数を明示しない時、型推論が推論される順番を理解するとき

contextual typingが発生する場面
- 変数の型注釈によって型推論中の式の型があらかじめわかっている場合
- 関数引数のcontextual typing
- 関数の返値のcontextual typ

```

**メモ**

型引数の推論について
実行時に型引数を省略すると
TやRといった型引数も一緒に推論される
この場合引数から推論される

```ts
function apply<T, R>(value: T, func: (arg: T) => R): R {
  return func(value);
}

// res は string 型
const res = apply(100, num => String(num ** 2));
```

- 呼び出し時仮引数から受け取り側の引数の型が決まる
- 引数の型が決まってから関数の型が判明する

引数の型より型変数の推論ができていないといけない
|
型引数の推論と、引数で関数を渡す場合のその引数の型推論はどのような順番で解決されるか

contextual typingが必要な引数だけ後回しにする

`apply(100, num => String(num ** 2))`

1. contextual tyipingが不要な引数を先に型推論。100に対して型推論。numberを得る
2. 得られた情報からTがnumberに推論される
3. contextual typingが必要な引数を型推論する。 num => String(num ** 2)に対して型推論。 (num: number) => stringを得る
このときcontextual tyingは `(arg: T) => R`型だが`T`は判明しているので (arg: number) => R
4. 再び型引数を推論する型引数 `R`が`string`に推論される

型引数`T`と`R`の推論結果が決まるタイミングが異なる

Tの型引数の推論結果はその型引数が使われた時点で確定する

[型推論結果は処理の順番に依存する](https://qiita.com/uhyo/items/6acb7f4ee73287d5dac0)

オーバーロードシグネチャ

**問23**

こちらのコード

```ts
type MyObj = {
  name?: string;
}

function foo(obj: MyObj): string {
  return obj.name!.slice(0, 5);
}
```

の `!`の意味、危険性について説明をしてください。

**問24**

こちらの

```ts
function isStringArray(obj: unknown): obj is Array<string> {
  return Array.isArray(obj) && obj.every(value => typeof value === "string");
}

function foo(obj: unknown) {
  if (isStringArray(obj)) {
    obj.push("abcde");
  }
}
```
`obj is Array<string>`の説明をしてください

```ts
このように返り値をobj is Array<string>のように宣言している関数は
真偽値が返らなくてはならず、
isStringArray関数の返値がtrueならobjは`Array<string>型`が返ることを指定しています。
```

**問25**

こちらの

```ts
(num: number) => { // 'num' is declared but never used.
  return "return";
}
```

'num' is declared but never used.をdisableしてください

```ts
(_num: number) => { // ok, _ pre
  return "return";
}
```

**問26**

WIP enumの使い方 1

```ts
enum Weekend {
  Friday = 1,
  Saturday,
  Sunday
}

function getDate(Day: string): Weekend {
    if ( Day === 'TGIF') {
        return Weekend.Friday;
    }
    return Weekend.Saturday
 }
let DayType: Weekend = getDate('TGIF');


// string enum
enum Weekend {
  Friday = 'FRIDAY',
  Saturday = 'SATURDAY',
  Sunday ='SUNDAY'
}
const value = someString as Weekend;
if (value === Weekend.Friday || value === Weekend.Sunday){
    console.log('You choose a weekend');
    console.log(value);
}

// enumを使用するのが最適かつ非常に効率的な場所と適切な使用例があります

// 列挙型は、他のTypeScriptデータ型と同じように、配列の初期化内で使用できます。
// これは簡単な例です。

enum NigerianLanguage {
  Igbo,
  Hause,
  Yoruba
}

//can be used in array initialisation
let citizen = {
  Name: 'Ugwunna',
  Age: 75,
  Language: NigerianLanguage.Igbo
}

列挙型は理想的には、週の7日のように、定数と見なすことができる明確な値がある状況で使用されるべきです。
enum Days {
  Sunday = 1,
  Monday,
  Tuesday,
  Wednesday,
  Thursday,
  Friday,
  Saturday
}

// 列挙型は、文字列または定数を変数で表す必要がある場所でも使用できます。

// TypeScriptの列挙型は、次の場所では使用しないでください。

// 列挙型メンバーの値を再割り当てまたは変更する予定の場合、enumは型保証されているため、再割り当て時にコンパイルエラーが返されます。
// 動的な値を記録したい場合、enumは有限項目に最も適しており、その背後にある一般的な考え方はユーザー定義の定数システムを作成するのを助けることでした
// 列挙型を変数として使用することはできません。そうするとエラーが返されます
```

**問27**

WIP unknown type

```ts
```

[unknown type](https://mariusschulz.com/blog/the-unknown-type-in-typescript)

**問28**

こちらのエラーをnumberとstringに対応できるように修正してください。

```ts
function eachItem(val: number, i: number) {
    return val.toExponential(3);
}
const arr = [4, "fafa", 6];
arr.map(eachItem);
```

**問29**

こちら

```ts
function fa(callback, e){
  return callback(e);
}

const fun = (e) => 1 * e;
const v = fa(fun, 1);
```

`Parameter 'callback' implicitly has an 'any' type.` と `Parameter 'e' implicitly has an 'any' type.` に対応してください(callbackに型付けしてください)

```ts
interface Fun {(e: number): number;}

function fa(callback:Fun, e: number){
  return callback(e);
}
const fun:Fun = (e) => 1 * e;
const v = fa(fun, 1);
```

**問30**

こちら

```ts
type YukarinoChi = "tokyo";
type OnlySpecificProperty<T> = Pick<T, {[K in keyof T]: T[K] extends YukariNoChi ? K : never}[keyof T]>;
```

の型を説明してください

```ts
// "tokyo"リテラル型を値としてもつプロパティだけを抜き出した型を定義しています

type YukariNoChi = "tokyo"

const obj = {
    name: "kenji",
    age: 99,
    born: "tokyo",
    live: "tokyo"
}
type Obj = {
    name: string,
    age: number,
    born: YukariNoChi,
    live: YukariNoChi
}

const obj2 = {
    born: "tokyo",
    live: "tokyo"
} as const

type OnlySpecificProperty<T> = Pick<T, {[K in keyof T]: T[K] extends YukariNoChi ? K : never}[keyof T]>;


function fun(onlyYukari: OnlySpecificProperty<Obj>){
    return onlyYukari
}
const result = fun(obj2); // Pick<Obj, "born | live">

```

[playground](http://www.typescriptlang.org/play/index.html#code/C4TwDgpgBAmgrgawIYCcCWA5A9gYQBZpQC8UARMFgiFqQFC0DGWAdgM7BRYBGAVsVAG9aUEVGZIAthABcZBBGY80pADTDRSAOYyoATl1rRULlhTNZ5StVXqRAGzQA3HZao1aAX1qhIUAPK8-EJG4lKy7OjMmoYa2rLMcBJcECgxIiZmsvDI6Nj4aGlQDs5ZiKiYuASe9ExsHNw8AExBtsam5mQUbjZGxS5d1p5QSKxQtez0PtB+zHYg2eUAPAAqAHz8AApoDAgrKoIA2gDSUGjMUPLUAGZQywC6ssvHd1AQAB7ACgAmowu5lYQAPxQE7xCDOFAeA6XLA3e6rADc9FoVzgzAYwDQLCgqOYAAoWHM-mhZDMiWV0IsAjxVgBKYKiFAQYBwMycWbzCloarjDhM1hwOwcEi4gm8Rq0hFAA)

**問31**

stringとnullableな配列の型を作ってください

```ts
let arr: (string | null)[] = []
```

[playground](http://www.typescriptlang.org/play/index.html#code/MYewdgzgLgBAhgJwQLgBTQQSzAcxgHxjAFcAbUgSgG0BdGAXhloFgAoNxBAOgAdiIAFqhLkKAbjahIIUgFMupEDlSdxQA)

**問32**

こちらの

```ts

type F = {
    foo: string;
    bar: number;
}
const E:F = { foo: "fafa", bar: "fafa"} //Error
```

定義元のFを直接編集せずに代入できるように型付けしてください

```ts
type F = {
    foo: string;
    bar: number;
}

const E:Record<keyof F, string> = { foo: "fafa", bar: "fafa"}
```

**問33**

`type Exclude<T, U>` の説明をしてください

```ts
ExcludeはTがUに代入可能ならnever、そうでない場合Tを返すconditionalTypeです

use case
type Q = Exclude<string | number, boolean | string | number>
//boolean

type Q = Exclude<string | number | undefined, any>
// never
```

**問34**

こちら

```ts
export defaut function person({ detail } : Person) {
  return <div>{detail.name}</div>
};

interface Person {
    id: number
    detail: Detail
}

person({detail: {name: "fafa"}, id: 1 });
```

はdetailが初期化された時 `undefined`が渡って来てもいいように対応してください

```ts
export defaut function person({ detail = {}} : Person) { // error
  return <div>{detail.name}</div>
};
// このままだと
// Property 'name' is missing in type '{}' but required in type 'Detail'.
// になります

defaultaParameterを設定し、アサーションします

export defaut function person({ detail = {} as Detail} : Person) {
  return <div>{detail.name}</div>
};
```

**問35**

reactでsetStateをする際に

```ts
interface State {
 name: string
 age: number
}

this.setState({name: "kenji"}) // Error
this.setState({name: "kenji", age: this.state.age}); // ok
```

このように特定のState.propertyのみを渡すとエラーになる

全てのpropertyを渡さないでもいいようにしてください。

```ts
interface State {
  name?: string;
  age?: number;
}
```

**問36**

こちらは

```ts
interface User {
  id: string
}
interface AppUser {
  appName: "appName"
  appID: string
}
interface ServiceUser {
  serviceName: 'serviceName'
  serviceID: string
}
const user = {id: "1"}
const appUser = { appName: "appName", appID: "appId"} as const;
const serviceUser = { serviceName: "serviceName", serviceID: "serviceID"} as const


function a(o: ServiceUser | User | AppUser){
  return o
}

const result = a(user)
```

関数 `a` は`ServiceUser` or `User` or `AppUser`を`a` に渡してそれを返す関数です。
期待型は `ServiceUser | User | AppUser` になっています。
これを それぞれ `ServiceUser` は`serviceID`、`User`は`id`、`AppUser`は`appId`を返す関数に直して、 期待型を`string`にしてください

```ts

function a(o: ServiceUser | User | AppUser){
  if("serviceID" in o) return o.serviceID;
  if("appID" in o) return o.appID;
  return o.id;
}

const result = a(serviceUser) // string
```

[playground](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgKoGdrIN4ChkHLAAmAXMumFKAOa4C+uoksiKAggA6cZZ6HI43AHJwAthHIAiIZ1ESp+QrICSAEXKVqIOo2bR4SZAGVoAN2BJeUHEoKYoFpPMnIA5A6cQXbuxXOWEOqaVLQMuAgA9iCUyACuDsgAvDgk0gCMUoxRMWCC3NbJOPly4q4yImVSADQlwchSEE1ZgujIOZQA3BHRsZ6BhSnY-o6BLtL9zlW1k0EaDegtcG0dYLjrMHEgCGDA0YIAFJHkpqNWiQA+aJfIXDwOAJT8hMAwB1Kz6lJEIMiRD8goBAwHEoL9IgA6T5qboCV7vVRqb6gP4AoEgsF-CGI2GEdGg8EQkjdRg9XKAiDoOIAGzyKTgB1m1geQA)

**問37**

WIP 問題文。

上の問題の

```ts
function a(o: ServiceUser | User | AppUser){
  if("serviceID" in o) return o.serviceID;
  if("appID" in o) return o.appID;
  return o.id;
}
```

を
独⾃定義 TypeGuardで型定義してください。(それぞれ `isService`、`isAppUser`、任意で`isUser`関数を作り、ifのコンディション内で実行。返す値がそれぞれのプロパティを持つようにして、型付けされていることを確認してください)

```ts
const isService = (o: any): o is ServiceUser => {
    return o.serviceID === "serviceID";
}
const isAppUser = (o: any): o is AppUser => {
    return o.AppUser === "appUser";
}

type O = ServiceUser | User | AppUser;
function a(o: any){
    if(isService(o)) return o.serviceID;
    if(isAppUser(o)) return o.appID;
    return o.id; // User
}
const result = a(serviceUser)
```

[playground](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgKoGdrIN4ChkHLAAmAXMumFKAOa4C+uoksiKAggA6cZZ6HI43AHJwAthHIAiIZ1ESp+QrICSAEXKVqIOo2bR4SZAGVoAN2BJeUHEoKYoFpPMnIA5A6cQXbuxXOWEOqaVLQMuAgA9iCUyACuDsgAvDgk0gCMUoxRMWCC3NbJOPly4q4yImVSADQlwchSEE1ZgujIOZQA3BHRsZ6BhSnY-o6BLtL9zlW1k0EaDegtcG0dYLjrq0TopqNGKQAUkeRwIACeAJTkkVsmAVaJSQB8tgJQEGBxUCDIkQB0s+pkkkUlIAWopN1sr08sB0FweA9kIdjmdLj8bvDBs9+IQ3h8vj9fpiHsCGrJrBDwrgwKdOCgAPJFHZeQoAHzQiXZxOg3RgcRACDAwGigmRglROIIwBg+1hzMCh3O52QeM+3z+YO6AmlsrhBQciuVqoJf1Uai1uPeasJJEhG2hKog6DiABs8ik4PtZtZzkA)

**問38**

こちら

```ts
const o = { name: "hoge" }
function a(o){
    return o
}

a(o)
a();
```

の defaultValueとany型に対応してください

```ts
const getDefaultProps = () => {
    return {name: "hoge"}
}
const defaultProps = getDefaultProps();
const o = {name: "hoge"}
function a(o = defaultProps){
    return o
}

a(o)
a();
```

[playground](https://www.typescriptlang.org/play/#code/MYewdgzgLgBA5gUygEQQMwIYFcA2UAKATiAA4QwC8MAFAJSUB8MA3gFAwcyFJaFgtgMAWwQAuGACIAFiEQSAvq0WhIsACbpseIqXJVEKTbgLEydANysV0GCEoDhYyTLmK0WMMCgBLcDAzUdlQamMY6ZLRsnFw8fLZKrKwBILRJFkA)

**問39**

こちらは

```ts
type Animal = { name: string, run: boolean }
type Bird = { name: string, fly: boolean }
const animal = { name: "tigger", run: true }
const bird = { name: "condol", fly: true }

type NotHumman = Animal | Bird

const a = (b:NotHumman) => {
    b.run
}
```

なぜコンパイルエラーになるのですか？説明してください

```
// NotHummanはAnimal型かBird型の可能性があるので、それを区別してからではないと一方にしかないproperyへのアクセスはできません。
// 型を確定後に参照する必要があります
type NotHumman = Animal | Bird

const b = (b: NotHumman) => {
    if ("run" in b) { // animalであることが確定する
        b.run
    } else {
        b.fly
    }
}
```

**問40**

こちら

```ts
declare function beforeAll(action: () => void, timeout?: number): void;
declare function beforeAll(action: (done: DoneFn) => void,timeout?: number): void;
```

コールバックに渡す引数の数が違うのでオーバーライドしてあります。修正してください

```ts
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;

// コールバックの引数が違うだけでオーバーライドしないようにしましょう。
// コールバックがパラメータを無視することは常に正当です。渡って来なくても無視されるだけです。
```

**問41**

こちら

```ts
declare function fn(x: any): any;
declare function fn(x: HTMLElement): number;
declare function fn(x: HTMLDivElement): string;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: any, wat?
```

修正してください。

```ts
declare function fn(x: HTMLDivElement): string;
declare function fn(x: HTMLElement): number;
declare function fn(x: any): any;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: string, :)

// TypeScript は関数呼び出し時に最初にマッチしたオーバーライドを選ぶので、any だと最初に必ずマッチしてしまう
```

**問42**

こちら

```ts
interface Example {
  diff(one: string): number;
  diff(one: string, two: string): number;
  diff(one: string, two: string, three: boolean): number;
}
```

修正してください

```ts
interface Example {
  diff(one: string, two?: string, three?: boolean): number;
}

//　返る型が同じ場合、可能な限りオプショナルを使いましょう。
```

**問43**

こちら

```ts
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

// 1
y = x;

// 2
x = y;
```

`1`と`2`はそれぞれエラーになりますかなりませんか

```ts
y = x; // ok!
x = y; // error

//返る型が同じ場合、引数の数は関係ない。代入元が代入先の引数を持っているかどうか。

// example
type F = (name: string, n: number) => void;
let f: F = (value: string) => {
  //実装は使わないでもokだが
  console.log("here");
};
f("how", 2); //渡す際に満たさないといけない,
```

**問44**

こちら

```ts
let x = () => ({ name: "Alice" });
let y = () => ({ name: "Alice", location: "Seattle" });

// 1
x = y;
// 2
y = x;
```

`1`, `2`はそれぞれエラーになるかならないか

```ts
let x = () => ({ name: "Alice" });
let y = () => ({ name: "Alice", location: "Seattle" });

x = y; // OK
y = x; // エラー。xの戻り値には location プロパティがない

// 代入元の返り型は代入先のプロパティを含んでいないといけない
```

**問46**

こちら

```ts
let identity = function<T>(x: T): T {
  // ...
};
let reverse = function<U>(y: U): U {
  // ...
};
identity = reverse;
```

は代入できるか。それぞれ`T`と`U`の型は何か

```ts
 // OK。anyになります。
 (x: any)=>any は (y: any)=>any と互換性がある
 ```

**問47**

こちらは呼び出すことができません。

```ts
type StrFunc = (arg: string) => string;
type NumFunc = (arg: number) => string;

declare const obj: StrFunc | NumFunc;
obj("fa"); // Argument of type 'string' is not assignable to parameter of type 'never'.
```

なぜですか

```ts
// objの型はStrFuncかNumFuncの型であり、それぞれの引数の型が違うためどちらの関数が呼び出されてもいいようにどちらの引数にも対応できる型を渡す必要があります


type StrFunc = (arg: string) => string;
type NumFunc = (arg: number) => string;
type StrOrNumFunc = <T>(arg: T) => string

declare const obj: StrOrNumFunc
obj("fa");

// or

(obj as StrFunc)("fa"); // unnn...
```

**問48**

こちらは

```ts
interface MyObj {
  name: string;
  age: number | undefined;
}

let obj: MyObj = {
  name: "kenji"
};
```

Errorになります。なぜですか。また正しく修正してください

```ts
interface MyObj {
  name: string;
  age?: number | undefined;
}

let obj: MyObj = {
  name: "kenji"
};

// オプショナルを使わない場合はプロパティは存在はして居ないといけません。

let obj: MyObj = {
  name: "kenji",
  age: undefined
};
// なら可能
存在もして居ない場合は`?`を付与すること。
```

**問49**

TypeScriptで`console.log`を呼び出してもコンパイルエラーにならないのはなぜですか?

```ts
//https://docs.solab.jp/typescript/ambient/declaration/

TypeScript では console.log などを呼び出してもコンパイルエラーにはなりません。 
これは、TypeScript コンパイラがデフォルトで lib.d.ts という宣言ソースファイルを利用しており、
lib.d.ts には次のようなアンビエント宣言が記述されているためです。

// lib.d.ts
declare var console: Console;
```

**問50**

こちらは

```ts
interface Foo {
  name: string;
}
let obj: Foo = { name: "kenji", age: 90 };
```

なぜコンパイルエラーなのですか？ `{ name: "kenji", age: 90 };`が代入できるように修正してください

```ts
// オブジェクトリテラル型はFooが知っているプロパティのみ代入可能です。ageは知りません。
// これを回避するためにはオブジェクト型にすることです。

interface Foo {
  name: string;
}
const other = { name: "kenji", age: 90 };
// otherで推論が下記のように変わる
const other: {
  name: string;
  age: number;
}
let obj: Foo = other;

// or

//何が入ってくるかわからない場合
interface Foo {
  name: string;
  [other: string]: any; //here
}

let obj: Foo = { name: "kenji", age: 90 };
```

**問51**

こちらは

```ts

let foo:any = {}
foo["a"] = { message: "some message"};
```

fooにanyを注釈しています。インデックスにstring、 値に代入しようとしている型を指定してください

```ts

let foo:{ [index: string]: { message: string }} = {}
foo["a"] = { message: "some message"};

```

**問52**

こちらは

```ts
const tupleStrNum = ["X", 2];
```

型推論で(string|number)[]になります。

`[string, number]` とするにはどうしたらいいですか

```ts
const tupleStrNum = ["x", 2] as [string, number];
//const tupleStrNum: [string, number] = ["X", 2];
```

**問53**

こちらを

```ts
interface SomeObject {
    firstKey:   string;
    secondKey:  string;
    thirdKey:   { id: { name: string} }
}
```

再帰的に各プロパティをオプショナルにした型を定義してください

```ts
type RecursivePartial<T> = {
    [P in keyof T]?: RecursivePartial<T[P]>;
};

const b: RecursivePartial<SomeObject> = {}
b.thirdKey = {}

/// 一部のプロパティをのぞいて再帰的にオプショナルにする型
type PartialExcept<T, K extends keyof T> = RecursivePartial<T> & Pick<T, K>;

//ok
const a: PartialExcept<SomeObject, "thirdKey"> = {
    firstKey:   "",
    secondKey:  "",
    thirdKey:   { id: { name: {}} }
}
// ok
const b: PartialExcept<SomeObject, "thirdKey"> = {
    thirdKey:   { id: { name: {}} }
}
// error
const c: PartialExcept<SomeObject, "thirdKey"> = {}
```


**問54**

プロパティ`name`の値型が`string | null`、ageの値型が`number | null`の型`User`を定義してください

```ts
const d = {name: "kenji", age: 99}

type E = {name: string, age: number}
type User<T> = {[K in keyof T]: T[K] | null }

const e:User<E> = { name: null, age: null};
```

**問55**

`U`を`extends`している値`T`は`never`を返し、そうでない値型は`T`を返す`Diff`を定義してください

```ts

type Diff<T, U> = T extends U ? never : T;

const t1:Diff<"a" | "b", "b" | "c"> = "a";

```

**問56**

こちらの

```ts
const t3 = {name: "kenji", age: 99} as const
type T3 = keyof typeof t3
```

`T3`の型をおしえてください

```ts
//type T3 = "name" | "age"
```

**問57**

TODO

 ```ts
enum StatusEnum { RootAdmin = "RootAdmin", Admin = "Admin" }
type T2 =  Partial<Record<StatusEnum, number | null>>
const t2:T2 = { RootAdmin: 0 }
```


**問58**

こちらの

```ts
type User = { name: string, age: number}
const f = (a:User) => a
const a:F<User> = f({name: "kenji", age: 9});
```

を参照に、

もし関数型である引数を渡したらその引数が返ってくる型、関数型ではないなら関数が返ってくる`F<User>`を定義してください。

```ts
type F<T> = T extends (a: infer P) => any ? P : T;
type User = { name: string, age: number}
const f = (a:User) =>  a

const a:F<User> = f({name: "kenji", age: 9}); // User
const b:F<string> = "hello" //string
```

[playground](http://www.typescriptlang.org/play/#code/C4TwDgpgBAYgPAFQHxQLxQVCAPYEB2AJgM5QAUAhgFxQCW+AZhAE5QAKAlGihfiFAH52UGggDcAKAmhIUAKrEWaKAG8o+CgFsINYsGb0A5gBooFQzvUBXTQCMWAXykSAxgHt8eqA2WUqCli5UFDMpd09gMyp4AOYUdAYyFQ1tGgAiAGsCACtaNNNzSwBOBw4xKAB6CvlFZlcPL1touD0DfEN4qDSACwgAGz63NMqK1qNnIA)

**問59**

下記のような
```ts
type User = { name: string, age: number }
```
User型がある。こちらのvalueのUnion型を取得する型を定義してください。 `string | number`

```ts

type User = { name: string, age: number }
type Value<T> = T[keyof T]
type ValueType = Value<User> // string | number

// 別解
type Value<T> = { [K in keyof T]: T[K] }[keyof T]
type ValueResult = Value<User> // string | number

```

[playground](https://www.typescriptlang.org/play/?ssl=23&ssc=33&pln=18&pc=1#code/LAKFBcE8AcFMAICqBnWAneBeeBveA7AQwFtYAueZcNAS3wHMAaeQ+8ggV2ICN14BfUKAD0w+ACIipcfAA+E1rHFCQUOPADSsSCj7YA1toD2AMySoMK0ZWp16czj3Qq1CAGqEANh1gAeACoAfFi48ADaGvB08IaQpvD+ALoU-hGJAmGx8UkuMO5ePgBKsMgcnuAhHt5+umiBuepVPgDyJgHB2KlZZjlgqnnwTbCtxaXllQXDbbX1fSpAA)



**問60**

こちらの型

```ts
type User = { name: string, age: number, id: number }
```

の値の型がnumberのものだけを抽出した型を作ってください。 期待する結果 `{age: number, id: number}`

```ts
type User = { name: string, age: number, id: number }

type Value<T> = { [K in keyof T]: T[K] extends number ? K : never }[keyof T]

type NumberType = Pick<User, Value<User>>
```

[playground](https://www.typescriptlang.org/play/#code/C4TwDgpgBAqgzhATlAvFA3lAdgQwLYQBcUcwiAllgOYA0UOVR2ArngEZJ3kAmxWrHZAF8AsAChxoSFABqOADbMIAHgAqAPlQYoAbQDSUSlADWEEAHsAZlFUBdYqv22oEAB7AIWbnBbskUAH4oAz4IADd-IR1TC2s7cUlwaAA5ASRVJK0ABXIAY2NleE5ZBSVChER1dSA)

**問61**
下のようなコードがあります

```ts
const isNarrowScreen = () => false
export function wideNarrow(wide: number | string | undefined, 
    narrow:number|string|undefined){
    return isNarrowScreen() ? narrow : wide;
}

const a = wideNarrow(0, 8)
const extendedAreaHeight = 26;
const b = a + extendedAreaHeight // Operator '+' cannot be applied to types 'string | number' and 'number'.

```

上の場合unionTypeなため`+`で加算しようとするところでエラーになります。こちらを渡ってきた型を推論するようにしてください

```ts
const isNarrowScreen = () => false
export function wideNarrow<T>(wide: T,
    narrow:T){
    return isNarrowScreen() ? narrow : wide;
}

const a = wideNarrow(0, 8)
const extendedAreaHeight = 26;
const b = a + extendedAreaHeight
console.log(b)

```

[playground](https://www.typescriptlang.org/play/#code/MYewdgzgLgBAlhAcgQwE6pAdwMrFQU3zBgF4YAKASlID4YAzZAGwnwFgAofADwAcRUsegFcwwKHHAxMcACb4U6LAB4AKjXIz5ALhiqANDE4wTMMGgyZtqygG9jpglGGpiCRZdwEiVGAH4zCywYXS18AG5OAF9OTlBIWGRSaTkFIMxyAAZDAA5KOPBoGB4oInlZAEECZAAJfDgAcwALWDIAJgA2SI54ooAjZKSAamLuUrByqvxa+uaoAsgQJnwAOiYQBvI+-I4gA)

**問62**
こちらは

```ts
function add<T extends (number | string)>(a: T, b: T): T {
 if (typeof a === 'string') {
  return a + b;
} else if (typeof a === 'number') {
 return a + b;
 }
}
```

[playground](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABAQwCaoDwBVEFMAeUuYqAzogBRggC2ARrgE6IA+ipUjMYA5gJQA+CsgBciLABpEdMVj6zEAbwCwAKACQMYJSgBPAA64425IgC8FxAHIOXXlb5K169Y1xQQjJKYDU0gNzOAL54ADakuIhaOgZGJuaWVtT0TA5OGq7unt6IfnSBGkFqRapAA)

numberかstringが渡ってくることを想定して作られた関数です。
2つは同じ型の引数を取るため、型を`T`として使おうとしています。
ただ、コンパイルエラーになっています。正しく修正してください

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

関数の中のgeneric parameterの型は絞り込むことはできない。なのでaをテストした時、bをコンパイラに伝えなくてはならない。さらに返値の方もコンパイラに伝えなくてはならない

[playground](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABAQwCaoDwBVEFMAeUuYqAzogBRggC2ARrgE6IA+ipUjMYA5gJQA+CsgBciLABpEdMVj6zEAbwCwAKACQMYJSgBPAA64425IgC8FxAHIOXXlcQAyR4j2Hj085YBEt7j28+JTV1dUZcKBBGJFMAak9kciwQgF88ABtSXEQtHQMjEy8za2p6JgdnV3yPOiLEb1KGRkDgjTCIqJjEeNrE8VSQqAALRjgAd0QAUUZRxgoGuChEAGUQfX04RihAtRS1IA)

**問63**
下記のような

```ts
const values = ['A', 'B']
type Foo = OneOf<values>

const v1: Foo = 'A' // ok
const v2: Foo = 'D' // error
```

[playground](https://www.typescriptlang.org/play/index.html?ssl=5&ssc=20&pln=5&pc=86#code/MYewdgzgLgBAbgQwDYFcCmEYF4YG0DkAgvgDQz4BC+AugLABQUAngA5owBiII2MA8mDR8AZgB5EqDAD4YAelkwAkpigALNACd2CGAHcETGCGEwAJiACWYAOYw1FiAH4GDUJFhwAjAC5O3XkT4cgoAKqoOMBCqIChIpnogGgDWruDQ8ABMvlw8OPgAIkHyMGERUTFxMNYWcOwAttpgMJoaiZFWwOwFQeYYYPiwaAAeDrBW8MjoEEA)

配列の各要素(string)のどれかを割り当てることができるUnion型が返る`OneOf`を定義してください


```ts
function stringLiterals<T extends string>(...args: T[]): T[] { return args; }
type OneOf<T extends unknown[]> = T extends (infer R)[] ? R : never;

const values = stringLiterals('A', 'B');
type Foo = OneOf<typeof values>;
```

リテラルなstring、`("A"|"B")[]`にする必要がある。
`1` の方は一度関数を通してそれを得て、 `OneOf`の方で要素の型を取得している
`2` の方はReadOnlyしか受け付けないOneOf。constでReadonlyにしてから渡すとその要素を受け取れる
どちらも `"A" | "B"` が返る

```ts
// 1
function stringLiterals<T extends string>(...args: T[]): T[] { return args; }
type OneOf<T extends unknown[]> = T extends (infer R)[] ? R : never;
const values = stringLiterals('A', 'B'); // ("A" | "B")[]
type Foo = OneOf<typeof values>; // "A" | "B"

or

type OneOf<T extends ReadonlyArray<unknown>> = T extends ReadonlyArray<infer ElementType> ? ElementType : never;
const values = ["A", "B"] as const // readonly ["A", "B"]
type Foo = OneOf<typeof values>; // "A" | "B"
```

[playground](https://www.typescriptlang.org/play/index.html?ssl=8&ssc=1&pln=9&pc=1#code/GYVwdgxgLglg9mABAZygJxmA5gGRlAUzQEMAbZAHgBVECAPQsAE2RXUywD4AKAOn+JosyAFyIqAbQC6ASjGSpiAN6I0BKCDRJBwgNyIAvgFgAUFACeABwKIA8mAK3g1WgwLNW4ANZg4AdzBpTkQAXnFXRhZEbkxgIkQAJRlpRAB+RMQxBwA3Il1TUwB6QsQIBFREbLIQAlYwiQAiAEEGgBpEBoAhBsVUDGxpQGsGQCsGQGMGQDMGQHUGQD8GQDEGIpKysAqq0hq6xEaW9q6exGJWJdQF8QALGwtrRAByNWImBFJzTea2ju6pa8QYVluCe8e5i+xGYpRBvigiAARjYDsgYFgHExEFA4CjzogALYgKDEKGkC5WGzXby+ALSL53B5gJ4oomAewZfGAALRU5mAunWQaAJyVAFEMgGV9caASIZADEMw0AIgwnQDKDCLAEkMgEYowCaDIN6fZHMBZQl-kx7E9BorANEMUsAFgwnS42NVOFz0SKsLUAmnmJpoEjmCik-xgTjBMI0G3uKL26lPZ2uiixeIAUQJmPcUCoROC6WjBFjYHjRMyiByeROxVK5Uhq3WoRe23eewOBeWUBORyL1Vqpb6HDwhBI5G41ya13a1061xk+RM+fNiAAYnA0WFLc5zXBgJVG8hOMOzZnZ9a3B5EtrAaHiG6PQFvaW-dvA3vHQe3RG0IgU2mM9Ykw+Y3GE1csgRcmhh-WlzWJswhbbA2yIMhkG4V4dm6GRTDHSdpzsBwrXnRdi1qVcgA)

**問64**
こちら

```ts
const a: Record<string, string> = {
    doorToDoor: "delivery at door",
    airDelivery: "flying in",
    specialDelivery: "special delivery",
    inStore: "in-store pickup",
};
const aa = a["name"] // stringが入ってしまう
```

`a` に割り当てられたオブジェクトのプロパティ以外を参照できないようにしてください

```ts
プロパティ名のUnionTypeを作る必要があります

const source =  {
    doorToDoor: "delivery at door",
    airDelivery: "flying in",
    specialDelivery: "special delivery",
    inStore: "in-store pickup",
};
const a: Record<keyof typeof source, string> = source
const aa = a["name"] // error
```

**問65**

こちらの
```ts
type A = { name: string }
type B = { age: number }
```

AとBのkeyであるnameとageを合わせたtype、
`name | age`となるUnion型を作ってください

```ts
type A = { name: string }
type B = { age: number }
type T1 = keyof (A & B)
```

**問66**

こちらの

```ts
type MyUnionType =
  | { foo: 'a', bar: 1 }
  | { foo: 'b', bar: 2 }
  | { foo: 'c', bar: 3 }
```

型を type Foos = 'a' | 'b' | 'c' このようになるようにしてください

```ts
type MyUnionType =
  | { foo: 'a', bar: 1 }
  | { foo: 'b', bar: 2 }
  | { foo: 'c', bar: 3 }

type FooType = MyUnionType['foo']
// FooType = "a" | "b" | "c"


//or

type PickField<T, K extends string> = T extends Record<K, any> ? T[K] : never;

type FooType2 = PickField<MyUnionType, 'foo'>
// type FooType2 = "a" | "b" | "c"
```

**問67**
こちらの

```ts
interface Foo {
    foo: number;
    common: string;
}

interface Bar {
    bar: number;
    common: string;
}

function foo(arg){
  return arg.foo
}

const result = foo({foo: 9});
```

関数fooは[現状](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgGIHt3IN4FgBQyRyMmAXMiAK4C2ARtANwHHILo03ogUDOYUUAHNm+AL4ECoSLEQoAQnCg4WxOkorV6TVUXadufAcNET8k-DCogEYYNxKYAFEqEBKPIWRQIYKlBBkVwA6UnQCMwJ2EH5vCF4qABswZABeR3QnbDCKAE4xN1EgA) `interface Foo`型を受け取り、 現状`Foo`が持つ`foo`を返すようになっています。(argはanyです)

この関数をfooAndBarと名前をへんこうして、Foo型が渡された場合はarg.fooを、Bar型の場合はarg.barを返すように 実装して、型付けしてください

```ts
interface Foo {
    foo: number;
    common: string;
}

interface Bar {
    bar: number;
    common: string;
}

function isFoo(arg: any): arg is Foo {
  return arg.foo !== undefined;
}
function fooAndBar(arg: Bar | Foo){
  if(isFoo(arg)){
    return arg.foo
  } else {
    return arg.bar
  }
}

const result = fooAndBar({ foo: 9, common: "fa" });
```

[playground](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgGIHt3IN4FgBQyRyMmAXMiAK4C2ARtANwHHILo03ogUDOYUUAHNm+AL4ECoSLEQoAQnCg4WxOkorV6TVUXadufAcNET8BGFRAIwwbsmC8M6ABRKhFOCACeASk9QQg68aJgqhMhQEGBUUCDI7gB0pFgAhAC86chWACYQMKAQOaYWVjZ28SkAgiA5ilBugRT1yAA+oei+eBHAMC6Ozo1Cvl26kdGx8UkpqmLIEAA2vCjdrFExcQmBiepQswRmBOwg-OO8VAtgyFnVtfUu2CkUAJwANGwcXDzIAETwP2JfKIgA)

**問68**
こちらは

```ts
interface NumberMap {
    [key: string]: number;
}
const map: NumberMap = {
  one: 1,
  two: 2,
  three: 3,
}

// no error, but incorrect, this key does *not* exist
const lol = map.weoiroweiroew;

// Also cannot do this
// 'map' refers to a value, but is being used as a type here.
type MyKeys = keyof map;
```

現状mapに割り当てられている実際のproperty以外のプロパティを受け入れてしまっています 実際に渡したもpropertyをもつ型のみを受け入れるようにしてください(map.weoiroweiroewをエラーにししてください)

また type MyKeys = keyof map;を期待する結果であるone | two | threeにしてください

```ts
interface NumberMap {
   [key: string]: number;
}

function careteMap<T extends NumberMap>(v: T){
 return v
}

const map = careteMap({
 one: 1,
 two: 2,
 three: 3,
})

const lol = map.weoiroweiroew;

type MyKeys = keyof typeof map;
```

[playground](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgHIFcC2AjaBZOAB2QG8BYAKGWuQG0BrCATwC5kBnMKUAcwF02ILLigBuSgF9K0ijHQgEYYAHsQyBHCgRIBQgB4AKsggAPSCAAm7NMPxEAfAAoAbmwMBKclWRaw6KGrOkjIIqpzImETIALzqmtoQuo5e1KoQbACMADSU1GAA7spsAEw53mAAFlrpyADMZRLuMs0UAPStyCDKxlBQylBZyNjoYMigob0QioOVwNaMTMgWyhDWAFRdYGvGJnNglKEg4QA2yscxEUQAdPkQysB9tw8r+eIUlO3IAILH7N0aIE2S26s3YlDATEIKDwTAA0sxrLEFsoYMgIVCUZdCKIgA)

**問69**

こちらは型エラーがでます

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAChBOBnA9gOygXigbwLACgopUBDAWwgC4pFh4BLVAcwBooSmriBXMgIwRt6AE2qpeA+CwIBfAgGM0tKBUw5SFagCISWthy4BGQ0NEBOCzIDcBAgHk+AKwjzgAOgDWEEIgAUFAEo3ADNkeABREnkAC18vEEwAPhwCIkVUFAAbCDdM5CZ-CABteIBdANkAoA)

```ts
type Person = {
  name: string, age: number, id: number,
}
const me = {name: "a", age: 11, id: 999};

Object.keys(me).forEach(key => {
  console.log(me[key]) // error
})
```

正しく修正してください

```ts

// コンパイラはmeがオブジェクトリテラルなことを知っていてそのkeyもまた具体的に "name" | "age" | "id"ということを知っています。keyはstringでかなり広範囲なものを差し、unionなそれらにアサインできません。この場合、keyが何かをより具体的に明示するする必要があります

type Person = {
  name: string, age: number, id: number,
}
const me = {name: "a", age: 11, id:999};

Object.keys(me).forEach(key => {
  console.log(me[key as keyof Pertion]) // ok
})


// see https://www.reddit.com/r/typescript/comments/edzgtw/help_no_index_signature_with_a_parameter_of_type/

// other interfaceに追加する方法 https://fettblog.eu/typescript-better-object-keys/
```

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAChBOBnA9gOygXigbwLACgopUBDAWwgC4pFh4BLVAcwBooSmriBXMgIwRt6AE2qpeA+CwIBfAgGM0tKBUw5SFagCISWthy4BGQ0NEBOCzIDcBAgHk+AKwjzgAOgDWEEIgAUFAEo3ADNkeABREnkAC18vEEwAPhwCIkVUFAAbCDdM5CZ-CABtePZEKHjkYNgEFFQAXQDZAKA)

**問70**

[こちら](https://www.typescriptlang.org/play?ssl=13&ssc=8&pln=13&pc=70#code/C4TwDgpgBAqgzhATlAvFA3gWAFBT1ASwBMAuKOYRAgOwHMd8oIBbAQwIBsyKq6H8wrOHADuAe0SlylGvWwBfHDlCRYCRAAVEYgGadoaeEigAyDFGqtmEbjLoAaKETEAjW71pRF2HDoCu1ADGwARi1ITU6sAAFH7qZEaIAJQY-HiBYXBiHBAAdBxitLHqSTjeODRR0ejEZABEAIx1jizsXFB1OqwAArRsnLkZzM1QgsLikvUADDMjltb1AEzLdfJJQA)は

```ts
type User = {
    id: string
    email: string
    password: string
}

type UserProfile = User & { name: string, dob: string }

function insert(user: User) {
    console.log(user)
}

insert({id: "1", email: "fa@gmail.com", password: "000", name: "222"})

```

過剰なプロパティチェックをしてくれているのでエラーがでますが、
[こちら](https://www.typescriptlang.org/play?#code/C4TwDgpgBAqgzhATlAvFA3gWAFBT1ASwBMAuKOYRAgOwHMd8oIBbAQwIBsyKq6H8wrOHADuAe0SlylGvWwBfHDlCRYCRAAVEYgGadoaeEigAyDFGqtmEbjLoAaKACsxAI1u9aURdhw6ArtQAxsAEYtSE1OrAABT+6mRGiACUGPx4QeFwYhwQAHQcYrRx6sk4PplRwFDxSFq6+qgYxGQARACMrY4s7FxQrTqsAAK0bJx5mcxdUILC4pJtAAzL05bWbQBMW9Mu7v0QdDQQSK0+NNElddp6uclAA)

```ts
const userProfile = {id: "1", email: "fa@gmail.com", password: "000", name: "222", job: "engineer"}
insert(userProfile)
```

のようにリテラルじゃないケースの場合TypeScriptは過剰なプロパティチェックをやめてしまいます

insertがUser型のみしか受け入れたくないように修正してください

```ts
type User = {
    id: string
    email: string
    password: string
}

type UserProfile = User & { name: string, job: string }

function insert<T extends User>(user: Exact<User, T>) {
    console.log(user)
}

type Exact<TExpected, TActual extends TExpected> = TExpected extends TActual ? TExpected: never;

const userProfile = {id: "1", email: "fa@gmail.com", password: "000", name: "222", job: "engineer"}
insert(userProfile) // 期待するerror

// other: 関数の中で過剰なオブジェクトを受け入れて、type guardで型チェック。正統だったら実行するなども

function _insert(user: User) {
 console.log(user)
}

function insertUser(user: unknown) {
    if(isUser(user)) {
        //user arg is now verified to be of shape User, safe to insert into db
        _insert(user);
    }
}

//do your check
function isUser(user: unknown): user is User {
    if(typeof user !== "object" || user === null)
        return false;

    const neededKeys: Array<keyof User> = ["email", "id", "password"];
    const actualKeys = Object.keys(user);

    return new Set([...neededKeys, ...actualKeys]).size === neededKeys.length;
}
```

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAqgzhATlAvFA3gWAFBT1ASwBMAuKOYRAgOwHMd8oIBbAQwIBsyKq6H8wrOHADuAe0SlylGvWwBfHDlCRYCRAAVEYgGadoaeEigAyDFGqtmEbjLoAaKACsxAI1u9aURdhw6ArtQAxsAEYtSE1OrAADwAKkwAHsAQ1ERwakgAfAAU-upkAKKJrCExRoiOcVkAlBj8eEHhcGIcEAB0HGK0eeo1OD7K4NDFpbFxxZAhEERVAIIh-qwcSSlpGROJUylEWahQm9szq6npBwvASysA-AeTENNS1BAAbkgA3ErYTVHAUPlILS6fT7dDEMgAIgAjBDHCx2FwoBCdKwAAK0NicdpNZiwqCCYTiSSQgAMZLxlmskIATLS8S53EjUrQaBAkBD5FAAPRcqCsHA0aK9QHaPRtGpAA)

[see](https://www.reddit.com/r/typescript/comments/i8vxz2/is_there_an_exactt_advanced_type_something_that/)

**問71**

こちらの

```ts

const obj = {a: "A", b: "B", c: 1}

```

`obj` からそれぞれの値で且つ`literal`な`union type`を作ってください。(期待する結果 -> `"A" | "B" | 1`)

```ts
const a = {a: "A", b: "B", c: 1} as const
type LiteralsUnion = typeof a[keyof typeof a]
```

[playground](https://www.typescriptlang.org/play?#code/MYewdgzgLgBAhjAvDA3nAXDARAQSwGhgCNMsAhAmYTARgF94IrxoBYAKCgE8AHAUxgAZAJZQ+AJzgAbCAFUww8Ehgxu-EADN4AbQDWfLptW8+RuAF0OQA)


**問72**

このようなオブジェクトがあります

```ts
const userByIdResult = {
  data: {
    userById: {
       id: 123,
       username: 'joseph'
    }
  }
}

const userByUsernameResult = {
  data: {
    userByUsername: {
       id: 123,
       username: 'joseph'
    }
  }
}
```

`userById` と `userByUsername` は同じ型を返します。

ただこちら

```ts
type GraphQLResponse<QueryKey, ResponseType> = {
  data: {
    [QueryKey]: ResponseType
  }
}

interface User {
    username: string
    id: string
}

type UserByIdResponse = GraphQLResponse<'userById', User>
type UserByUsernameResponse = GraphQLResponse<'userByUsername', User>
```

ではうまくいきません。

[playground](https://typescript-play.js.org/#code/C4TwDgpgBA4gTgQzACwIoBkBKEDOYD2AdjhADyoCuEcIA0hCADRTZ5EkAq4EAfFALxQA3gCgoUACYJgCAFzCx4qAG1K1OgwC681gWIQukRQF8RpkQEtCwagDMEAY2gBVEnAVKoFN4QQBbCHkcYDgrAHNFcQsJIJDwsxERUEgoV2oAIRAASQlddmhBeCQ0LFw9ElIAcm8M7IlK5jS4HiTuVLdMpt8AvP0BWEQUDF6K6o6QLv8IBvbqFpEgA)

正しく修正してください


```ts
// 1
// 'QueryKey' is not assignable to type 'string | number | symbol'
// QueryKeyはstring、nnumber、symbleになりうるので型を絞り込む。この場合string

// 2 QueryKeyがanyとして推論されているので修正する(computed valuには識別する名前が必要です) see: https://stackoverflow.com/questions/44110641/typescript-a-computed-property-name-in-a-type-literal-must-directly-refer-to-a-b

type GraphQLResponse<QueryKey extends string, ResponseType> = {
  data: {
    [K in QueryKey]: ResponseType
  }
}

interface User {
    username: string
    id: string
}

type UserByIdResponse = GraphQLResponse<'userById', User>
type UserByUsernameResponse = GraphQLResponse<'userByUsername', User>

```

[playground](https://typescript-play.js.org/#code/C4TwDgpgBA4gTgQzACwIoBkBKEDOYD2AdjhADyoCuEcIA0hCFBAB7ASEAmOUOwcAloQDmAGijY8REgBVwEAHxQAvFADeAKChQOCYAgBcazVqgBtWlEFRK1OgwC6hiQWIRZkYwF9139YLZwAGYIAMbQAKokcEYmUBRRhAgAthCGvALCxlr8HGl8gkI+6uqgkFCR1ABCIACSHM5S0CrwSGhYuC4kpADk8VW1HN1iFXDyJXLlUdUjiSkNrsqwiCgY8129UyAzyRBDk9Rj6kA)

**問73**

このようにすると全てを許してしまいます

```ts
type TransitionStyles = {
  entering: Object
  entered: Object
  exiting: Object
  exited: Object
  [key: string]: Object
}

const b:TransitionStyles = {
	entered: {},
	exiting: {},
	exited: {},
	entering: {},
	eee : "fa"
}
```

特定のkeyだけにしてください

```ts
type TransitionStyles<T extends string> = {
  [K in T]: Object
}

type TransitionStylesKeys = "entered" | "exiting" | "exited" | "entering"
const b:TransitionStyles<TransitionStylesKeys> = {
	entered: {},
	exiting: {},
	exited: {},
	entering: {},
	fafa: "fa"
}
```

**問74**

こちらの
```ts
const a = {a: "A", b: "B", c: 1}
```

cの値を型とするtypeを書いてください

```ts
const a = {a: "A", b: "B", c: 1}

type ExtractCValue<T extends Record<string, string | number>, V extends T[keyof T]> =  V extends number ? V : never

const c: ExtractCValue<typeof a, 1> = "aaa"

// or ...  I think This is not best answer.


```

**問75**

こちら

```ts
const objArray = [ { foo: 1, bar: 2}, { foo: 3, bar: 4}, { foo: 5, bar: 6} ]
```

のfooの値のみが入った`1 | 3 | 5)[]`の型を返す関数を書いてください

```ts
const objArray = [ { foo: 1, bar: 2}, { foo: 3, bar: 4}, { foo: 5, bar: 6} ]

function getFooValue(arr: typeof objArray){
	return objArray.map(({foo}) => foo)
}
const result = getFooValue(objArray) // (1 | 3 | 5)[]

```

**問76**

こちらの

```ts
const value = {
	data: {
		name: {id: 1}
	}
}
```

name型を抽出してください expect `{id: number}`


```ts
type B = typeof value["data"]["name"]

// or

const value = {
	data: {
		name: {id: 1}
	}
}

type Data = {
	data : {
		name: {id: number}
	}
}

const getNameValue = (value: Data) => {
	return value.data.name
}

type B = ReturnType<typeof getNameValue>
```


**問77**

こちら

```ts
type ActivityLog = {
	lastEvent: Date
	events: {
		id: string
		timestamp: Date
		type: "Read" | "Write"
	}[]
}

let activityLog: ActivityLog = { lastEvent: new Date(), events: [{ id: "1", timestamp: new Date(), type: "Read"}]}


function get(activityLog: ActivityLog, key: string){
	return activityLog[key] // Element implicitly has an 'any' type because expression of type 'string' can't be used to index type 'ActivityLog'.
  No index signature with a parameter of type 'string' was found on type 'ActivityLog'.(7053)
}
let lastEvent = get(activityLog, "lastEvent") // any

```

[playground](https://www.typescriptlang.org/play?#code/LAKALgngDgpgBAQQMZgJYDdWQDIHsDmcAvHAN6gCQANgIYDOYAoujAHZgBccAIjWDJRgt2dLuRAUKqACZcGAJ1St8lCmgC2MBjXVQuvfqsiwuAIgBKMGtNNwAPnFMB1Rf1OUAvgG0AuqA+goFQwYHA0KBhYEHj4XMhomDgExGRwtAzMbJxwrDAA7jx8MAAUAJQANHBCWaJwXqRwMmYAjKaVGlpgOno5+YX8Ze3QMGaW1qYePgEggSAAZgCurBG4rHD4IcXhCVExcRGJ0QSVANYwEHJgisql4hTyIQvya9uRSfheZxB+INPBoekmMJQiQNmAtgddsdHIDMuxTKUgA)

は`key`を`string`にannotateされているため、`activityLog[key]`がいろいろな型を返す可能性があるためTypeScriptはanyを返しています

`key`が`activityLog`に存在するプロパティのみ渡すことを保証し、その後のルックアップで適切に推論されるように`get`を型付けしてください


 ```ts
 type ActivityLog = {
	lastEvent: Date
	events: {
		id: string
		timestamp: Date
		type: "Read" | "Write"
	}[]
}

let activityLog: ActivityLog = { lastEvent: new Date(), events: [{ id: "1", timestamp: new Date(), type: "Read"}]}


function get<T, K extends keyof T>(activityLog: T, key: K){
	return activityLog[key]
}
let lastEvent = get(activityLog, "lastEvent")

// or
type Get = <T, K extends keyof T>(activityLog: T, key: K) => T[K]

let get:Get = (activityLog, key) => {
	return activityLog[key]
}

 ```

 [playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAggxsAlgN0aAMgewOZQLxQDeAsAFACQANgIYDOwAoshAHbABcUAItcBGeQjM2tTiQrlEAE070ATohbYB5JAFsI9amrCcefFaEicARACUI1KSagAfKCYDqCviYEBfANoBdMu7JklBDAUNQIKGggWNic8EioGDj4RFA09EysHFAsEADu3LwQABQAlAA0UEKZolCehFDSpgCMJhXqmsDautl5BXylbeAQphZWJu7e-qQBpABmAK4s4ZgsUNjBADwAKhUA0pUAHnwsUrRQANYQIJizUFsAfEVh8ZHRnDsXV5y7JeLkcsF5nJVs8IolsJ5LiBfKQpkEQmlGMIQgR1sAnuEElEcBUTIiMmwTCUgA)

**問78**


こちら

```ts

 type ActivityLog = {
	lastEvent: Date
	events: {
		id: string
		timestamp: Date
		type: "Read" | "Write"
	}[]
}

let activityLog: ActivityLog = { lastEvent: new Date(), events: [{ id: "1", timestamp: new Date(), type: "Read"}]}

type Get = <T, K extends keyof T>(activityLog: T, key: K) => T[K]

const get: Get = (activityLog, key) => {
	return activityLog[key]
}

```

のgetがよりネストされた値の型を返す
(`get(activityLog, "events", 0, "id")`を実行したら `string`を返す)
ようにgetをオーバーライドで定義して、
内部の実装を変更して
それぞれのkeyを参照に型を返すようにしてください


```ts
type ActivityLog = {
	lastEvent: Date
	events: {
		id: string
		timestamp: Date
		type: "Read" | "Write"
	}[]
}

let activityLog: ActivityLog = { lastEvent: new Date(), events: [{ id: "1", timestamp: new Date(), type: "Read"}]}


type Get = {
	<O extends object, K1 extends keyof O>(o: O, key: K1): O[K1]
	<O extends object, K1 extends keyof O, K2 extends keyof O[K1]>(o: O, key: K1, key2: K2): O[K1][K2]
	<O extends object, K1 extends keyof O, K2 extends keyof O[K1], K3 extends keyof O[K1][K2]>(o: O, key: K1, key2: K2, key3: K3): O[K1][K2][K3]
}


let get:Get = (o: any, ...keys: string[]) => {
	let result = {...o}
	keys.forEach(k => {
		result = result[k]
		return result
	})
}
let lastEvent = get(activityLog, "events", 0, "id")
```

[playground](https://www.typescriptlang.org/play?#code/LAKALgngDgpgBAQQMZgJYDdWQDIHsDmcAvHAN6gCQANgIYDOYAoujAHZgBccAIjWDJRgt2dLuRAUKqACZcGAJ1St8lCmgC2MBjXVQuvfqsiwuAIgBKMGtNNwAPnFMB1Rf1OUAvgG0AuqA+goFQwYHA0KBhYEHj4XMhomDgExGRwtAzMbJxwrDAA7jx8MAAUAJQANHBCWaJwXqRwMmYAjKaVGlpgOno5+YX8Ze3QMGaW1qYePgEggeDDcADiISniFAA8APJVAB78rNJ0cLgARgBWMCiVANLNO3sHcADWMBC4AGZwGwB8xbhcG5VnhAuDdSv8vDc-BJNnc2A8TudLnAbrD9ocge9PtcAEyoh4Yj4bCHNHw-P5Yp4vEHNQEvbEg7Fgz7EnwQ7FQ9ZbGC7OGHBEXMDXW7c+7ol6YgHI3Ei3mU16ElnXADMeLF8uZkLZpN+-1pwORNLl9KleqVIKVTKJmqu7IhSqh01mwVC+BCHCWoRIOrCrAglQAdIGgbUFEp8L5SsQvmRKM64PItABXKiesiB-24aYUYP+t64eSMcIAC2KjyjMYkFATdGTqerta8jw5VZCifkrHjSZTnlK-iCy3STGEqddYGK4QSURilVM1REbTgAAYZzJTKUgA)



**問79**

このような

```ts
type Type = {
    "a": string,
    "b": number,
    "c": (ind: string) => void
}
```

型がある。こちらを型パラメータにそれぞれのkeyを渡した時値型が返ってくる型を作ってください

```ts
type Type = {
    "a": string,
    "b": number,
    "c": (ind: string) => void
}

type Key<K extends keyof Type> = Type[K]
const a:Key<"a"> = "a"

```

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAKu0F4oG8CwAoKWoCICGOAXFAM7ABOAlgHYDmANBtrgEZFTUCuAtixOY0zYcAY3YAKGgBNiZKnQCUUBAD4oANwD2lKRgC+GDKEhQA0hBAAeU1AgAPYBGpSSUANYXNAM1jw1SOEgAbVMAXQwRTWoyKDxCcyt8HH9cAEUvPAycIA)

**問80**

[こちら](https://www.typescriptlang.org/play?#code/C4TwDgpgBAyg9gWwgeQEYCsIGNhQLwCwAUFFAD5QDexptUAhgFxQDOwATgJYB2A5gNw1aAXyEVqJOlFTNuAVwSoI7QZNKjJ4oXSzNUcOABsI9bqrrDVxACbZD9dtCxxubVohQZm8JGkw4rIk4AMygAChYPPwA6LABKKigAeiSoZXY4diFiYSA)は

```ts
type SomeObject =
  | {
      a: string;
    }
  | {
      b: number;
    }
  | {
      c: boolean;
    };

declare const someObj: SomeObject;

if (someObj.c) { // error
}

```

参照すると、エラーになります。TypeScriptが正しく推論できるようにしてください

```ts
// typeというtagを与えます
type SomeObject =
  | {
      type: "a"
      a: string;
    }
  | {
      type: "b"
      b: number;
    }
  | {
      type: "c"
      c: boolean;
    };

declare const someObj: SomeObject;

if (someObj.type === "c") {
    someObj.c
}

// or

const check = (someObj:SomeObject) => {
switch(someObj.type){
    case "a":
    someObj.a
    return
    case "b":
    someObj.b
    return
    case "c":
    someObj.c
    return
    default:
        new Error("not provided type")
    }
}
```

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAyg9gWwgeQEYCsIGNhQLwCwAUFFAD5QDexptUokAXFAEQCGLNdUbzAzsABOASwB2AcwDcXUgF8uFaiW4MIzFqk7K6qZqICuCVBEHTtUecsUzaq9Vi3coWZqjhwANhDaizdWWbEACbYHmyC0FhwogJQfIgoGMzwSGiYOIFExMIAZlAAFPGpGAB0qvh4eKwOAJRUNkWJ6CVYxLJQEB580JSWxMRRMbhYABbYANb4BY1pjClN2MB1eAB89VlEfADuwsCjhQlpZeAQNUp0WGzdrByMDYelbDYRwPqColA2l9caLHfmM1KqGeEFe7y+V2gLAc-zogOarXMLzeohsIRybH0HmAsKcUFEEC2UAAooJBHBBPkWKI4LgwOSAG7CEJBegnFg1GyWSxAA)




**問81**

こちらの

```ts

interface Hoge {
    a: string;
    (arg: string): void;
}

const hoge = (arg: string) => { console.log(arg); }

const a = {a: "foo"}

const f: Hoge = ??

```

`??`の箇所をHoge型になるように代入してください

[playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgBIHsDmKDeAoZQ5OALmQGcwpRMBuAogCjikzMupEwEoyA3dMAAm9AL548CdCErIAFlhQBeZM1bsqNbsiUA+ZDmRSZ6ADYQAdKaxqetZOMnTZcHQdLIARDHTpPj41kYMgxsNwB+cKA)


```ts
interface Hoge {
    a: string;
    (arg: string): void;
}

const hoge = (arg: string) => { console.log(arg); }

const a = { a: "foo" }

const f:Hoge = Object.assign(hoge, a)
```

[playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgBIHsDmKDeAoZQ5OALmQGcwpRMBuAogCjikzMupEwEoyA3dMAAm9AL548CdCErIAFlhQBeZM1bsqNbsiUA+ZDmRSZ6ADYQAdKaxqetZOMnTZcHQdLIARDHTpPj41kYEgxsNwB5ACMAKwgEMAs4cnJgTBBGBWwAGmJuIA)

see: (https://twitter.com/uhyo_/status/1197098731503411200?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1197098731503411200%7Ctwgr%5E&ref_url=https%3A%2F%2Ftech.dely.jp%2Fentry%2Ften_trivia_of_typescript_)


**問82**

必須プロパティをUnion型で返す型を作ってください

```ts
type RequireKeys<T> = {[K in keyof T]-?: {} extends Pick<T, K> ? never : K}[keyof T]


// 例えば Tが{id: string, age?: number}の場合

// -?はオプショナルなキーが渡ってきてもは必須になる。
// type TObj = {id: string, age?: number}
// type RecordT<TObj> = {[K in keyof TObj]-?: TObj[K]}
// type RequireTObj = RecordT<TObj>
// type RequireTObj = {
//     id: string;
//     age: number;
// }
// この処理をしないと最後に
// type RequireTObj = string | number | undefinedのようなオプショナルな場合の値型であるundefinedがついてくる

//  {} extends Pick<T, K>のイメージ。それぞれ
// {} extends {id: string}
// {} extends {age? number}

type Id = Pick<{id: string}, "id">
type Age = Pick<{age?: number}, "age">
const id: Id = {} // error
const age: Age = {} // ok
type Result = RequireKeys<TObj> // "id"

// オプショナルなものは{}にassignableなので neverが返り、そうでないidはKが返る
type BB = RequireKeys<{id: string, age?: number}>
// type BB = {
//     id: "id";
//     age: never;
// }

// ageがnever, idがKの値型になる

// 最後に
// [keyof T]で値型にアクセス
// 値型がnever以外を抽出
// "id"となる

```

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAShCOBXAlgJwgaQiAzgHgBUA+KAXigG8BtDKZAOygGtsB7AMygIF0BaAfgBclAL5QIAD2AR6AExxQACsgDGTQgBooGEvyj0IANwioowjCKosQHLtwCwAKCdOA9K6iBo+UASDIAcGLoAyDBTIssI4wKgMAOZaAIZREEL6iAC2AEYmIoB2DIAsGoAQKi6O7lACgPYMgFUMgOsMgO0MgOcMgFMMgNcMgFYMgLUMgD8MAYCEdoDGDIBmDICyDH2AQQylgKP6gBAZgNYMzYDRDIBADE6gkFwA8mkAVmSUIWER0XEJSfSpGagiS+DQcCqsqLIEhOsbJOTUtAzMbJwEz3xJv02NG4F0cy2uCBQ6EBW3INzuDyemyIhWKgGUGLKAM8VAGAugCSGQDqDM1ACIMgAsGQAA5oAY-Smbg84NgkLQEBh23CkXoUSgAB9kukTFyoIg5BB2AwILIsoAIhkAYgzNKp1JrNfJZQAkCoBo9UA5gyAIQZZoLZMLRbIAoATBiJfUA8gyzVEeUTiKQyeRKVTqAhaHRZQAlDIBDhnagA6GeaAXQZADEMgD0GQM0m2SaRyBTBUJQVnRUHFChiSP2mPxRI8s5JjwBQCqUYBC+UAqgyBwCADCFSgZjKgy4U6QBJWTbZRqPCxvZsqIiLQAIhCvZRYKuUAAggkW0725njqdMn3M4OnLd6OE6HGm9sU1BiiZUHdl6xV8AoJnhOPoG8xMVWExLis4DhEAAbE9whnoLC4JEvHcefuyL2QA)

**問83**

オプショナルなキーだけをUnion型で返す型を作ってください

```ts
type OptionalKeys<T> = { [K in keyof T]-?: {} extends Pick<T, K> ? K : never }[keyof T];


// 問題82とconditionalが逆
 ```


**問84**


`type Union = {a: "a"} | {b: "b"}` を使って `{a: "a", b: "b"}`になるような`type InterSection` を作ってください

```ts
type GetKeys<U> = U extends Record<infer K, any> ? K : never

type UnionToIntersection<U extends object> = {
   [K in GetKeys<U>]: U extends Record<K, infer T> ? T : never
}

type Union = { a: "a" } | { b: "b" }
type Transformed = UnionToIntersection<Union>

∏
//　こちらでもうまくいくが
// const aAndB = {a: "a", b: "b"}
// type AandB<Intersection> = { [K in keyof Intersection]: Intersection[K]}
// type Result = AandB<typeof aAndB>
// この場合 typeだけで作られていないので問題の趣旨と違います
```

[playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBA4hwGkIgM4B4CqA+KBeKGUEAHsBAHYAmKUAShAMYD2ATpWgJbkBmELUCADRQAhuRA4A-AKgAuKOQgA3PgChVoSAXIcm5ACpMAkuTIsUjYLvKYipCtShMARgCtLOfAG9VUPwG0EKC5YeCRUTCwAXXlCEjIqGnpmNjQhYJ4+KH0pbLkFZTUAX3VNaH0WMRRuVgBbCEo8bWtDEzMLBis9NC9ReRRgFi4AcygiqAAfKF7neXIAV1rnLPGprwZ+wZGirFUgA)

**問83**

```ts


keyof Record<K, T>
Record<K, T>[K]

```

**問84**

```ts

```

WIP


[playgrond](https://www.typescriptlang.org/play/index.html#code/JYOwLgpgTgZghgYwgAgMoHsC2EDyAjAKwgTGQG8BYAKGVuRmCgGcwBpCATwC46WpQA5gG5qdZE2LoQAE3bdafQSJp0wAC0azOPWmWTBpPPSDjYjAX3PJz1G1WrUwHAA4oASsQCuzYADcIAApwUGDAcAA2ADwAKgB8yAC85KJ0ANoB+iDIANac6DDI0QC6APw8HgjeTH6BwaERMelFscrmyk6uyEEhYeEAogAeSM5gMQA0yKzIEAOQMkw5eQVxicgVVTXd9VErAGRdwAjZ45MtDlQIUizIcDxbvYPDoxjY+EQkEwBE6ppyn-FJSgqWgMZhsbR0T6fMYpBSSGRyHRQmHA5A-KBaeS6fSGcjIExmciWay2c6XEDXPDlLw+fz3BovXCEYhgAFE6h4AB06MxqzIdkcLhQABEIBBnPSdmygWJ0plFhx8oVSjxReLJY0As1WspqOTrghVWKJXVepFGW8WdLYWiNBjEeQDEZ8aYIBZLKT7FQgA)



```ts
https://tech-1natsu.hatenablog.com/entry/2019/02/09/014218
```

**参照**

- [https://qiita.com/uhyo/items/6acb7f4ee73287d5dac0](https://qiita.com/uhyo/items/6acb7f4ee73287d5dac0)
- [https://www.pg-fl.jp/program/ts/kw-ref/never.htm](https://www.pg-fl.jp/program/ts/kw-ref/never.htm)
- [https://qiita.com/Quramy/items/b45711789605ef9f96de](https://qiita.com/Quramy/items/b45711789605ef9f96de)
- [https://qiita.com/uhyo/items/aae57ba0734e36ee846a](https://qiita.com/uhyo/items/aae57ba0734e36ee846a)
- [https://blog.logrocket.com/writing-readable-code-with-typescript-enums-a84864f340e9/](https://blog.logrocket.com/writing-readable-code-with-typescript-enums-a84864f340e9/)
- [https://mariusschulz.com/blog/the-unknown-type-in-typescript](https://mariusschulz.com/blog/the-unknown-type-in-typescript)
- [TypeScript3.4 型の強化書](https://booth.pm/ja/items/1317204)

- [use Partial in nested property with typescript](https://stackoverflow.com/questions/47914536/use-partial-in-nested-property-with-typescript)
- [intersection of mapped types](https://stackoverflow.com/questions/47464913/intersection-of-mapped-types)

