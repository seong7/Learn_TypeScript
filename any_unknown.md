## any 대신 unknown 사용하기

매개변수를 자유롭게 받아 type 에 따라 해당 매개변수를 처리하고 싶다면, `any` 대신 `unknown` 을 사용하는 것이 안전하다.

- **any** : 모든 처리를 자유롭게 할 수 있다. (JS 와 동일함)

- **unknown** : 모든 동작을 막아두고 type 이 명시된 경우에만 해당 타입에 맞는 동작을 처리할 수 있다.

any 사용

```typescript
function f1(a: any): number | string | void {
  a.toString();

  if (typeof a === "number") {
    return a * 38;
  } else if (typeof a === "string") {
    return `Hellow ${a}`;
  }
}

console.log(f1(10)); // 380
console.log(f1("Mark")); // Hello Mark
console.log(f1(true)); // undefined
```

unknown 사용

```typescript
function f2(a: unknown): number | string | void {
  a.toString(); // error : object is of type 'unknown'.(2571)

  // 아래처럼 Type 명확히 한 후 처리 가능
  if (typeof a === "number") {
    return a * 38;
  } else if (typeof a === "string") {
    return `Hellow ${a}`;
  }
}
```
