# 1장 리액트 개발을 위해 알아야 할 자바스크립트

본 장은 리액트 개발을 위한 필수 자바스크립트 지식들을 다룹니다. 각 절에서는 데이터 타입과 비교 방식, 함수와 클로저, 비동기 이벤트 루프, 자주 쓰이는 문법, 타입스크립트 등 리액트에 대한 이해를 높이는 데 필요한 내용을 정리했습니다.

## 1.1 자바스크립트의 동등 비교

리액트의 렌더링에는 **동등 비교**가 핵심입니다. 가상 DOM과 실제 DOM을 비교할 때, 렌더 여부 결정, 메모이제이션 등 모든 판단은 자바스크립트의 비교 규칙을 따릅니다.

### 1.1.1 데이터 타입

#### 원시 타입 (primitive)

- **boolean**: `true` 또는 `false`
- **null**: 값이 비어 있음을 표현
- **undefined**: 값이 주어지지 않았을 때 자동 할당
- **number**: 숫자
- **bigint**: 더 큰 정수 표현
- **string**: 텍스트 데이터
- **symbol**: 고유한 값 표현

#### 객체 타입 (object/reference)

배열·함수·정규식·클래스 등 원시 타입이 아닌 모든 값은 객체(참조 타입)입니다. 객체는 주소(참조)를 통해 전달됩니다.

### 1.1.2 값 저장 방식

원시값과 객체는 저장 방식이 다릅니다.

```js
let hello = 'hello world';
let hi = hello;
console.log(hello === hi); // true
````

* **원시 타입**은 값 자체를 저장하는 불변 데이터입니다. 같은 값을 다른 변수에 할당해도 독립적인 값으로 인식합니다.
* **객체 타입**은 참조를 저장합니다. 동일한 내용을 가진 두 객체를 비교하면 항상 `false`입니다. 동일한 참조일 때만 `true`를 반환합니다.

```js
const a = {greet: 'hello'};
const b = {greet: 'hello'};
console.log(a === b); // false
console.log(a.greet === b.greet); // true

const c = a;
console.log(c === a); // true
```

### 1.1.3 `Object.is`

* `==` 비교: 타입이 다를 경우 형변환 후 값 비교
* `===` 비교: 타입과 값 모두 일치해야 `true`
* `Object.is()`: `==`과 `===`이 놓치는 특수 케이스(`NaN`, `+0/-0`)를 처리합니다.

```js
Object.is(-0, +0);      // false
Object.is(NaN, NaN);    // true
```

### 1.1.4 리액트에서의 비교

리액트는 내부적으로 `Object.is`를 사용해 비교합니다. `Object.is`로 처리하지 못하는 경우에는 \*\*얕은 비교(shallowEqual)\*\*를 한 번 더 수행하여 1-depth 객체까지 비교합니다.

```js
import shallowEqual from 'react-redux/lib/utils/shallowEqual';

Object.is({x:1}, {x:1});          // false
shallowEqual({x:1}, {x:1});       // true
shallowEqual({x:{y:1}}, {x:{y:1}}); // false
```

### 1.1.5 정리

리액트가 자바스크립트의 비교 규칙을 그대로 따르기 때문에, **얕은 비교** 이상의 비교를 기대하면 안 됩니다. 객체 간 비교는 같은 참조인지 확인할 뿐 내용을 비교하지 않습니다.

## 1.2 함수

함수는 작업을 수행하거나 값을 계산하는 실행 단위입니다. 리액트 컴포넌트도 함수로 표현할 수 있으며, 자바스크립트에서 함수는 일급 객체입니다.

### 1.2.1 함수 정의 방법

#### 함수 선언문

```js
function add(a, b) {
  return a + b;
}
```

#### 함수 표현식

```js
const sum = function (a, b) {
  return a + b;
};
const result = sum(10, 24); // 34
```

함수 표현식은 변수에 할당되므로 **호이스팅** 시점에 `undefined`가 됩니다. 선언식과 표현식은 필요에 따라 일관되게 사용하세요.

#### Function 생성자

드물게 사용되며 권장되지 않습니다.

```js
const add = new Function('a', 'b', 'return a + b');
```

#### 화살표 함수

화살표 함수는 간결하고 `this` 바인딩이 없습니다.

```js
const add = (a, b) => a + b;
```

**특징**

1. **생성자로 사용 불가**: `new`와 함께 호출할 수 없습니다.
2. **`arguments` 없음**: 화살표 함수 내부에서 `arguments` 객체를 사용할 수 없습니다.
3. **`this` 바인딩**: 상위 스코프의 `this`를 그대로 사용합니다.

예를 들어 클래스 컴포넌트에서 일반 메서드는 `this`가 `undefined`일 수 있지만, 화살표 함수로 정의하면 상위 `this`가 유지됩니다.

```js
class Component extends React.Component {
  state = { counter: 1 };

