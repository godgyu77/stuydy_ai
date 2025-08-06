1장 리액트 개발을 위해 꼭알아야 할 자바스크립트
1.1 자바스크립트의 동등 비교
- 리액트 컴포넌트의 렌더링이 일어나는 이유 중 하나가 바로 props의 동등비교에따른 결과
- 리액트의 가상 DOM과 실제 DOm의 비교, 리액트 컴포넌트가 렌더링 할 지를 판단하는 방법, 변수나 함수의 메모이제이션 등 모든 작업은 자바스크립트의 동등 비교를 기반으로 함
1.1.1 자바스크립트의 데이터 타입
 1) 원시 타입(primitive type) 
    - 객체가 아닌 다른 모든 타입, 객체가 아니므로 메서드를 갖지않음
    boolean - 참(true)과 거짓(fals)만 가질 수 있는 데이터 타입
    null - 아직 값이 없거나 비어 있는 값을 표현할 때 사용
    undefined - 선언한 후 값을 할당하지 않은 변수 또는 값이 주어지지 않은 인수에 자동으로 할당되는 값
    number - 숫자
    bigint - number가 다룰 수 있는 숫자 크기의 제한을 넘어서 더 큰 숫자를 저장할 수 있음
    string - 텍스트 타입의 데이터를 저장하기 위해 사용
    symbol - 중복되지 않는 어떠한고유한 값을 나타내기 위해 사용

 2) 객체 타입(object/reference type) 
    - 원시 타입 이외의 모든것, 자바스크립트를 이루고 있는 대부분의 타입(배열,함수,정규식,클래스등)
    object - 참조를 전달한다고 해서 참조타입으로도 불림

1.1.2 값을 저장하는 방식의차이
- 원시타입과 객체타입의 가장 큰 차이점 => 값을 저장하는 방식의 차이

let hello = 'hello world' 
let hi = hello
console.log(hel.Io == hi) // true

- 원시타입은 불변 형태의 값으로 저장됨. 이 값은 변수 할당시점에 메모리 영역을 차지하고 저장됨
- 값을 비교하기 때문에 전달하는 방식이 아닌 각각 선언하는 방식에도 같은 결과를 바라보게됨

// 다음 객체는 완벽하게 동일한 내용을 가지고 있다.
 var hello = { 
greet: 'hello, world',
 }
 var hi = { 
greet: 'hello, world1,
 }
 // 그러나 동등 비교를 하면 false가 나온다. 
console.log(hell.o === hi) // false
 // 원시값인 내부 속성값을 비교하면 동일하다.
 console.log(hello.greet === hi.greet) // true

 - 객체는 원시 값과 다르게 변경 가능한 형태로 저장
 - 객체타입은 값이 아닌 참조를 전달
 - 객체는 값을 저장하는게 아닌 참조를 저장하기 때문에 동일하게 선언했던 객체라 하더라도 저장하는 순간 다른 참조를 바라보므로 false를 반환

 var hello = {
 greet: ’hello, world',
 }
 var hi = hello
 console.log(hi === hello) // true

  - hello와 hi 변수, 변수명 및 가리키는 주소가 다르지만 value가 가리키는 주소가 같으므로 둘의 비교는 언제나 true

  - 자바스크립트 개발자는 객체간 비교가 발생하면 항상 true가 아닐수 있다는 것을 인지해야 함

1.1.3 Object.is
 - == 비교는 같음을 비교하기 전에 같은 타입이 아니라면 비교할 수 있게 강제로 형변환 후 비교, 값이 동일하다면 true 반환
 - === 비교는 타입까지 비교하므로 값이 같아도 타입이 다르다면 false 반환
 - Object.is는 ==와 ===가 만족하지 못하는 몇가지 특이 케이스들을 적용함

 ex) 
 -0 === +0 // true 
 Object.is(-0, +0) // false
 Number.NaN = NaN // false
 Object.is(Number.NaN, NaN) //true
 NaN = 0 / 0 // false
 Object.is(NaN, 0/0) //true

  - 그러나 Object.is도 객체 비교는 별 차이 없음

1.1.4 리액트에서의 동등비교
 - 리액트에서 동등 비교는 Object.is
 - 리액트에서 비교는 Object.is로 먼저 비교를 수행한 다음 Object.is에서 수행하지 못한 비교(객체간 얕은 비교)를 한번 더 수행함

 // Object.is는 참조가 다른 객체에 대해 비교가 불가능하다.
 Object.is({ hello: 'world' }, { hello: 'world' }) // false
 // 반면 리액트 팀에서 구현한 shallowEqual은 객체의 1 depth까지는 비교가 가능하다.
 shallowEqual({ hello: 'world' }, { hello: 'world' }) // true
 // 그러나 2 depth까지 가면 이를 비교할 방법이 없으므로 false를 반환한다.
 shallowEqual({ hello: { hi: 'world' } }, { hello: { hi: 'world' } }) // false

1.1.5 정리
 - 리액트는 자바스크립트를 기반으로 개발되어 있으므로 언어적인 한계를 뛰어넘지 못하고 얕은 비교만을 사용해 비교를 수행하고 있음
 - 위와 같은 자바스크립트의 특징을 잘 숙지한다면 향후 함수 컴포넌트에서 사용되는 훅의 의존성 배열 비교, 렌더링 방지를 넘어선 useMemo와 useCallback 필요성, 렌더링최적화를 위해 꼭 필요한 React.memo를 올바르게 작동시키기 위해 고려해야 할 것들을 쉽게 이해할 수 있음