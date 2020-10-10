## Sub Type 과 Super Type

Type 집합 간의 포함 관계에 따라 나뉘어 진다.

```typescript
// '1' type < 'number' type
let sub1: 1 = 1; // literal type (1 외엔 부여할 수 없다.)
let sup1: number = sub1; // OK
sub1 = sup1; // error ! Type 'number' is not assignable to type '1'.

// 'number[]' type < 'object' type
let sub2: number[] = [1];
let sup2: object = sub2; // OK (array 는 object 의 한 종류)
sub2 = sup2; // error Type '{}' is missing the following properties from type 'number[]': length, pop, push, concat, and 16 more.

// '[number, number]' type < 'number[]'
let sub3: [number, number] = [1, 2]; // tuple type
let sup3: number[] = sub3;
sub3 = sup3; // error ! Type 'number[]' is not assignable to type '[number, number]'. Target requires 2 element(s) but source may have fewer.
```

- **'any', 'never', custom Type**

```typescript
// any 는 모든 것이 가능하다.
let sub4: number = 1;
let sup4: any = sub4;
sub4 = sup4;

// 'never' type 에는 무엇도 assign 할 수 없다.
let sub5: never = 0 as never;
let sup5: number = sub5;
sub5 = sup5; // error ! Type 'number' is not assignable to type 'never'.

// 'SubDog' < 'SubAnimal'
class SubAnimal {}
class SubDog extends SubAnimal {
  eat() {}
}
let sub6: SubDog = new SubDog();
let sup6: SubAnimal = sub6;
sub6 = sup6; // error ! Property 'eat' is missing in type 'SubAnimal' but required in type 'SubDog'.
```

다시 정리해보자면,

### 공변성 (covariant)

Type 이 같거나 Sub type 인 경우, 할당이 가능하다.

```typescript
// primitive type
let sub7: string = "";
let sup7: string | number = sub7; // OK

// object :  각각의 프로퍼티가 대응하는 프로퍼티와 같거나 서브타입이어야 한다.
let sub8: { a: string; b: number } = { a: "", b: 1 };
let sup8: { a: string | number; b: number } = sub8; // OK

// array :  object 와 마찬가지
let sub9: Array<{ a: string; b: number }> = [{ a: "", b: 1 }];
let sup9: Array<{ a: string | number; b: number }> = sub9;
```

### 반(공)변성 (contravariant)

함수의 **매개변수 타입만 같거나 슈퍼타입인 경우**, 할당이 가능하다.

이는 매우 헷갈릴 수 있는 개념이다.

```typescript
class Person {}
class Developer extends Person {
  coding() {}
}
class StartupDeveloper extends Developer {
  burning() {}
}
// StartupDeveloper < Developer < Person

function tellme(f: (d: Developer) => Developer) {} // (매개변수와 return 타입이 지정된 함수를 매개변수로 받는다.)

// 아래의 경우들 모두 default 로 허용됨 (반공병성)

// 매개변수 (Developer) => Developer 에 (Developer) => Developer 를 할당하는 경우
tellme(function dToD(d: Developer): Developer {
  return new Developer();
});

// 매개변수 (Developer) => Developer 에 (Person) => Developer 할당
tellme(function pToD(p: Person): Developer {
  return new Developer();
});

// 헷갈리는 부분** (StartupDeveloper < Developer 이기 때문)
// 매개변수 (Developer) => Developer 에 (StartupDeveloper) => Developer 할당
tellme(function sToD(s: StartupDeveloper): Developer {
  return new Developer();
});
```

[TypeScript 2.6](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-6.html)부터는 이 문제를 해결할 수 있다.

`--strictFunctionTypes` 또는 `--strict` 를 사용하면 **함수의 매개변수 타입만 같거나** **슈퍼타입이 아닌 경우** (위의 세번째 경우), 에러를 통해 경고해준다.

## Reference Link

- [TypeScript 에서 공변성과 반공병성 설명](https://edykim.com/ko/post/what-is-coercion-and-anticommunism/#%EB%8B%A4%EB%A5%B8-%ED%83%80%EC%9E%85%EC%9D%80)
