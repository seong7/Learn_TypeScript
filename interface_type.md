## interface 와 type alias

JavaScript 함수의 매개변수에 `object` 가 들어오는 경우 더욱 error 를 catch 하기가 어렵다.

```javascript
function f6(a) {
  return `이름은 ${a.name} 이고 ${a.age / 10}대 입니다.`;
}

console.log(f6("Eco")); // 이름은 undefined 이고 NaN대 입니다.
```

이 때는 `object literal type` 으로 방어 할 수 있다.

```typescript
function f7(a: { name: string; age: number }): string {
  return `이름은 ${a.name} 이고 ${a.age / 10}대 입니다.`;
}

console.log(f7("Eco")); // error TS2345: Argument of type 'string' is not assignable to parameter of type '{ name: string; age: number }'.
```

좀 더 명시적인 방법으로 `Interface` 와 `Type Alias` 가 있다.

```typescript
interface PersonInterface {
  name: string;
  age: number;
}

type PersonTypeAlias = {
  name: string;
  age: number;
};

function f8(a: PersonInterface): string {
  return `이름은 ${a.name} 이고 ${a.age / 10}대 입니다.`;
}

console.log(f8("Eco")); // error TS2345: Argument of type 'string' is not assignable to parameter of type 'PersonInterface'.
```

### Interface 와 Type Alias 의 차이점

- _funciton_

  ```typescript
  // type alias
  type EatType = (food: string) => void;

  // interface
  interface IEat {
    (food: string): void;
  }
  ```

- _array_

  ```typescript
  // type alias
  type PersonList = string[];

  // Iterface
  interface IPersonList {
    [index: number]: string; // array 의 index (number) 자리에 string 이 들어간다는 의미
  }
  ```

- _intersection_

  두 개의 타입을 합치는 방법

  ```typescript
  // 타입 1
  interface ErrorHandling {
    success: boolean;
    error?: { message: string };
  }

  // 타입 2
  interface ArtistsData {
    artists: { name: string }[];
  }

  // type alias 로 합치는 방법
  type ArtistsResponseType = ArtistsData & ErrorHandling;

  // interface 로 합치는 방법
  interface IArtistsResponse extends ArtistsData, ErrorHandling {}

  let art: ArtistsResponseType;
  let iart: IArtistsResponse;

  console.log(typeof art === typeof iart); // true
  ```

- _union types (type alias)_

  **type alias 에서만 제공됨**

  ```typescript
  interface Bird {
    fly(): void;
    layEggs(): void;
  }

  interface Fish {
    swim(): void;
    layEggs(): void;
  }

  // type alias 를 사용한다.
  type PetType = Bird | Fish;

  interface IPet extends PetType {} // error TS2312: An interface can only extend an object type or intersection of object types with statically known members.

  class Pet implements PetType {} // error TS2422: A class can only implement an object type or intersection of object types with statically knwon members.
  ```

  - union type 은 `type alias` 를 사용한다.

  - union type 을 상속 받거나 class 가 구현하는 행위는 허용되지 않는다.

- _Declaration Merging (interface)_

  **interface 에서만 제공됨**

  타입 선언을 merge 할 수 있는 기능

  ```typescript
  interface MergingInterface {
    a: string;
  }

  interface MergingInterface {
    b: string;
  }

  let mi: MergingInterface;
  // mi 는 'a' 와 'b' 를 모두 가진다.

  type MergingType = {
    a: string;
  };
  type MergingType = {
    b: string;
  };
  // Duplicate identifier 'MergingType'. ts(2300)
  ```

### 무엇을 사용할지?

기능의 차이로 인한 구분은 위에 언급된 `Merging` 정도가 있고 그 외에는 아래와 같은 기준으로 결정한다. (강사님 의견)

**Type Alias**

- Type 의 별칭으로 사용할 때 (A 라는 type 을 다른 이름으로 부르고 싶을 때)
- 이미 있는 타입들을 조합해 Union Type 또는 Intersection Type 을 만들 때

**Interface**

- 새로운 Type 을 만들어 낼 때
