## Class 를 안전하게 만들기

### `strictPropertyInitialization` 옵션

Class 의 Property 가 생성자 혹은 선언에서 값이 지정되지 않으면, 컴파일 에러를 발생시켜 주의를 준다.

해당 옵션이 있는 이유는 Property 의 값이 초기화되어있지 않으면 사용자가 Class 의 인스턴스로부터 해당 Property 에 접근할 경우에는 `undefined` 일 수 있기 때문이다.

즉, **Compile time** 에서는 선언된 Type 대로 Property 가 typing 되지만 **Run time** 에서는 **`undefined` 로 Type 이 달라진다.**

```typescript
class Square {
  area: number; // error TS2564: Property 'area' has no initializer and is not definitely assigned in the constructor
  sideLength: number; // error TS2564: Property 'area' has no initializer and is not definitely assigned in the constructor
}

// 사용자는 시도조차 할 수 없도록 만든다.

const square = new Square();
console.log(square.area);
console.log(square.sideLength);
```

그럼 안전하게 사용하는 방법은?

1. 아래처럼 **선언과 동시에 초기화**해주는 방법이 있다.

```typescript
class Square2 {
  area: number = 0;
  sideLength: number = 0;
}
```

2. **생성자 함수(constructor) 에서 초기화**해주는 방법도 있다.

```typescript
class Square3 {
  area: number;
  sideLength: number;

  constructor(value: number) {
    this.area = value;
    this.sideLength = value ** 3; // 세제곱
  }
}

const test = new Square3(2);
console.log(test.sideLength); // 8
```

### Class Property 의 타입 추론 (v4.0.2)

```typescript
class Square4 {
  area;
  sideLength;
  // any 가 아니라 생성자에 의해 number 로 추론된다.

  constructor(value: number) {
    this.area = value;
    this.sideLength = value ** 3; // 세제곱
  }
}
```

#### 한계점) 생성자를 벗어나면 추론되지 않는다.

반드시 생성자 내부에서 Property 를 초기화하지 않아도 우리는 Property 가 반드시 초기화 되도록 코드를 작성할 수 있다.

**하지만 TypeScript 는 이를 인식하지 못한다.** (한계)

이럴때는 **`!`** 라는 Type Assertion 을 사용해 **해당 변수의 type 이 확실하다는 것을 강제로 명시**할 수 있다. (TypeScript 는 해당 변수의 type check 을 **건너뛴다.**)

하지만 `!` 과 같은 Type Assertion 은 반드시 사용에 주의해야한다. (타입이 무너지고 예외를 놓쳐 마치 JS 처럼 **추적하기 힘든 bug** 를 발생시킬 수 있다.)

```typescript
class Square5 {
  // (추론하지 못함)
  sideLength; // error: TS(7008) Member 'sideLength' implicitly has an 'any' type.
  sideLength: number; // error: TS(2564) Property 'sideLength' has no initializer and is not definitely assigned in the constructor.

  // Type Assertion `!` 사용
  sideLength!: number; // OK

  constructor(length: number) {
    this.initialize(length);
  }

  // 초기화해주는 method
  initialize(length: number) {
    this.sideLength = length;
  }

  get area() {
    return this.sideLength ** 2;
  }
}
```