  functionCountUp() {
    this.setState(prev => ({ counter: prev.counter + 1 }));
  }

  ArrowFunctionCountUp = () => {
    this.setState(prev => ({ counter: prev.counter + 1 }));
  };
}
```

### 1.2.2 다양한 함수

* **즉시 실행 함수**: 정의와 동시에 실행됩니다.

  ```js
  (function(a,b){ return a + b; })(10, 24);
  ((a,b) => a + b)(10, 24);
  ```

  이름이 없으며 한 번만 실행되어 독립적 스코프를 만듭니다.

* **고차 함수**: 함수를 인수로 받거나 반환하는 함수입니다.

  ```js
  const doubled = [1, 2, 3].map(item => item * 2); // [2,4,6]

  const add = a => b => a + b;
  const sum = add(1)(3); // 4
  ```

### 1.2.3 함수 작성 시 주의사항

1. **부수 효과를 억제**: 순수 함수는 같은 입력에 항상 같은 출력을 반환하며 외부 상태를 변경하지 않습니다. 리액트에서는 부수 효과를 `useEffect`에서 다루는 것이 적절합니다.
2. **함수는 작게 분할**: 하나의 함수는 한 가지 일을 수행해야 합니다. 길고 복잡한 함수는 가독성을 떨어뜨립니다.
3. **명확한 이름 사용**: 함수명과 변수명은 간결하면서도 역할을 설명해야 합니다. 콜백 함수에도 이름을 붙이면 가독성이 좋아집니다.

### 1.2.4 정리

함수의 설계는 재사용성과 가독성에 큰 영향을 줍니다. 리액트 훅을 사용할 때도 함수의 부수 효과를 줄이고 간결하게 작성해야 합니다.

## 1.3 클래스

리액트 16.8 이전에는 컴포넌트를 클래스 기반으로 많이 작성했습니다. 클래스는 특정 형태의 객체 생성을 돕는 템플릿 역할을 합니다.

### 1.3.1 클래스 구성 요소

```js
class Car {
  constructor(name) {
    this.name = name;    // 프로퍼티
  }
  honk() {
    console.log(`${this.name}이 경적을 울립니다!`);
  }
  static hello() {
    console.log('저는 자동차입니다.');
  }
  set age(value) {
    this.carAge = value;
  }
  get age() {
    return this.carAge;
  }
}

const myCar = new Car('자동차');
myCar.honk();  // 인스턴스 메서드
Car.hello();    // 정적 메서드
myCar.age = 3;
console.log(myCar.age); // 3
```

* **constructor**: 생성 시 호출되어 초기화 작업을 합니다. 한 클래스에는 하나만 정의할 수 있습니다.
* **프로퍼티**: 생성자 안에서 `this`에 값을 할당해 선언합니다.
* **getter/setter**: 값을 읽거나 설정할 때 원하는 로직을 넣을 수 있습니다.
* **인스턴스 메서드**: 프로토타입에 정의되며 객체에서 호출할 수 있습니다.
* **정적 메서드**: 클래스 자체에서 호출하며 `this`는 클래스 자체를 가리킵니다.

#### 상속

`extends` 키워드로 상속을 구현합니다.

```js
class Truck extends Car {
  constructor(name) {
    super(name); // 부모 클래스 생성자 호출
  }
  load() {
    console.log('짐을 싣습니다.');
  }
}

