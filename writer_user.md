## 작성자와 사용자

타입이란 해당 변수가 **할 수 있는 일을 결정**한다. 특히 작성자는 해당 변수의 타입을 정확히 명시해 사용자가 변수를 의도된 방식으로 사용하도록 해야한다.

타입 시스템에는 크게 두가지가 있다.

1. 컴파일러에게 사용하는 타입을 **명시적으로 지정**하는 시스템
2. 컴파일러가 **자동으로 추론하는 시스템** (명시하지 않을 경우)

- JavaScript

```javascript
function f2(a) {
  return a * 12;
}

console.log(f2(10));
console.log(f2("Eco"));
```

function `f2` 의 작성자는 매개변수 `a` 를 `number` 타입으로 가정하고 함수를 작성했을 것이다.

하지만 사용자는 그것을 인지하지 못하고 `string` 을 매개변수로 넣는 일이 발생할 수 있다.

- TypeScript

  - `noImpilicitAny` 옵션으로 방어

    ```typescript
    function f3(a) {
      return a * 12;
    }
    ```

    TypeScript 로 function `f3` 를 작성했지만 매개변수 `a` 의 type 이 `any` 로 추정될 경우 JavaScript 에서의 문제가 동일하게 발생한다.

    이렇게 `any` type 이 추론될 경우 **컴파일 단계에서 error 를 발생**시켜 의도치 않은 방식의 함수 활용을 **방어**할 수 있다.

  - `strictNullChecks` 옵션으로 방어

    ```typescript
    function f4(a: number) {
      if (a > 0) {
        return a * 12;
      }
    }

    console.log(f4(5)); // 60
    console.log(f4(-5) + 5); // error TS2532: Object is possibly 'undefined'
    ```

    위 코드에선 매개변수의 type 을 명시해 `any` 타입을 방어했지만, 함수의 `return` 타입이 `number` 과 `undefined` 를 포함하게 된다.

    `null` 과 `undefined` 값은 default 로 모든 타입의 변수에 assign 될 수 있다. 이는 종종 코드 작성을 간편하게 해준다.  
    하지만 작성자가 `null` 과 `undefined` 의 처리를 제대로 하지 못한 경우 셀 수 없는 bug 가 발생할 수 있는 위험이 있다.

    TypeScript 에서는 `strictNullChecks` 옵션을 켜면 `null` 과 `undefined` 를 변수에 부여하지 못하도록 막아 다양한 예외로부터 방어해준다.

    즉, `f4` 의 `return` 타입은 `number | undefined` 로 추론되고 `undefined` 는 부여될 수 없으므로 두번째 호출문에서 `Object is possibly 'undefined'` error 가 발생한다.

    이런 컴파일 에러를 고쳐야하기 때문에 사용자와 작성자 간의 의논이 필요하다.

    ```typescript
    // error TS2366: Function lacks ending return statement and return type does not include 'undefined'
    function f4(a: number): number {
        if (a > 0) {
            return a \* 12;
        }
    }
    ```

    하지만, 위의 코드처럼 함수의 `return` 타입을 **명시**하는 것이 더 좋은 방법의 작성법이다.

    : 함수의 모습을 정확히 정해두고 그 모습과 맞는지 비교해가며 작성 가능하기 때문이다. (위처럼 error 메세지를 참고하면 좋다.)

    - `noImplictReturns` 옵션

    ```typescript
    // error TS7030: Not all code paths return a value
    function f5(a: number) {
      if (a > 0) {
        return a * 38;
      }
    }
    ```

    해당 옵션을 켜면, 모든 가짓수에서 묵시적 `return` 을 허용하지 않게 된다.

    - 함수를 원하는 모습으로 더 정확히 작성할 수 있다.
