# TypeScriptPractice

**問 1**. こちらに型をつけてください

```ts
function f(a, b) {
  return a + b;
}

f("age is", "18");
```

```ts
function f(a: string, b: string): string {
  return a + b;
}

f("age is", "18");
```

**問 2**. こちらの`x`の型は何か

```ts
let x = [0, 1, null];
```

```ts
number[]

//もっとも共通する型推論をする
//nullは全ての型の部分型
```

**問 3**. なぜエラーが起きるか説明してください

```ts
window.onmousedown = function(mouseEvent) {
  console.log(mouseEvent.button); //<- Error
};
```

```ts
//onmousedownは引数にMouseEvent型を取ることは知っているが、
//MouseEvent型にbuttonプロパティはない為
```

**問 4**

こちらは型推論で`(string|number)[]`になります。

```ts
const tupleStrNum = ["X", 2];
```

`[string, number]`とするにはどうしたらいいですか

```ts
const tupleStrNum = ["x", 2] as [string, number];
//const tupleStrNum: [string, number] = ["X", 2];
```

**問 5**. 名前を変えずに既存の`Foo型 {x: number}`にメンバー `y:number`を追加してください

```ts
type Foo = {
  x: number;
};
// ... elsewhere ...
type Foo = {
  //Error
  y: number;
};
```

```ts
interface Foo {
  x: number;
}
// ... elsewhere ...
interface Foo {
  y: number;
}
let a: Foo = { x: 1, y: 2 };
```

**問** 6. 下記はなぜエラーが出るのかお答えください

```ts
namespace A {}
var B = A;
```

```ts
namespace A {} //non-instanted なので名前空間宣言空間にのみ宣言されている
var B = A; //=で代入できるのは変数宣言空間のみなのでAは見つからない
```

**問**

```ts
```

```ts
```

**問**

```ts
```

```ts
```

**問**

```ts
```

```ts
```

**問**

```ts
```

```ts
```

**問**

```ts
```

```ts
```

**問**

```ts
```

```ts
```

**問**

```ts
```

```ts
```

**問**

```ts
```

```ts
```

**問**

```ts
```

```ts
```

**参照**

[https://qiita.com/kurogelee/items/12c45f9fb1615877a61f](https://qiita.com/kurogelee/items/12c45f9fb1615877a61f)