const truck = new Truck('트럭');
truck.honk(); // 트럭 경적을 울립니다!
truck.load(); // 짐을 싣습니다!
```

### 1.3.2 클래스와 프로토타입

ES6 이전에는 프로토타입을 직접 사용하여 클래스와 유사한 패턴을 구현했습니다. 클래스는 프로토타입 기반 상속을 더 이해하기 쉽게 표현한 **문법적 설탕**입니다.

### 1.3.3 정리

클래스를 이해하면 리액트의 클래스 컴포넌트에서 사용하는 **생명주기 메서드**와 `this` 바인딩, 메서드 정의 차이 등을 이해할 수 있습니다.

## 1.4 클로저

함수형 컴포넌트를 이해하려면 **클로저** 개념이 필수입니다. 리액트 훅은 클로저를 활용하여 상태를 관리합니다.

### 1.4.1 정의

클로저는 **함수와 그 함수가 선언된 어휘적 환경의 조합**입니다. 함수가 중첩될 때 내부 함수는 외부 함수의 변수에 접근할 수 있으며, 외부 함수 호출이 끝난 후에도 그 환경을 기억합니다.

```js
function add() {
  const a = 10;
  function innerAdd() {
    const b = 20;
    console.log(a + b);
  }
  innerAdd(); // 30
}
```

### 1.4.2 변수 스코프

* **전역 스코프**: 어디에서든 접근 가능한 변수
* **함수 스코프**: `var`로 선언된 변수는 함수 범위에 한정되며 블록(`{}`)은 스코프를 생성하지 않습니다.
* **블록 스코프**: `let`과 `const`는 블록 범위를 가지며 루프나 조건문 내에서 유효합니다.

### 1.4.3 클로저 활용

전역 변수는 누구나 수정 가능하므로 안전하지 않습니다. 클로저를 이용해 상태를 은닉할 수 있습니다.

```js
function Counter() {
  let counter = 0;
  return {
    increase() { return ++counter; },
    decrease() { return --counter; },
    getCounter() { return counter; },
  };
}

const c = Counter();
c.increase(); // 1
c.increase(); // 2
c.decrease(); // 1
```

리액트의 `useState` 훅도 클로저를 통해 상태를 유지합니다.

### 1.4.4 주의할 점

루프 안의 `var` 사용 시 클로저 문제가 발생할 수 있습니다. `let`을 사용하거나 즉시 실행 함수를 이용해 각 반복마다 새로운 스코프를 만듭니다.

```js
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), i * 1000);
}
// 5 5 5 5 5

for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), i * 1000);
}
// 0 1 2 3 4
```

클로저는 메모리를 계속 점유하므로 꼭 필요한 경우에만 사용해야 합니다.

### 1.4.5 정리

클로저는 함수형 프로그래밍의 핵심 개념입니다. 리액트 훅의 상태 관리도 클로저를 이용하므로, 원리를 이해하는 것이 중요합니다.

## 1.5 이벤트 루프와 비동기 통신

자바스크립트는 싱글 스레드 언어로 코드가 **동기식**으로 실행됩니다. 그러나 웹 API와 태스크 큐를 통해 **비동기 작업**을 처리할 수 있습니다.

### 1.5.1 싱글 스레드

* 과거에는 프로그램마다 하나의 프로세스만 할당되었지만, 복잡도가 증가하며 스레드가 도입되었습니다.
* 자바스크립트는 스레드 한 개로 실행되며, 긴 작업이 있으면 다음 코드 실행이 지연됩니다. 이러한 특성을 **Run-to-completion**이라고 부릅니다.

### 1.5.2 이벤트 루프와 호출 스택

호출 스택은 실행할 함수가 쌓이는 구조입니다. 이벤트 루프는 호출 스택과 **태스크 큐**를 모니터링하여, 호출 스택이 비면 큐에 대기 중인 콜백을 실행합니다. `setTimeout`, `Promise` 등 비동기 작업의 콜백들은 큐에 들어갑니다.

```js
setTimeout(() => console.log('timeout'), 0);
Promise.resolve().then(() => console.log('microtask'));
// microtask
// timeout
```

마이크로 태스크 큐는 태스크 큐보다 우선하여 실행됩니다.

### 1.5.3 정리

싱글 스레드 환경에서도 이벤트 루프, 태스크 큐, 마이크로 태스크 큐를 통해 많은 비동기 작업을 처리할 수 있습니다. 이 구조를 이해하면 리액트의 비동기 동작을 더 잘 예측할 수 있습니다.

## 1.6 리액트에서 자주 사용하는 자바스크립트 문법

리액트를 이해하려면 최신 자바스크립트 문법도 숙지해야 합니다. 다양한 브라우저에서 안정적으로 실행하기 위해 **바벨** 같은 트랜스파일러를 사용합니다.

### 1.6.1 구조 분해 할당

배열과 객체의 값을 분해하여 변수에 할당할 수 있습니다.

#### 배열 구조 분해

```js
const array = [1, 2, 3, 4, 5];
const [first, second, third, ...rest] = array;
// first = 1, second = 2, third = 3, rest = [4,5]

