## Type Guard 로 안전함 파악하기

TypeScript 에는 Type Guard 라는 것이 있다. Type Guard 는 특정 scope 내에서 type 을 보장하는 runtime check 을 수행하는 특정 표현식이다.

## Type Guard 의 종류

### `typeof` Type Guard

`typeof` type guard 를 사용해 타입을 하나로 확정지을 수 있다.

보통 **Primitive type** 인 경우에 사용한다.

```typescript
function getNumber(value: number | string): number {
  value; // number | string
  if (typeof value === "number") {
    value; // number
    return value;
  }
  value; // string
  return -1;
}
```

### `instanceof` Type Guard

custom Error 객체를 생성해 `instanceof` Type Guard 로 예외처리를 해주면,
안전한 type 으로 값을 처리할 수 있다.

```typescript
class NagativeNumberError extends Error {}

function getNumber(value: number): number | NagativeNumberError {
  if (value < 0) return new NagativeNumberError();

  return value;
}

function main() {
  const num = getNumber(-10);

  if (num instanceof NagativeNumberError) {
    // 예외 처리
    return;
  }

  num; // 보장된 number 타입으로만 안전하게 처리 가능
}
```

### `in` operator Type Guard

object 의 프로퍼티 유무로 처리하는 경우

```typescript
interface Admin {
  id: string;
  role: string;
}

interface User {
  id: string;
  email: string;
}

function redirect(user: Admin | User) {
  if ("role" in user) {
    routeToAdminPage(user.role);
  } else {
    routToHomePage(user.email);
  }
}

class Eco implements Admin {
  id = "eco";
  role = "boss";
}

const eco = new Eco();

redirect(eco);
```

`Admin` 과 `User` 는 **value 가 아니라 type** 이기 때문에 `instanceof` 나 `typeof` 로 Guard 할 수 없다.

### literal Type Guard

object 의 프로퍼티가 동일하고, 타입이 다른 경우에 사용

```typescript
interface IMachine {
  type: string;
}

class Car implements IMachine {
  // 프로퍼티의 타입 선언  __(값도 지정 가능)
  type: "CAR";
  wheel: number;
  // 프로퍼티 initialize
  constructor() {
    this.type = "CAR";
    this.wheel = 10;
  }
}

class Boat implements IMachine {
  // 프로퍼티의 타입 선언  __(값도 지정 가능)
  type: "BOAT";
  motor: number;
  // 프로퍼티 initialize
  constructor() {
    this.type = "BOAT";
    this.motor = 11;
  }
}

function getWheelOrMotor(machine: Car | Boat): number {
  if (machine.type === "CAR") {
    return machine.wheel;
  } else {
    return machine.motor;
  }
}

const car = new Car();
const boat = new Boat();

console.log(getWheelOrMotor(car)); // 10
console.log(getWheelOrMotor(boat)); // 11
```

### custom Type Guard

```typescript
/********* type 선언부는 위와 동일 *******/

function getWheelOrMotor(machine: any): number {
  if (isCar(machine)) {
    return machine.wheel;
  } else if (isBoat(machine)) {
    return machine.motor;
  } else {
    return -1;
  }
}

// 함수 반환값과 동일한 scope 내에서만 `Car` type 으로 narrow 됨
function isCar(arg: any): arg is Car {
  return arg.type === "CAR";
}

// 함수 반환값과 동일한 scope 내에서만 `Boat` type 으로 narrow 됨
function isBoat(arg: any): arg is Boat {
  return arg.type === "BOAT";
}
```

다양한 라이브러리에서 구현되어 있는 Type Guard 방식이다.

`any` type 으로 매개변수를 받아 type check 을 하고 `type predicate` <sub>`arg is Car`</sub> 를 통해 각 블록(block) 별로 type 을 좁혀서 처리할 수 잇게 한다.

- [`is` 예약어 관련 글 - stackoverflow](https://stackoverflow.com/questions/40081332/what-does-the-is-keyword-do-in-typescript)
