## Type System 의 종류

- `structural type system`

TypeScript 는 [`structural type system`](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html#structural-type-system) 을 따르고 있다. 즉, **동일한 구조를 가진 객체들**은 **동일한 타입으로 간주**된다.

```typescript
interface IPerson {
  name: string;
  age: number;
  speak(): string;
}

type PersonType = {
  name: string;
  age: number;
  speak(): string;
};

let personInterface: IPerson = {} as any; // 예시에서 property 들 작성을 생략하려고 다운캐스팅 한 듯?  (정식 명칭은 type assertions)
let personType: PersonType = {} as any;

console.log(typeof personInterface === typeof personType); // true
```

- `nominal type system`

하지만 `structural type system` 를 따르지 않는 언어들도 있다. 그리고 TypeScript 에서 그 것을 흉내낼 수도 있다.

[`nominal type system`](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-oop.html#nominal-reified-type-systems) 이란 구조가 같아도 **이름이 다르면 다른 타입으로 간주**하는 type system 이다.

```typescript
type PersonID = string & { readonly brand: unique symbol };
// & : intersection (두 타입을 합친다.)

function PersonID(id: string): PersonID {
  return id as PersonID;
}

function getPersonById(id: PersonID) {}

getPersonById(PersonID("id-aaaaaa"));
getPersonById("id-aaaaaa"); // error TS2345: Argument of type 'string' is not assignable to parameter of type 'PersonID'. Type 'string' is not assignable to type '{ readonly brand: unique symbol; }'.
```

이 방식은 앱의 규모가 커졌을 때 type 을 이름별로 분류해 각 역할을 나누는 설계를 할 경우 유용하게 사용할 수 있다.