const [a=10, b=10, c=10] = [1, 2];
// a=1, b=2, c=10

const [x, , , , y] = array;
// x=1, y=5
```

#### 객체 구조 분해

```js
const obj = {a:1, b:2, c:3, d:4};
const {a, b, ...others} = obj;
// a=1, b=2, others={c:3,d:4}

// 기본값과 다른 변수명 사용
const {x=10, y:alias=20} = {y:5};
// x=10 (기본값), alias=5

function SampleComponent({a, b}) {
  return a + b;
}
```

구조 분해는 컴포넌트의 `props`에서 필요한 값만 쉽게 꺼낼 때 유용합니다.

### 1.6.2 전개(스프레드) 구문

배열, 객체, 문자열 등 반복 가능한 값을 펼쳐서 간결하게 사용할 수 있습니다.

```js
// 배열 전개
const arr1 = ['a', 'b'];
const arr2 = [...arr1, 'c', 'd']; // ['a','b','c','d']

// 객체 전개
const obj1 = {a:1, b:2};
const obj2 = {c:3, d:4};
const merged = {...obj1, ...obj2}; // {a:1,b:2,c:3,d:4}

// 전개 구문 이후 덮어쓰기 순서 주의
const override1 = {...obj, c:10};
const override2 = {c:10, ...obj};
// override1: c가 10
// override2: c가 obj.c 값으로 덮어짐
```

### 1.6.3 객체 초기자

객체를 선언할 때 변수 이름과 동일한 키를 간결하게 넣을 수 있습니다.

```js
const x = 1;
const y = 2;
const point = {x, y}; // {x:1, y:2}
```

### 1.6.4 배열 메서드: `map`, `filter`, `reduce`, `forEach`

* **`map`**: 배열의 각 요소를 변환하여 새 배열을 만듭니다.

  ```js
  const arr = [1,2,3];
  const doubled = arr.map(n => n * 2); // [2,4,6]
  ```
* **`filter`**: 조건을 만족하는 요소만 새 배열로 반환합니다.

  ```js
  const even = arr.filter(n => n % 2 === 0); // [2]
  ```
* **`reduce`**: 누산기를 통해 하나의 값으로 합칩니다.

  ```js
  const sum = arr.reduce((acc, cur) => acc + cur, 0); // 6
  ```
* **`forEach`**: 각 요소에 대해 콜백을 실행하지만 반환값이 없습니다. 중간에 멈출 수 없습니다.

  ```js
  arr.forEach(n => console.log(n));
  ```

### 1.6.5 삼항 조건 연산자

`조건 ? 참일 때 : 거짓일 때` 형식으로 간단한 조건식을 작성합니다.

```js
const value = 10;
const result = value % 2 === 0 ? '짝수' : '홀수';
```

### 1.6.6 정리

리액트에서는 최신 ES 문법을 자주 사용합니다. 브라우저 호환성을 위해 바벨 등의 트랜스파일러가 필요하며, 코드의 가독성을 높이는 데 도움이 됩니다.

## 1.7 타입스크립트

타입스크립트는 자바스크립트에 타입 시스템을 도입하여 런타임 이전에 오류를 잡고 코드를 더 안전하게 만듭니다.

### 1.7.1 개요

타입스크립트는 자바스크립트의 슈퍼셋 언어로, 빌드 단계에서 타입 검사를 수행합니다. 변수나 함수 매개변수에 타입을 지정하여 런타임 에러를 줄일 수 있습니다.

```ts
function divide(a: number, b: number): number {
  return a / b;
}

