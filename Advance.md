# Advance TypeScript

## TODO aliasing technique

use TODO prefix.

```ts
type TODOPersion = any
```

## `unknown` と `any`

any ・・・全てのタイプはanyに割り当て可能であり、anyは全てのTypeから割り当られることができる
unknown・・・全てのタイプはunknownに割り当て可能であるが、unknown は割り当てられることができない

```ts
function foo1(bar: any) {
  const a: string = bar; // no error
  const b: number = bar; // no error
  const c: { name: string } = bar; // no error
}

function foo2(bar: unknown) {
  const a: string = bar; // 🔴 Type 'unknown' is not assignable to type 'string'.(2322)
  const b: number = bar; // 🔴 Type 'unknown' is not assignable to type 'number'.(2322)
  const c: { name: string } = bar; // 🔴 Type 'unknown' is not assignable to type '{ name: string; }'.(2322)
```

and

```ts
let x: unknown;

x = 123; // no error
x = 'abc';  // no error
x = true; // no erro
```

「実行時の型を見てから確定します」という時に使う


## マジックストリングを回避


