# TypeScriptPractice

Q1. こちらに型をつけてください

```ts
function f(a, b) {
  return a + b;
}

f("age is", "18");
```

A1

```ts
function f(a: string, b: string): string {
  return a + b;
}

f("age is", "18");
```