// divide('안녕', '하이'); // 타입 오류
```

### 1.7.2 유용한 기능

* **`unknown`**: 정확한 타입을 모를 때 `any` 대신 사용합니다. 사용 전에 타입 검사를 해야 합니다.
* **타입 가드**: `instanceof`, `typeof`, `in` 등을 사용하여 타입을 좁힙니다.
* **제네릭**: 다양한 타입에 대응할 수 있는 코드 재사용을 돕습니다.

```ts
function getFirstAndLast<T>(list: T[]): [T, T] {
  return [list[0], list[list.length - 1]];
}

const [first, last] = getFirstAndLast([1,2,3]); // first: number
const [f, l] = getFirstAndLast(['a','b','c']);   // f: string
```

* **인덱스 시그니처**: 동적인 객체 타입을 표현할 수 있습니다.

  ```ts
  type Greeting = {
    [key: string]: string;
  };
  const hello: Greeting = { hello: 'hello', hi: 'hi' };
  ```

### 1.7.3 전환 가이드

자바스크립트 프로젝트를 타입스크립트로 전환할 때는 **점진적**으로 진행하는 것이 좋습니다. `tsconfig.json`을 설정하고, JSDoc과 `@ts-check`를 활용하여 서서히 타입을 추가하세요.

### 1.7.4 정리

타입스크립트의 도입은 코드 안정성을 높여주지만, 자바스크립트에 대한 이해가 전제되어야 합니다.

# 2장 리액트 핵심 요소 깊게 살펴보기

리액트 내부 구조를 이해하면 성능 최적화와 유지보수에 도움이 됩니다. 여기서는 JSX, 가상 DOM, 파이버 구조, 클래스/함수 컴포넌트, 렌더링 과정, 메모이제이션에 대해 설명합니다.

## 2.1 JSX란?

JSX는 XML과 유사한 리액트만의 문법으로, 자바스크립트에서 HTML처럼 UI 구조를 표현할 수 있도록 해줍니다. JSX는 브라우저에서 실행되지 않으므로 반드시 **트랜스파일** 과정이 필요합니다.

### 2.1.1 JSX 구성 요소

* **JSXElement**: 가장 기본 요소로, `<tag>내용</tag>` 형태입니다. 자식이 없는 경우 `<tag />`처럼 self-closing합니다.
* **JSXFragment**: 빈 요소를 의미하는 `<> </>` 형태입니다.
* **JSXIdentifier / JSXMemberExpression / JSXNamespacedName**: 요소의 이름을 지정하는 방식입니다. 점(`.`)이나 콜론(`:`)을 사용해 네임스페이스를 구분할 수 있습니다.
* **JSXAttributes**: 요소의 속성을 `{key="value"}` 형태로 표현합니다. 전개 연산자를 통해 여러 속성을 한 번에 넘길 수도 있습니다.
* **JSXChildren**: 요소의 자식으로 텍스트, 다른 JSX, 혹은 `{}` 내부의 자바스크립트 표현식이 올 수 있습니다.

### 2.1.2 JSX 예제

```jsx
const ComponentA = <A>안녕하세요.</A>;
const ComponentB = <A />;
const ComponentC = <A {...{required: true}} />;
const ComponentD = <A required />;
const ComponentE = <A required={false} />;

const ComponentF = (
  <A>
    <B text="리액트" />
  </A>
);

const ComponentG = (
  <A>
    <B optionalChildren={<>안녕하세요.</>} />
  </A>
);

const ComponentH = (
  <A>
    안녕하세요
    <B text="리액트" />
  </A>
);
```

### 2.1.3 JSX의 변환

JSX는 컴파일 시 `React.createElement()` 호출로 변환됩니다.

```jsx
const ComponentA = <A required={true}>Hello World</A>;
// 변환 결과
var ComponentA = React.createElement(
  A,
  { required: true },
  'Hello World'
);

