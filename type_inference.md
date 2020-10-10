## Type Inference (타입 추론)

### **let** 과 **const** 의 타입 추론 이해하기 (+ as const)

- primitive type 일 때

```typescript
let a = "Mark"; // string
const b = "Mark"; // 'Mark' => literal type

let c = 38; // number
const d = 38; // 38 => literal type

let 3 = false; // boolean
const f = false; // false => literal type

```

- Array 일 때

let 과 const 모두 요소들의 공통된 type 을 찾아 추론한다.

```typescript
let g = ["Mark", "Haeun"]; // string[]
const h = ["Mark", "Haeun"]; // string[]
```

- `as const`

readonly + array literal type 으로 추론한다.

```typescript
const i = ["Mark", "Haeun", "Bokdang"] as const; // readonly ['Mark', 'Haeun', 'Bokdang']
```

### Best common type (가장 적절한 타입)

여러 type 의 요소를 가진 배열의 Best common type (가장 적절한 타입) 추론

```typescript
let j = [0, 1, null]; // (number | null)[]
const k = [0, 1, null]; // (number | null)[]
```

배열의 타입 Best common type 추론 유도하는 방법

```typescript
class Animal {}
class Rhino extends Animal {}
class Elephant extends Animal {}
class Snake extends Animal {}

let l = [new Rhino(), new Elephant(), new Snake()];
const m = [new Rhino(), new Elephant(), new Snake()];
// 위 l 과 m 은 "(Rhino | Elephant | Snake)[]" 로 추론됨

const n = [new Animal(), new Rhino(), new Elephant(), new Snake()];
// "Animal[]"
// n 처럼 부모 type 이 포함되어 있으면 부모 type 으로 추론됨

const o: Animal[] = [new Rhino(), new Elephant(), new Snake()];
// "Animal[]"
// 가장 좋은 방법은 o 처럼 type 을 명시 하는 것
```

### Contextual Typing

변수의 위치에 따라 타입 추론이 달라진다.

```typescript
const click = (e) => {
  e; // any
};

document.addEventListener("click", (e) => {
  e; // MouseEvent
});
```

두번째 함수의 `e` 는 `click` event 가 명시되어 있으므로 'MouseEvent' type 으로 추론된다.