const ComponentB = <>Hello World</>;
// 변환 결과
var ComponentB = React.createElement(React.Fragment, null, 'Hello World');
```

### 2.1.4 정리

JSX는 브라우저가 이해할 수 없는 문법이므로 트랜스파일해야 합니다. JSX는 HTML과 비슷하지만 실제로는 자바스크립트 함수 호출로 변환됩니다.

## 2.2 가상 DOM과 리액트 파이버

### 2.2.1 DOM과 렌더링

DOM(Document Object Model)은 웹페이지 구조와 콘텐츠를 표현하는 객체입니다. 브라우저는 DOM을 기반으로 HTML과 CSS를 렌더링합니다.

### 2.2.2 가상 DOM의 탄생

실제 DOM 조작은 느리며 비용이 많이 듭니다. 가상 DOM을 사용하면 변경 사항을 메모리상의 트리에 먼저 적용하고, 실제 DOM과 차이를 비교하여 최소한의 변경만 반영할 수 있습니다. 가상 DOM이 실제 DOM보다 무조건 빠른 것은 아니지만, 대부분의 애플리케이션에서 충분한 성능을 제공합니다.

### 2.2.3 리액트 파이버

파이버는 리액트의 가상 DOM 구현체로, 작업을 작은 단위로 분할하고 우선순위를 부여합니다. 파이버는 다음과 같은 기능을 제공합니다.

* 작업을 쪼개서 일시 중지·재개
* 이전 작업을 재사용하거나 폐기
* 비동기적으로 실행하여 사용자 인터랙션을 부드럽게 유지

리액트는 현재 화면을 나타내는 파이버 트리와 작업 중 트리(**workInProgress**) 두 개를 유지하며, 작업 완료 후 포인터를 변경해 최신 상태로 교체합니다. 이를 **더블 버퍼링**이라고 합니다.

### 2.2.4 파이버와 가상 DOM

각 리액트 요소는 파이버와 1:1로 대응합니다. 파이버는 비동기적으로 업데이트되며, 변경이 확정되면 DOM에 동기적으로 커밋됩니다.

### 2.2.5 정리

가상 DOM과 파이버는 브라우저의 DOM 자체를 빠르게 조작하기 위한 것이 아니라, **값으로 UI를 표현**하기 위한 메커니즘입니다. 메모리에서 계산 후 필요한 부분만 실제 DOM에 적용함으로써 성능을 높입니다.

## 2.3 클래스 컴포넌트와 함수 컴포넌트

### 2.3.1 클래스 컴포넌트

클래스 컴포넌트는 `React.Component`를 상속하여 생성합니다.

```tsx
import React from 'react';

interface SampleProps {
  required?: boolean;
  text: string;
}

interface SampleState {
  count: number;
  isLimited?: boolean;
}

class SampleComponent extends React.Component<SampleProps, SampleState> {
  constructor(props: SampleProps) {
    super(props);
    this.state = { count: 0, isLimited: false };
  }

  handleClick = () => {
    const newValue = this.state.count + 1;
    this.setState({ count: newValue, isLimited: newValue >= 10 });
  };

  render() {
    const { required, text } = this.props;
    const { count, isLimited } = this.state;
    return (
      <h2>
        Sample Component
        <div>{required ? '필수' : '필수 아님'}</div>
        <div>문자: {text}</div>
        <div>count: {count}</div>
        <button onClick={this.handleClick} disabled={isLimited}>증가</button>
      </h2>
    );
  }
}
```

**주요 속성**

* **constructor**: 컴포넌트 초기화 시 호출되며 `super(props)`를 통해 부모 생성자를 호출합니다.
* **props**: 부모 컴포넌트로부터 전달받는 읽기 전용 값입니다.
* **state**: 컴포넌트 내부에서 관리하는 값으로, 항상 객체 형태이며 변경 시 렌더링을 트리거합니다.
* **메서드**: 이벤트 핸들러 등 렌더링에 필요한 함수를 클래스 메서드로 선언합니다.

#### 생명주기 메서드

클래스 컴포넌트에는 다양한 생명주기 메서드가 존재하여 특정 시점에 동작을 수행할 수 있습니다.

| 단계                             | 설명                                                                                      |
| ------------------------------ | --------------------------------------------------------------------------------------- |
| **render()**                   | UI를 렌더링합니다. 순수해야 하며 부수 효과를 일으키지 않아야 합니다.                                                |
| **componentDidMount()**        | 마운트 후 한 번 호출되며 데이터 요청 등 부수 효과를 처리할 수 있습니다.                                              |
| **componentDidUpdate()**       | 업데이트 직후 호출됩니다. 이전 `props` 및 `state`에 접근할 수 있습니다.                                        |
| **componentWillUnmount()**     | 컴포넌트가 제거될 때 호출되어 리소스 정리 및 클린업 작업을 수행합니다.                                                |
| **shouldComponentUpdate()**    | 리렌더링을 방지할 수 있습니다. `true`를 반환하면 렌더링, `false`면 건너뜁니다.                                     |
| **getDerivedStateFromProps()** | `render()` 이전에 호출되며 `state`를 갱신할 수 있습니다. 정적 메서드이므로 `this`에 접근할 수 없습니다.                  |
| **getSnapshotBeforeUpdate()**  | DOM 업데이트 직전에 호출되어 스크롤 위치 등 정보를 반환할 수 있습니다. 이 값은 `componentDidUpdate()`의 세 번째 인수로 전달됩니다. |
| **getDerivedStateFromError()** | 자식 컴포넌트에서 발생한 오류를 포착해 `state`를 업데이트할 수 있습니다.                                            |
| **componentDidCatch()**        | 오류가 발생했을 때 호출되어 로깅 등 부수 효과를 수행할 수 있습니다.                                                 |

#### 클래스 컴포넌트의 한계

* 데이터 흐름을 추적하기 어렵고 재사용성이 낮습니다.
* 기능이 많아질수록 컴포넌트 크기가 커집니다.
* 함수보다 문법이 복잡하며 코드 분할과 핫 리로딩에도 불리합니다.

### 2.3.2 함수 컴포넌트

리액트 16.8부터 \*\*훅(Hooks)\*\*이 도입되어 함수 컴포넌트에서도 상태를 관리할 수 있게 되었습니다.

```tsx
import { useState } from 'react';

type SampleProps = {
  required?: boolean;
  text: string;
};

export function SampleComponent({ required, text }: SampleProps) {
  const [count, setCount] = useState(0);
  const [isLimited, setIsLimited] = useState(false);

  function handleClick() {
    const newValue = count + 1;
    setCount(newValue);
    setIsLimited(newValue >= 10);
  }
  return (
    <h2>
      Sample Component
      <div>{required ? '필수' : '필수 아님'}</div>
      <div>문자: {text}</div>
      <div>count: {count}</div>
      <button onClick={handleClick} disabled={isLimited}>증가</button>
    </h2>
  );
}
```

함수 컴포넌트는 단순히 `props`를 받아 JSX를 반환하는 함수이며, 훅을 사용해 상태와 생명주기 메서드 역할을 수행합니다.

### 2.3.3 비교

* **생명주기**: 함수 컴포넌트에는 클래식한 생명주기 메서드가 없으며, `useEffect` 등으로 유사한 동작을 구현합니다.
* **`this` 사용**: 함수 컴포넌트는 `this`가 없고, `props`와 `state`를 함수 인수와 훅을 통해 직접 사용합니다. 클래스는 `this`에 바인딩된 값이 변경되며 렌더링이 이루어집니다.
* **학습 곡선**: 클래스보다 함수가 익숙한 개발자가 많아 함수 컴포넌트가 더 직관적입니다.

### 2.3.4 정리

새로운 프로젝트에서는 함수 컴포넌트를 권장하지만, 기존 코드나 오류 처리와 같은 특수한 경우를 위해 클래스 컴포넌트도 이해해두어야 합니다.

## 2.4 렌더링은 어떻게 일어나는가?

### 2.4.1 정의

* **브라우저 렌더링**: HTML과 CSS를 기반으로 UI를 그리는 과정.
* **리액트 렌더링**: `props`와 `state`의 값을 기반으로 DOM을 계산하고 실제 DOM과 비교하는 과정. 렌더링 비용을 줄이는 것이 중요합니다.

### 2.4.2 언제 렌더링되는가?

* **최초 렌더링**: 애플리케이션 첫 진입 시 발생합니다.
* **리렌더링**: 이후 발생하는 모든 렌더링으로, 다음과 같은 상황에서 트리거됩니다.

  * 클래스 컴포넌트에서 `setState` 또는 `forceUpdate` 호출
  * 함수 컴포넌트에서 `useState`의 setter나 `useReducer`의 `dispatch` 호출
  * 컴포넌트의 `key` 변경
  * 부모 컴포넌트 렌더링
  * `props` 변경

단순 변수 변경은 리렌더링을 발생시키지 않으며 DOM에 반영되지 않습니다.

### 2.4.3 렌더링 프로세스

리액트는 컴포넌트 루트부터 아래로 내려가며 업데이트가 필요한 컴포넌트를 찾아 렌더링합니다. 각 컴포넌트의 결과를 수집한 뒤 이전 가상 DOM과 비교하여 변경 사항을 계산합니다. 이 과정을 \*\*재조정(reconciliation)\*\*이라고 합니다.

렌더링 과정은 두 단계로 나뉩니다.

| 단계        | 설명                                      |
| --------- | --------------------------------------- |
| **렌더 단계** | 컴포넌트를 실행하여 새 가상 DOM을 생성하고 변경 사항을 계산합니다. |
| **커밋 단계** | 실제 DOM에 변경 사항을 적용하여 브라우저에 렌더링합니다.       |

렌더링이 일어나도 변경 사항이 없으면 커밋 단계는 생략될 수 있습니다.

### 2.4.4 렌더링 시나리오 예시

```jsx
function A() {
  return (
    <div className="App">
      <h1>Hello React!</h1>
      <B />
    </div>
  );
}

function B() {
  const [counter, setCounter] = useState(0);
  return (
    <>
      <label>
        <C number={counter} />
      </label>
      <button onClick={() => setCounter(prev => prev + 1)}>+</button>
    </>
  );
}

function C({ number }) {
  return (
    <div>
      {number} <D />
    </div>
  );
}

function D() {
  return <>리액트 재밌다!</>;
}
```

부모 컴포넌트가 렌더링되면 모든 자식도 렌더링됩니다. `props`가 변경되지 않아도 리렌더링이 일어날 수 있습니다.

### 2.4.5 정리

리액트는 예상보다 자주 리렌더링이 일어납니다. 상태 변경이나 `props` 전달 방식이 전체 렌더링에 어떤 영향을 주는지 이해하는 것이 중요합니다.

## 2.5 메모이제이션

메모이제이션은 계산 결과를 저장해 다음 호출 시 재사용하는 최적화 기법으로, 리액트에서는 주로 렌더링을 줄이기 위해 사용합니다.

### 2.5.1 필요성 판단

메모이제이션은 비용을 줄이지만, 값을 비교하고 캐시를 유지하는 데도 비용이 듭니다. 간단한 연산에 메모이제이션을 적용하는 것은 오히려 비효율적일 수 있습니다. 따라서 신중히 적용해야 합니다.

### 2.5.2 전면 적용 vs 선택 적용

메모이제이션을 **일부만** 적용하는 것이 이상적이나, 규모가 큰 프로젝트에서는 모든 컴포넌트에 메모이제이션(`React.memo`, `useMemo`, `useCallback` 등)을 적용하고 필요에 따라 제거하는 방법도 고려합니다. 메모이제이션을 하지 않았을 때의 위험 비용(불필요한 렌더링, 값 재계산, 가상 DOM 비교 등)이 더 클 수 있기 때문입니다.

### 2.5.3 정리

메모이제이션은 리액트 애플리케이션의 성능을 향상시키는 방법입니다. 충분한 시간을 들여 어느 부분에서 성능 이득이 있는지 측정하고 적용하는 것이 좋습니다. 현업에서는 시간적 여유가 없을 때 미리 적용해 두는 것도 한 방법입니다.

---