1장 리액트 개발을 위해 꼭알아야 할 자바스크립트 47 ~ 143
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


 1.2 함수
1.2.1 함수란?
 - 작업을 수행하거나 값을 계산하는 등의과정을 표현하고, 이를 하나의 블록으로 감싸서 실행 단위로 만들어 놓은 것
 - 리액트에서 컴포넌트를 만드는 함수도 함수의 기본적인 형태를 따름

1.2.2 함수를 정의하는 4가지 방법
 함수 선언문
   - 자바스크립트에서 함수를 선언할 때 가장 일반적으로 사용하는 방식이다.
   function add(a, b) {
      return a + b
   }

 함수 표현식
   - 함수는 다른 함수의매개변수가 될 수도 있고 할당도 가능하므로 일급 객체라고 할수 있다.

   const sum = function (a, b) { 
      return a + b
   }
   sum(10, 24) // 34

   - 함수 표현식을 사용할때는 할당하려는 함수의 이름을 생략하는것이 일반적임.(코드를 봤을때 혼란을 방지하기 위해서)
   - 함수 표현식과 함수 선언식의 차이 => 호이스팅(hoisting)
   - 호이스팅은 함수 선언문이 마치 코드 맨 앞단에 작성된 것 처럼 작동하는 자바스크립트의 특징

   hello() // hello
   function hello() { 
      console.log('hello')
   }
   hello() // hello

   - function hello가 중간에 선언됐음에도 불구하고 맨앞에서 호출한 hello는 에러 없이 정상적으로 함수 작동 수행
   - 하지만 함수 표현식은 함수를 변수에 할당하므로 호이스팅 되는 시점에서는 undefined로 초기화 함

   console.log(typeof hello === 'undefined') // true
   hello() // Uncaught TypeError: hello is not a function
   var hello = function () {
      console.log('hello')
   }
   hello()

   - 함수표현식, 함수선언식 둘중 어느게 더 낫다고 할 수 없음, 코드 작성하는 환경을 살펴보고 상황에 맞춰서 작성법을 일관되게 사용하면 된다.

 function 생성자
   - function 생성자를 활용하는 방법, 실제로는 코딩에서 잘 사용하지 않으며 권장되지않음. 있다는정도만 알고있으면 될듯

   const add = new Function('a', ’b’, 'return a + b')
   add(10, 24) // 34

 화살표 함수
  - 최근에 자주 사용하는 함수 정의 방식
  - function이라는 키워드 대신 => 화살표를 활용해서 함수를 만듬

   const add = (a, b) => { 
      return a + b
   }
   const add = (a, b) => a + b

   - 기존 함수 생성방식과 큰 차이점들이 있음

   - 화살표 함수에서는 constructor를 사용할수 없다. -> 생성자 함수로 화살표 함수를 사용하는것은 불가능
   const Car = (name) => { 
      this.name = name
   }
   // Uncaught TypeError: Car is not a constructor
   const myCar = new Car('하이')

   - 화살표 함수에서는 arguments가 존재하지 않는다.
   function hello() { 
      console.log(arguments)
   }
   // Arguments(3) [1, 2, 3, callee: f. Symbol(Symbol.iterator): f]
   hello(l, 2, 3)

   const hi =()=>{ 
      console.log(arguments)
   }
   // Uncaught ReferenceError: arguments is not defined 
   hi(l, 2, 3)

   - 화살표 함수와 일반 함수의 가장 큰 차이점은 this 바인딩
   - this는 자신이 속한 객체나 자신이 생성할 인스턴스를 가리키는 값
   - this는 함수를 정의할때 결정되는것이 아닌 함수가 어떻게 호출되는냐에 따라 동적으로 결정
   - 일반 함수로 호출된다면 내부의 this 는 전역 객체를 가리킴
   - 화살표 함수는 자체 바인딩을 갖지않는다. 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 따르게 됨

   class Component extends React.Component {
      constructor(props) {
         super(props)
      this.state = {
         counter: 1,
      }
   }
   functionCountUp {
      console.log(this) // undefined
      this.setState((prev) => ({ counter: prev.counter + 1 }))
   }
   ArrowFunctionCountUp =()=>{
      console.log(this) // class Component
      this.setState((prev) => ({ counter: prev.counter + 1 }))
   }
   render() {
      return (
         <div>
            {/* Cannot read properties of undefined (reading 'setstate') */}
            <button onClick={this.functionCountUp}>일반 함수</button>
            {/* 정상적으로 작동한다. */}
            <button onClick={this.ArrowFunctionCountUp}>화살표 함수</button>
         </div>
         )
      }
   }

   - 별도의 작업없이 this에 접근할수 있는 방법 -> 화살표 함수

1.2.3 다양한 함수
   - 리액트에서 사용하는 다양한 함수들

 즉시 실행 함수
   - 함수를 정의하고 그 순간 즉시 실행되는 함수. 단 한번만 호출되고 다시 호출할 수 없다.
   
   (function (a, b) { 
      return a + b
   })(10, 24);// 34

   ((a, b) => {
      return a + b
    }, 
   )(10, 24) // 34

   - 즉시 실행하고 다시 호출할 수 없으므로 일반적으로 즉시 실행 함수에는 이름을 붙이지 않음
   - 함수의 선언과 실행이 바로 그 자리에서 끝나므로 독립적인 함수 스코프를 운용할 수 있다는 장점을 얻을 수 있음.
   - 코드를 읽는 사람에게도 이 함수는 다시 호출되지 않음을 알수 있으므로 리팩토링에 도움이 됨.

 고차 함수
  - 자바스크립트 함수가 일급 객체라는 특징을 활용하면 함수를 인수로 받거나 결과로 새로운 함수를 반환 시킬 수 있음.

   const doubledArray = [1, 2, 3].map((item) => item * 2)
   doubledArray // [2, 4, 6]

   // 함수를 반환하는 고차 함수의 예 
   const add = function (a) {
      // a가 존재하는 클로저를 생성 
      return function (b) {
         // b를 인수로 받아 두 합을 반환하는 또 다른 함수를 생성 
         return a + b
      }
   }
   add(l)(3) // 4

   - 이러한 특징들을 활용해서 함수 컴포넌트를 인수로 받아 새로운 함수 컴포넌트를 반환하는 고차 함수도 만들 수 있음

1.2.4 함수를 만들 때 주의사항
 1) 함수의 부수효과를 최대한 억제하라
  - 부수효과란, 함수 내의 작동으로 인해 함수가 아닌 함수 외부에 영향을 끼치는 것
  - 부수효과가 없는 함수를 순수 함수라 하고 부수 효과가 존재하는 함수를 비순수 함수라고 한다.
  - 순수함수는 어떠한 상황이든 동일한 인수를 받으면 동일한 결과를 반환해야 함, 이러한 작동 와중에 외부에 어떠한 영향도 미쳐서는 안됨
  - 항상 순수함수로만 작성해야 하는가?
   - 만드는 과정에서 부수 효과는 피할수 없음. 부수 효과를 최대한 억제할 수 있는 방향으로 함수를 설계해야 한다.(리액트 관점에서는 useEffect의 작동을 최소화하는것)
  - 자바스크립트 함수에서는 가능한 부수 효과를 최소화하고 함수의 실행과 결과를 최대한 예측 가능하도록 설계해야함
 2) 가능한 함수를 작게 만들어라
  - 함수당 코드 길이가 길어질수록 코드가 문제를 일으킬 확률이 커지고 내부에서 무슨일이 일어나는지 추적하기 어려워진다.
  - Eslint에서 기본적으로 50줄 이상이면 과도하게 커진 함수로 분류하고 경고메시지를 출력할 정도
  - 하나의 함수에서 너무나 많은 일을 하지 않게 하는것
  - 함수는 하나의 일만 잘하면 된다. 그것은 함수의 원래 목적인 재사용성을 높일 수 있는 방법
 3) 누구나 이해할 수 있는 이름을 붙여라
  - 함수나 변수에 이름을 붙일때 가능한 간결하고 이해하기 쉽게 붙이는 것이 좋다
  - 리액트에서 사용하는 useEffect나 useCallback등 훅에 넘겨주는 콜백 함수에 네이밍을 붙여주면 가독성에 도움이 된다.
  
  useEffect(function apiRequest() { 
   // ... do something
  }, [])

  - 위와같이 콜백 함수에 이름을 붙여준다 한들 apiRequest()와 같은 형태로 호출하거 접근할 수 있는것은 아니지만 해당 useEffect가 어떻게 작동하는지 파악하는데 큰 도움이 될것

1.2.5 정리
 - 함수를 많이 만들어 봤더라도 개발자 자신이 만든 함수가 함수답게 작동하기 위해 잘 설계돼 있는지 한번 뒤돌아보는 것도 좋다.

1.3 클래스
 - 리액트 16.8버전이 나오기 전까지 컴포넌트들은 클래스로 작성돼 있었음.
 - 함수로 컴포넌트를 작성한게 얼마 되지 않은 일이고 이제는 클래스 컴포넌트를 잘 사용안한다고 하더라도 과거에 작성된 코드를 읽기 위해서, 이 코드를 함수 컴포넌트로 개선하기 위해서는 자바스크립트의 클래스가 어떤 식으로 작동되는지 이해해야 함
 - 클래스에 대해 이해하면 리액트가 왜 패러다임을 바꾼지, 오래된 리액트 코드를 리팩터링 하는 데도 도움이 될것

1.3.1 클래스란?
 - 자바스크립트에서 클래스란 특정한 객체를 만들기 위한 일종의 템플릿
 - 특정한 형태의 객체를 반복적으로 만들기 위해 사용되는 것이 바로 클래스
 - 이를 활용하면 객체를 만드는데 필요한 데이터나 이를 조작하는 코드를 추상화해서 객체 생성을 더욱 편하게 할 수 있음

   // Car 클래스 선언 
   class Car {
   // constructor는 생성자다. 최초에 생성할 때 어떤 인수를 받을지 결정할 수 있으며, 
   // 객체를 초기화하는 용도로도 사용된다.
      constructor(name) { 
         this.name = name 
      }

      // 메서드 
      honk() { 
         console.log('${this.name}이 경적을 울립니다!')
      }

      // 정적 메서드 
      static hello() {
         console.log(•저는 자동차입니다,) 
      }

      // setter 
      set age(value) { 
         this.carAge = value 
      }

      // getter 
      get age() { 
         return this.carAge
      }
   }

   // Car 클래스를 활용해 car 객체를 만들었다.
   const myCar = new Car('자동차’)

   // 메서드 호출 
   myCar.honk()

   // 정적 메서드는 클래스에서 직접 호출한다.
   Car.helloO

   // 정적 메서드는 클래스로 만든 객체에서는 호출할 수 없다.
   // Uncaught TypeError: myCar.hello is not a function 
   myCar.hello()

   // setter를 만들면 값을 할당할 수 있다.
   myCar.age = 32

   // getter로 값을 가져올 수 있다.
   console.log(myCar.age, myCar.name) // 32 자동차

   constructor
   - 생성자, 객체를 생성하는데 사용하는 특수한 메서드
   - 단 하나만 존재할 수 있고 여러 개를 사용하면 에러가 발생, 별다른 수행할 작업이 없다면 생략 가능

   // X - constructor가 두개라서 에러발생생
   class Car {
      constructor (name) { 
         this.name = name
      }
      // SyntaxError： A class may only have one constructor 
      constructor (name) {
         this.name = name
      }
   }

   // o - 생략가능
   class Car {
   // constructor는 없어도 가능하다.
   }

   프로퍼티
   - 클래스로 인스턴스를 생성할 때 내부에 정의할 수 있는 속성값

   class Car {
      constructor(name) {
            // 값을 받으면 내부에 프로퍼티로 할당된다. 
            this.name = name
         }
   }

   const myCar = new Car(’자동차’) // 프로퍼티 값을 넘겨주었다.

   - constructor 내부에 빈 객체가 할당돼 있는데 해당 객체에 프로퍼티의 키와 값을 넣어서 활용할 수 있게 도와줌.

   getter & setter
   - getter : 클래스에 무언가 값을 가져올때 사용됨, 사용하기 위해서는 get을 앞에 붙여야하고 뒤이어서 getter의 이름을 선언해야함.
   class Car {
      constructor(name) { 
         this.name = name
      }
      get firstCharacter() {
         return this.name[0] 
      }
   }
   const myCar = new Car('자동차')
   myCar.firstCharacter // 자

   - setter : 클래스 필드에 값을 할당할 때 사용, set이라는 키워드를 먼저 선언하고 그 뒤를 이어서 이름을 붙여야함.
   class Car {
      constructor(name) { 
         this.name = name
      }
      get firstCharacter() { 
         return this.name[0]
      }
      set firstCharacter(char) {
         this.name = [char, ...this.name.slice(l)].join('')
      } 
   }
   const myCar = new Car('자동차')
   myCar.firstCharacter // 자

   // •차’를 할당한다.
   myCar. firstCharacter = '차,
   console.log(myCar.firstCharacter, myCar.name) // 차, 차동차

   인스턴스 메서드
   - 클래스 내부에서 선언한 메서드
   - 실제로 자바스크립트의 prototype에 선언되므로 프로토타입 메서드로 불리기도 함

   class Car { 
      constructor(name) { 
         this.name = name 
      }
      // 인스턴스 메서드 정의
      hello() {
         console• log('안녕 하세요, ${this • name}입 니다.') 
      }
   }

   const myCar = new Car('자동차') 
   myCar.hello() // 안녕하세요, 자동차입니다

   - 메서드가 prototype에 선언됐기 때문에 위와같이 인스턴스 메서드에 접근할 수 있음
   - 직접 객체에 선언하지 않았음에도 프로토타입에 있는 메서드를 찾아서 실행을 도와주는 것이 바로 프로토타입 체이닝
   - 프로토타입과 프로토타입 체이닝이라는 특성 덕분에 객체에서도 직접 선언하지 않은 클래스에 선언한 hello 메서드를 호출할 수 있고 내부에서 this도 접근해 사용할 수 있게됨

   정적 메서드
   - 클래스의 인스턴스가 아닌 이름으로 호출할 수 있는 메서드

   class Car {
      static hello() {
         console.log('안녕하세요!,) 
      }
   }
   const myCar = new Car()

   myCar.hello() // Uncaught TypeError: myCar.hello is not a function
   Car.helloO // 안녕하세요!

   - 정적 메서드 내부의 this는 클래스로 생성된 인스턴스가 아닌 클래스 자신을 가리키기 때문에 다른 메서드에서 일반적으로 사용할 수 없음
   - 리액트 클래스 컴포넌트 생명주기 메서드에서는 this.state에 접근할 수 없다.
   - this에 접근할 수 없지만 인스턴스를 생성하지 않아도 사용가능, 생성하지 않아도 접근이 가능하므로 객체를 생성하지 않고 여러곳에서 재사용 가능
   - 애플리케이션 전역에서 사용하는 유틸 함수를 정적 메서드로 많이 활용함.

   상속
   - 리액트에서 클래스 컴포넌트를 사용하기 위해서 extends React.Component 선언하는데 extends는 기존 클래스를 상속받아 자식 클래스에 상속받은 클래스를 기반으로 확장하는 개념

   class Car {
      constructor(name) { 
         this.name = name 
      }

      honk() {
         console.log('${this.name} 경적올 울립니다!')
      }
   }
   class Truck extends Car { 
      constructor(name) {
         // 부모 클래스의 constructor, 즉 Car의 constructor를 호출한다. 
         super(name)
      }
      load() {
         console.log(’짐을 싣습니다•,)
      }
   }

   const my Car = new Car('자동차')

   myCar.honk() // 자동차 경적을 울립니다!

   const truck = new Truck('트럭')

   truck.honk() // 트럭 경적을 울립니다! 
   truck.load() // 짐을 싣습니다!

   - Car를 extends한 Truck이 생성한 객체에서도 honk 메서드를 사용할 수 있음
   - 이를 활용하면 기본 클래스를 기반으로 다양하게 파생된 클래스를 만들 수 있음

1.3.2 클래스와 함수의 관계
   - 클래스는 ES6에서 나온 개념으로 이전에는 프로토타입을 활용해 클래스의 작동 방식을 동일하게 구현하고 있었음
   - 반대로 말하면 클래스가 작동하는 방식은 자바스크립트의 프로토타입을 활용하는 것
   - 클래스는 객체지향 언어를 사용하던 다른 프로그래머가 좀 더 자바스크립트에 접근하기 쉽게 만들어주는 일종의 문법적 설탕(읽는 사람 또는 작성하는 사람이 편하게 디자인 된 문법) 역할을 한다고 볼수 있음.

1.3.3 정리
   - 과거 리액트의 많은 코드들이 클래스 컴포넌트로 생성됐으므로 클래스를 이해하고 나면 클래스 컴포넌트에 어떻게 생명주기를 구현할수 있는지, 왜 클래스 컴포넌트 생성을 위해 React.Component나 React.PureComponent를 상속하는지, 메서드가 화살표 함수와 일반 함수일 때 어떤 차이가 있는지 등을 이해할 수 있을 것.

1.4 클로저
   - 리액트 클래스 컴포넌트에 대한 이해가 자바스크립트의 클래스, 프로토타입, this에 달려있다면 함수 컴포넌트에 대한 이해는 클로저에 달려있다고 할수 있음
   - 함수 컴포넌트의 대부분의 기술이 클로어제 의존하고 있기때문에 함수컴포넌트를 이해하기 위해서는 클로저를 이해해야 하는게 필수

1.4.1 클로저의 정의
   - 클로저는 함수와 함수가 선언된 어휘적 환경의 조합
   - 어휘적 환경이란?

   function add() {
      const a = 10
      function innerAdd() {
         const b = 20
         console.log(a + b) 
      }
      innerAdd() // 30
   }

   add()

   - 함수가 위 처럼 중첩돼 있는 상황에서 변수의 범위가 어떻게 정의되있는지 알수있다.
   - a는 add 전체, b는 innerAdd 전체 a는 add 전체 이므로 innerAdd 안에서 사용이 가능함
   - 선언된 어휘적 환경이란것은 변수가 코드 내부에서 어디서 선언됐는지를 말하는 것.
   - 호출되는 방식에 따라 동적으로 결정되는 this와 다르게 코드가 작성된 순간에 정적으로 결정, 클로저는 이러한 어휘적 환경을 조합해 코딩하는 기법

1.4.2 변수의 유효 범위, 스코프
   - 변수의 유효 범위를 스코프라 칭함, 자바스크립트에는 다양한 스코프가 있음

전역 스코프
   - 전역 레벨에 선언하는 것을 전역 스코프
   - 전역(global)이라는 이름에서 알수 있듯 이 스코프에서 변수를 선언하면 어디서든 호출할 수 있음
   
   var global = 'global scope'
   function hello() {
      console.log(global) 
   }
   console.log(global) // global scope 
   hello() // global scope
   console.log(global. === window.global) // true

   - global이라는 변수를 var과 함께 선언했더니 전역 스코프와 hello 스코프 안에서 모두 사용가능

함수 스코프
   - 자바스크립트는 기본적으로 함수 레벨 스ㅜ코프를 따른다.
   - {} 블록이 스코프 범위를 결정하지 않음

   if (true) {
      var global = 'global scope'
   }
   console.log(global-) // 'global scope'
   console.log(gl.obal === window.global) // true

   - var global은 분명 {} 내부에서 선언돼 있는데, {} 밖에서도 접근이 가능한 것을 확인할 수 있다. 이는 자바스크립트는 함수 레벨 스코프를 가지고 있기 때문

   function hello() {
      var local = 'local variable'
      console.l.og(local) // local variable
   }

   hello()
   console.log(local) // Uncaught ReferenceError: local is not defined

   - 함수 레벨 스코프이므로 함수 내부에서만 가능하고 밖에서는 사용 불가

   var x = 10
   function foo() {
      var x = 100
      console.log(x) // 100
      function bar() {
         var x = 1000
         console.log(x) // 1000
      }
      bar()
   }

   console.log(x) // 10 
   foo()

   - 자바스크립트에서 스코프는 일단 가장 가까운 스코프에서 변수가 존재하는지를 먼저 확인한다
   - foo 안에 console.log는 var x = 100로 선언된 x를 호출
   - 그 다음 bar 안에 console.log는 var x = 1000로 선언된 x를 호출
   - 마지막 console.log는 foo 밖에 있는 var x = 10로 선언된 x를 호출

1.4.3 클로저의 활용
   - 전역 스코프는 어디서든 원하는 값을 꺼내올 수 있음
   - 이는 누구든 접근할 수 있고 수정할 수도 있다는 뜻

   var counter = 0
   function handleClick() { 
      counter++
   }

   - 위 예제에서 counter 변수의 문제점
   - 전역 레벨에 선언돼 있어서 누구나 수정 가능
   - 전역 레벨에 저장되면 누구든 접근이 가능하므로 리액트 애플리케이션을 쉽게 망가트릴 수 있음
   - 리액트가 관리하는 내부 상태 값은 리액트가 별도로 관리하는 클로저 내부에서만 접근할 수 있음.

   function Counter() {
      var counter = 0
      return {
         increase: function () { 
            return ++counter
         },
         decrease: function () {
            return --counter
         }, 
         counter: function () { 
            console.log('counter에 접근!') 
            return counter
         }, 
      } 
   }

   var c = Counter()
   console.log(c.increase()) // 1
   console.log(c.increase()) // 2
   console.log(c.increase()) // 3
   console.log(c.decrease()) // 2
   console.log(c.counter()) // 2

   - counter 변수를 직접적으로 노출하지 않으므로 사용자가 직접 수정하는 것을 막음, 접근하는 경우 제한해 로그를 남겨 부차적인 작업 수행가능
   - counter 변수의업데이트를 increase와 decrease로 제한해 무분별하게 변경되는것을 막음
   - 클로저를 활용해 전역 스코프의 사용을 막고 개발자가 원하는 정보만 개발자가 원하는 방향으로 노출시킬 수 있음

리액트에서의 클로저
   - 클로저의 원리를 사용하고 있는 대표적인것중 하나가 useState

   function Component() {
      const [state, setstate] = useState()

      function handleClick() {
         // useState 호출은 위에서 끝났지만,
         // setstate는 계속 내부의 최신값(prev)을 알고 있다.
         // 이는 클로저를 활용했기 때문에 가능하다. 
         setState((prev) => prev + 1)
      }
      // .... 
   }

   - useState 함수 호출은 첫줄에서 종료됐지만 클로저가 useState 내부에서 활용됐기 때문에 setState는 useState 내부의 최신값을 확인할 수 있음
   - 외부함수가 반환한 내부함수는 외부함수의 호출이 끝났음에도 자신이 선언된 외부 함수가 선언된 환경을 기억하기 때문에 계속해서 state 값을 사용할 수 있음

1.4.4 주의할 점
   - 클로저는 어렵고 다룹기 쉽지 않은 개념이므로 주의를 요함

   for (var i = 0; i < 5; i++) { 
      setTimeout(function () { 
         console.log(i)
      }, i * 1000) 
   }

   - 위 코드는 의도는 0부터 시작해 1초간격으로 0,1,2,3,4를 차례대로 출력하려는 것이지만 결과는 5만 출력된다.
   - i가 전역 변수로 작동하기 때문에 var는 for문과 상관없이 해당 구문이 선언된 함수 레벨 스코프를 따르고 있기때문에 함수 내부 실행이 아니라면 전역 스코프에 var i가 등록돼있음
   - for문 다 순회한 후 setTimeout을 실행하려할때 이미 i는 5로 업데이트가 완료돼있다.

   for (let i = 0; i < 5; i++) {
      setTimeout(function () { 
         console.log(i)
      }, i * 1000)
   }

   - 위 코드는 의도되로 작동됨, let은 블록 레벨 스코프를 가지게 되므로 let i 가 for문을 순회화면서 각각의 스코프를 갖게됨
   - setTimeout이 실행되는 시점에도 유효해서 각 콜백이 의도한 i값을 바라볼 수 있게된다.

   for (var i = 0; i < 5; i++) { 
      setTimeout(
         (function (sec) { 
            return function () { 
               console.log(sec) 
            }
         })(i), 
         i * 1000, 
      )
   }

   - 위 코드는 클로저를 활용한것, for문 내부에서 즉시 실행 익명 함수를 선언
   - 이 함수는 i를 인수로 받는데 sec라고 하는 인수에 저장해두었다가 setTimeout의 콜백함수에 넘기게 된다.
   - setTimeout의 콜백함수가 바라보는 클로저는 즉시 실행 익명함수가 되고 각 for문마다 생성되고 실행되기를 반복함
   - 각각의 함수는 고유한 스코프, 고유한 sec를 가지게 되서 의도되로 실행할 수 있게 됨

   - 클로저는 사용하는데 비용이 듬, 생성될 때마다 선언적 환경을기억해야하므로 추가 비용이 발생(메모리)
   - 클로저의 기본 원리에 따라 클로저가 선언된 순간 내부 함수는 외부 함수의 선언적인환경을 기억하고 있어야 하므로 어디에서 사용하는지 여부에 관계없이 저장해둠
   - 외부함수를 기억하고 이를 내부함수에서 가져다 쓰는 메커니즘은 성능에 영향을 미침
   - 꼭 필요한 작업만 남겨놔야 함, 아니면 메모리를 불필요하게 잡아먹는 결과를 야기할 수 있고 적절한 스코프로 가둬두지 않으면 성능에 악영향을 미침

1.4.5 정리
   - 클로저는 함수형 프로그래밍의 중요한 개념, 부수효과가 없고 순수해야 한다는 목적을 달성하기 위해 적극적으로 사용되는 개념
   - 공짜로 쓸 수 있는 것이 아니므로 항상 주의를 기울여야 한다.

1.5 이벤트루프와 비동기통신의 이해
   - 자바스크립트는 싱글스레드에서 작동함, 한번에 하나의 작업만 동기 방식으로 처리할 수 있음
   - 동기는 직렬방식으로 작업을 처리하는것, 이 요청이 시작된 이후에는 무조건 응답을 받은 이후에만 다른 작업을 처리 할 수 있음, 개발자에게는 매우 직관적으로 다가오지만 한번에 많은 작업을 처리할 수 없음
   - 비동기는 병렬 방식으로 작업을 처리하는것을 의미, 요청을 시작한 후 응답에 상관없이 다음 작업이 이루어지며 한번에 여러 작업이 실행될 수 있음

   - 자바스크립트는 싱글 스레드에서 동기 방식으로 작동하지만 많은 양의 비동기 작업이 이루어지고 있음
   - 리액트 또한 비동기식으로 작동하는 작업이 존재함

1.5.1 싱글 스레드 자바스크립트
   - 자바스크립트는 싱글 스레드 언어
   - 과거에는 프로그램을 실행하는 단위가 오직 프로세스 뿐이었음, 프로세스는 프로그램을 구동해 프로그램의 상태가 메모리상에서 실행되는 작업 단위를 의미함
   - 그러나 소포트웨어가 점점 복잡해지면서 하나의 프로그램에서 동시에 여러 개의 복잡한 작업을 수행할 필요성이 대두됨
   - 하나의 프로그램에서는 하나의 프로세스만 할당되므로 작업을 수행하기 어려워 그래서 탄생한 더 작은 실행 단위가 바로 스레드
   - 하나의 프로세스에서 여러개의 스레드를 만들 수 있고 스레드끼리 메모리를 공유할 수 있어서 여러 가지 작업을 동시에 수행 가능
   
   - 멀티스레드의 단점, 내부적으로 처리가 복잡함
   - 스레드는 하나의 프로세스에서 동시에 서로 같은 자원에 접근할 수 있는데, 동시에 여러 작업을 수행하는 경우 같은 자원에 대해 여러번 수정하는등 동시성 문제가 발생할 수 있음
   - 각각 격리돼있는 프로세스와 다르게 하나의 스레드에 문제가 생기면 공유하고있는 다른 스레드들도 문제가 생길 수 있음
   - 과거의 상황을 봤을때 자바스크립트를 멀티스레드로 구현하는것보다 싱글 스레드로 구현하는게 합리적인 결정으로 볼수있었음

   - 자바스크립트가 싱글 스레드라는 것은 자바스크립트 코드의 실행이 하나의 스레드에서 순차적으로 이루어지는것
   - 순차적으로 이루어진다는것은 코드를 한줄 한줄 실행하는것을 의미하며 하나의 작업이 끝나기 전까지 뒤이은 작업이 실행되지 않는것을 의미
   - 다른 프로그래밍 언어에서는 실행중인 함수를 임의로 멈추고 다른 스레드의 코드를 먼저 실행하는 기능이 있으나 자바스크립트는 해당 기능이 존재하지 않음
   
   - 자바스크립트에서 하나의 코드가 실행하는데 오래걸리면 뒤이은 코드가 실행되지않음, 이러한 특징을 Run-to-completion이라 함
   - 자바스크립트의 모든 코드는 ‘동기식’으로 한 번에 하나씩 순차적으로 처리됨
   - 비동기는 동기식과 다르게 요청한 즉시 결과가 주어지지 않을수 있고 응답이 언제올지 모름
   - 그러나 동기식과는 다르게 여러 작업을 동시에 수행할 수 있음

1.5.2 이벤트 루프란
   - 자바스크립트 런타임 외부에서 자바스크립트의 비동기 실행을 돕기 위해 만들어진 장치

호출 스택과 이벤트 루프
   - 호출 스택은 자바스크립트에서 수행해야 할 코드나 함수를 순차적으로 담아두는 스택

   function bar() { 
      console.log('bar')
   }
   function baz() { 
      console.log('baz')
   }
   function foo() {
      console.log('foo')
      bar() 
      baz()
   }
   foo()

   - foo를 호출하고 bar,baz를 순차적으로 호출하는 구조
   - 호출스택 순서 빈값 -> foo -> foo bar -> foo -> foo baz -> foo -> 빈값
   - 호출스택이 비어 있는지 여부를 확인하는것이 바로 이벤트 루프
   - 단순히 이벤트 루프만의 단일 스레드 내부에서 호출 스택 내부에 수행해야 할 작업이 있는지 확인하고 수행해야 할 코드가 있다면 자바스크립트 엔진을 이용해 실행
   - 단일스레드에서 일어나므로 두 작업은 동시에 일어날수 없고 한 스레드에서 순차적으로 실행됨

   function bar() {
      console.log('bar')
   }
   function baz() { 
      console.log('baz')
   }
   function foo() {
      console.log('foo')
      setTimeout(bar(), 0) // setTimeout만 추가했다. 
      baz()
   }
   foo()

   - setTimeout 으로 태스크 큐에 들어감
   - 태스크 큐란 실행해야 할 태스크의 집합, 이벤트 루프는 이러한 태스크큐를 한개이상 가지고있음
   - 자료구조의 큐가 아니도 set 형태를 띄고있다. 선택된 큐중에서 실행가능한 가장 오래된 태스크를 가져와야 하기때문
   - 자료구조인 큐는 FIFO로 무조건 앞에있는것을 가져와야 하지만 태스크 큐는 그렇지 않음. 태스크큐에서 실행해야할 태스크는 비동기함수의 콜백함수나 이벤트 핸들러 등을 의미
   - 이벤트 루프의 역할은 호출스택에 실행할 코드가 있는지 태스크 큐에 대기중인 함수가 있는지 반복해서 확인하는 역할을 함
   - 호출스택이 비었다면 태스크큐에 대기중인작업이 있는지 확인하고 이 작업을 실행 가능한 오래된 것부터 순차적으로 실행함. 이작업또한 태스크큐가 빌때까지 이루어짐
   - 이러한 작업들은 자바스크립트 코드가 동기식으로 실행되는 메인 스레드가 아닌 태스크 큐가 할당되는 별도의 스레드에서 수행됨
   - 자바스크립트 코드 실행은 싱글 스레드에서 이루어지지만 외부 Web Api등은 모두 자바스크릅티 코드 외부에서 실행되고 태스크 큐로 들어가는것

1.5.3 태스크 큐와 마이크로 태스크 큐
   - 이벤트 루프는 하나의 마이크로 태스크 큐를 가지고 있고 이는 기존의 태스크 큐와는 다른 태스크를 처리함
   - Promise가 대표적인 마이크로 태스크 큐
   - 마이크로 태스크 큐는 기존 태스크 큐보다 우선권을 가짐, setTimeout과 setInterval은 Promise보다 늦게 실행됨
   
   function foo() { 
      console.log('foo')
   }
   function bar() { 
      console.log('bar')
   }
   function baz() { 
      console.log('baz')
   }
   setTimeout(foo, 0)
   Promise.resolve().then(bar).then(baz)

   // bar baz foo

   - 렌더링은 태스크 큐를 실행하기에 앞서 마이크로 태스크 큐를 실행하고 마이크로 태스크 큐를 실행한 후 렌더링이 일어남. 각 마이크로 태스크 큐작업이 끝날때마다 한번씩 렌더링할 기회를 얻음

   - 마이크로 태스크 큐도 렌더링에 영향을 미칠수 있음. 특정 렌더링이 자바스크립트내 무거운 작업과 연관이 있다면 어떻게 분리해서 사용자에게 좋은 애플리케이션을 제공할지 고민해봐야함

1.5.4 정리
   - 자바스크립트 코드 자체가 싱글 스레드로 이루어져 비동기를 처리하기 어렵다
   - 하지만 태스크큐, 이벤트 루프, 마이크로 태스크큐 등 싱글스레드로 불가능한 비동기 이벤트 처리가 가능해진것

1.6 리액트에서 자주 사용하는 자바스크립트 문법
   - 리액트의 독특한 특징을 이해하려면 리액트에서 주로 사용하는 자바스크립트 문법을 이해해야 함
   - 서비스하는 브라우저가 어떤것인지 항상 염두에 두고 개발해야 한다, 브라우저 버전에 따라서 자바스크립트 문법에 제한이 생김
   - 이러한점을 해결하기 위해 나온것이 바벨, 자바스크립트의 최신 문법을 다양한 브라우저에서도 일관적으로 지원할 수 있도록 코드를 트랜스파일함

1.6.1 구조 분해할당
   - 구조 분해 할당이란 배열 또는 객체의 값을 말 그대로 분해해 개별 변수에 즉시 할당하는것

배열 구조 분해 할당
   - useState 함수는 2개짜리 배열을 반환하는 함수, 첫번째 값을 value 두번째 값을 setter로 사용 가능함
   - 배열로 반환하는 이유는 객체 구조 분해 할당은 원하는 이름으로 변경하기 힘들지만 배열 구조 분해 할당은 자유롭게 이름을 선언할 수 있음

   const array = [1, 2, 3, 4, 5]
   const [first, second, third, ...arrayRest] = array 
   // first 1 
   // second 2 
   // third 3
   // arrayRest [4, 5]

   - 배열 구조 분해 할당은 ,의 위치에 따라 값이 결정됨

   const array = [1, 2, 3, 4, 5]
   const [first, , , , fifth] = array // 2, 3, 4는 아무런 표현식이 없으므로 변수 할당이 생략돼 있다.
   first // 1 
   fifth // 5

   - 위와같은 방법은 실수를 유발할 수 있으므로 배열의 길이가 작을때 주로 쓰임
   
   - 배열분해할당에는 기본값을 선언할 수도 있음

   const array = [1, 2]
   const [a = 10, b = 10, c = 10] = array 
   // a 1 
   // b 2 
   // c 10

   - undefined일 때만 기본값을 사용

   const [a = 1, b = 1, c = 1, d = 1, e = 1] = [undefined, null, 0, ''] 
   a // 1
   b // null
   c // 0 
   d // "
   e // 1

   - 위 배열 구조 분해 할당에서는 기본값을 사용하는것이 a와 e뿐,자바스크립트에서 기본값을 사용할 수 있는경우는 undefined일때뿐
   
   - 특정값 이후의 값을 다시 배열로 선언하고 싶다면 전개 연산자(...)로 사용할 수도 있음
   
   const array = [1, 2, 3, 4, 5]
   const [first, ...rest] = array
   // first 1
   // rest [2, 3, 4, 5]

   - 뒤에 ... 을 사용하면 나머지 모든 값을 해당 변수에 배열로 넣게됨

   // 트랜스파일하기 전
   const array = [1, 2, 3, 4, 5]
   const [first, second, third, ...arrayRest] = array
   // 트랜스파일된 결과
   var array = [1, 2, 3, 4, 5]
   var first = array[0],
   second = array[l],
   third = array[2],
   arrayRest = array.slice(3)

   - 배열구조분해할당은 선언을 4줄에 걸쳐서 했지만 구조분해할당은 단 한줄로 위와같이 처리할수 있게됨

객체 구조 분해 할당
   - 객체에서 값을 꺼내온 뒤 할당하는것
   - 배열구조분해할당과는 객체 내부 이름으로 꺼내온다는 차이가 있음
   - 새로운 이름으로 다시 할당하는것도 가능하고 배열과 마찬가지로 기본값을 주는것도 가능함

   const object = { 
      a: 1,
      b: 1,
      const { a = 10, b = 10, c = 10 } = object
      // a 1
      // b 1 
      // c 10
   }

   - 위와같은 방식은 리액트 컴포넌트인 props에서 값을 바로 꺼내올때 자주쓰는방식으로 이해하고 있어야함

   function SampleComponent({ a, b }) {
      return a + b
   }
   SampleComponent({ a: 3, b: 5 }) // 8

   - 단순히 값으로 꺼내오는 것뿐만 아니라 변수에 있는 값으로 꺼내오는 이른바 계산된 속성 이름 방식도 가능함

   - 배열구조분해할당과 마찬가지로 전개연산자 ... 을 사용하면 나머지 값을 모두 가져올 수 있음, 똑같이 순서가 중요

   // 트랜스파일하기 전 
   const object = {
      a: 1,
      b: 1,
      c： 1,
      d: 1,
      e： 1,
   }
   const { a, b, ...rest } = object

   // 트랜스파일된 결과
   function _objectWithoutProperties(source, excluded) {
      if (source == null) return {}
      var target = _objectWithoutPropertiesLoose(source, excluded)
      var key, i
      if (Object.getOwnPropertySymbols) {
         var sourceSymbolKeys = Object.getOwnPropertySymbols(source)
         for (i = 0; i < sourceSymbolKeys.length; i++) {
            key = sourceSymbolKeys[i]
            if (excluded.indexOf(key) >= 0) continue
            if (!Object.prototype.propertylsEnumerable.call(source, key)) continue 
            target[key] = source[key]
         }
      }
      return target
   }
   function _objectWithoutPropertiesLoose(source, excluded) {
      if (source == null) return {}
      var target = {}
      var sourceKeys 그 Object.keys(source)
      var key, i
      for (i = 0; i < sourceKeys.length; i++) {
         key = sourceKeys[i]
         if (excluded.indexOf(key) >= 0) continue
         target[key] = source[key]
      }
      return target
   }
   var object = {
      a: 1,
      b: 1, 
      c： 1, 
      d: 1, 
      e: 1,
   }
   var a = object.a,
   b = object.b,
   rest = _objectWithoutProperties(object, ['a', 'b'])

   - 배열과 다르게 객체의 경우 구조 분해할당을 트랜스파일할 경우 조금더 복잡함

1.6.2 전개 구문
   - 전개구문은 앞서 소개한 구조 분해 할당과는 다르게 배열이나 객체,문자열과 같이 순회할 수 있는 값에 대해 말 그대로 전개해 간결하게 사용할 수 있는 구문.

배열의 전개 구문
   - 과거에는 배열 간에 합성하려면 메서드를 사용해야 했으나 전개구문을 활용하면 쉽게 배열을 합성할 수 있음.

   const arr1   = ['a', 'b']
   const arr2 = [...arr1, 'c', ’d’, 'e'] // [’a’, ’b’, ’c’, 'd', 'e']

   - 배열 내부에서 ... 배열을 사용하면 해당 배열을 마치 전개하는 것처럼 선언하고, 이를 내부 배열에서 활용함, 이러한 특징을 활용하면 기존 배열에 영향을 미치지 않고 배열을 복사하는 것도 가능하다.
   
   const arr1 = ['a', 'b']
   const arr2 = arr1
   arr1 = arr2 // true. 내용이 아닌 참조를 복사하기 때문에 true가 반환된다.

   const arr1 = ['a', 'b']
   const arr2 = [...arr1]
   arr1 == arr2 // false. 실제로 값만 복사됐을 뿐, 참조는 다르므로 false가 반환된다.

객체의 전개 구문
   - 배열과 비슷하게 사용 가능, 객체를 새로 만들때 사용할 수 있고 객체를 합성하는데 편리함
   const objl = {
      a: 1,
      b: 2,
   }
   const obj2 = { 
      c: 3, 
      d: 4,
   }
   const newObj = { ...objl, ...obj2 }
   // { "a": 1, "b"： 2, "c": 3, "d"： 4 }

   - 객체 전개 구문에 있어서 순서가 중요함, 작동 순서 차이로 다른 객체가 생성될 수 있음

   const obj = {
      a: 1,
      b: 1, 
      c: 1,
      d: 1, 
      e: 1,
   }
   // {a： 1, b: 1, c: 10, d: 1, e: 1} 
   const aObj = {
      ...obj,
      c: 10,
   }
   // {c: 1, a： 1, b： 1, d: 1, e: 1} 
   const bObj = {
      c: 10,
      ...obj,
   }

   - 결과값이 다른데 전개 구문 이후 값 할당이 있다면 할당한 값이 이전에 전개했던 구문값을 덮어씀
   - 반대의 경우에는 오히려 전개 구문이 해당값을 덮어씀, 갚을 덮어쓸것인지 받아들일지 순서에 차이가 발생하므로 주의해야함
   - 객체구조분해할당과 마찬가지로 객체전개연산자 또한 트랜스파일되면 상대적으로 번들리이 커지므로 사용할때 주의해야함

1.6.3 객체 초기자
   - 객체를 선언할때 객체에 넣고자 하는키와 값을 가지고있는 변수가 이미 존재한다면 해당 값을 간결하게 넣어줄 수 있는 방식식
   - 객체를 좀더 간편하게 선언할 수 있음

1.6.4 Array 프로토타입의 메서드 map,filter,reduce,forEach
   - 배열과 관련된 메서드
   - 기존 배열의 값을 건들지 않고 새로운 값을 만들어내므로 기존값이변경될 염려 없이 사용 가능

map
   - 인수로 전달받은 배열과 똑같은 길이의 새로운 배열을 반환하는 메서드

   const arr = [1, 2, 3, 4, 5]
   const doubledArr = arr.map((item) => item * 2) 
   // [2, 4, 6, 8, 10]
   리액트에서는 주로 특정 배열을 기반으로 어떠한 리액트 요소를 반환하고자 할 때 많이 사용한다.
   const arr = [1, 2, 3, 4, 5]
   const Elements = arr.map((item) => {
   return <Fragment key={item}>{item}</Fragment> 
   })

filter
   - 콜백 함수를 인수로 받는데 콜백 함수에서 truthy 조건을 만족하는경우에만 해당 원소를 반환하는 메서드
   - 말 그대로 필터링 하는 역할의 메서드
   - 주로 기존 배열에 대해 어떠한 조건을 만족하는 새로운 배열을 반환할때 사용

   const arr = [1, 2, 3, 4, 5]
   const evenArr = arr.filter((item) => item % 2 === 0)
   // [2, 4]

reduce
   - 콜백 함수와 함께 초기값을 추가로 인수를 받는데 초기값에 따라 배열이나 객체 또는 다른 무언가로 반환할 수 있는 메서드
   - reducer 콜백 함수를 실행하고 이를 초기값에 누적해 결과를 반환

   const arr = [1, 2, 3, 4, 5]
   const sum = arr.reduce((result, item) => { 
      return result + item
   }, 0) 
   // 15

   - 0은 초기값, reducer 첫번째인수는 선언한 초기값의 현재값 두번째 인수는 현재 배열의 아이템
   - 콜백의 반환값을 계속해서 초기값에 누적하면서 새로운 값을 만듬
   - filter와 map의 작동을 reduce 하나로도 구현할 수 있음

forEach
   - 반환값이 없음, 콜백함수를 실행할뿐 map과 같이 결과를 반환하는 작업은 수행하지않음
   - 실행되는  순간 에러를 던지거나 프로세를 종료하지 않는이상 멈출수 없다.

   function run() {
      const arr = [1, 2, 3]
      arr.forEach((item) => {
         console.log(item)
         if (item === 1) { 
            console.log('finished!') 
            return
         }
      })
   }
   // 이 함수를 실행하면 다음과 같은 결과를 볼 수 있다. 
   run()
   // 1 
   // finished!
   // 2 
   // 3

   - 중간에 return 존재해 함수 실행이 끝나도 계속 콜백이 실행됨
   - 사용할때 절대로 중간에 순회를 멈출수 없다는 사실을 인지하고 사용해야 함

1.6.5 삼항 조건 연산자
   - 3개의 피연산자를 취할 수 있는 문법

   const value = 10
   const result = value % 2 === 0 ? '짝수' : '홀수’ 
   // 짝수

   -먼저 맨 앞에 true/false를 판별할 수 있는 조건문이 들어가고 그 이후에 물음표가 들어간다. 물음표 뒤에는 참일 경우 반환할 값, : 뒤에는 거짓일 때 반환할 값을 지정한다.

1.6.6 정리
   - 리액트 애플리케이션에서 주로 사용하는 주요 문법과 메서드에 대해서만 알아봄

1.7 선택이 아닌 필수, 타입스크립트
   - 최근 개발자들은 타입스크립트를 많이 도입하고 있고 동적 언어인 자바스크립트에서 런타임에만 타입을 체크할수 있는 한계를 극복해 코드를 더욱 안전하게 작성하고 잠재적인 버그를줄일수 있음

1.7.1 타입스크립트란?
   - 기존 자바스크립트 문법에 타입을 가미한 것
   - 기존 자바스크립트에서는 모든 함수와 변수에 타입을 체크하고 사용하는것은매우 번거로운 작업
   - 타입스크립트는 이러한 자바스크립트의 한계를 벗어나 타입체크를 정적으로 런타임이 아닌 빌드타임에 수행할수 있게 해줌
   function test(a: number, b: n니mber) { 
      return a / b
   }
   // t유로 이 코드를 자바스크립트로 트랜스파일하면 다음과 같은 에러가 난다.
   // Argument of type 'string' is not assignable to parameter of type 'number'.
   // 이 코드는 타입 문제가 해결되기 전까지 쓸 수 없다.
   test(’안녕하세요,, '하이’)

   - 변수에 타입을 설정할수 있으므로 a와 b변수에 number라는 타입을 지정하면 오직 number만 할당할 수 있게됨
   - 다만 자바스크립트에서 불가능한 일은 타입스크립트에서도 여전히 불가능함

1.7.2 리액트 코드를 효과적으로 작성하기 위한 타입스크립트 활용법

any 대신 unknown을 사용하자
   - any는 정말 불가피할때만 사용해야하는 타입으로 왠만하면 사용X, 이를 사용하는것은 타입스크립트가 제공하는 정적 타이핑의 이점을 모두 버리는것과 같다

   function doSomething(callback: any) { 
      callback()
   }
   // 타입스크립트에서 에러가 발생하지 않는다. 그러나 이 코드는 실행 시 에러가 발생한다. 
   doSomething(l)

   - any는 정말로 예외적인 경우가 아닌 이상 사용하는것이 좋으므로 타입을 단정할 수 없는경우에는 unknown을 사용하는것이 좋다
   - unknown은 모든 값을 할당할 수있고 어떠한 값도 할당할수있음, 그러나 any와 다르게 바로 사용할수없다

   function doSomething(callback: unknown) {
      if (typeof callback === 'function') {
         callback()
         return
      }
      throw new Error('callback은 함수여야 합니다.')
   }

   - unknown과 반대로 어떠한 타입도 들어올수 없는 never도 있음
   - 코드상으로 존재가 불가능한 타입을 나타낼 때 never가 사용된다.
   - 타입스크립트로 클래스 컴포넌트를 선언할 때 props는 없지만 state가 존재하는 상황에서 이 빈 props, 정확히는 어떠한 props도 받아들이지 않는다는 뜻으로 사용이 가능

타입가드 적극적으로 활용
   - 타입을 사용하는 쪽에서 최대한 타입을 좁히는것이 좋은데 도움을 주는것이 바로 타입가드

   instanceof
   - 지정한 인스턴스가 특정 클래스의 인스턴스인지 확인할 수있는 연산자

   typeof
   - 특정 요소에 대해 자료형을 확인하는데 사용

   in
   - 주로 어떤 객체에 키가 존재하는지 확인하는 용도로 사용됨
   - 타입에 여러가지 객체가 존재할 수 있는 경우 유용함

제네릭
   - 함수나 클래스 내부에서 단일 타입이 아닌 다양한 타입에 대응할 수 있도록 도와주는 도구
   - 타입만 다른 비슷한 작업을 하는 컴포넌트를 단일 제네릭 컴포넌트로 선언해 간결하게 작성할 수 있음

   function getFirstAndl_ast<T>(list: T[]): [T, T] { 
      return [list[0], list[list.length - 1]]
   }

   const [first, last] = getFirstAndl_ast([l, 2, 3, 4, 5])
   first // number 
   last // number

   const [first, last] = getFirstAndLast(['a', 'b', 'c', 'd', 'e'])
   first // string 
   last //string

   - useState에 제네릭으로 타입을 선언하면 state 사용과 기본값 선언을 좀 더 명확하게 할수있음
   - 제네릭을 하나 이상 사용할수도 있으나 일반적으로 T,U 등으로 표현하는데 이 경우 의미하는바를 모를수 있으니 네이밍하는것이 좋음

인덱스 시그니처
   - 객체의 키를 정의하는 방식

   type Hello = {
      [key: string]: string 
   }
   const hello: Hello = { 
      hello: 'hello', 
      hi: 'hi',
   }
   hello['hi'] // hi
   hello['안녕'] // undefined

   - [key: string] 이 부분이 인덱스 시그니처, 키에 원하는 타입을 부여할수있음
   - 동적인 객체를 정의할때 유용하나 키의 범위가 string으로 너무 커지기 때문에 존재하지 않는 키로 접근하면 undefined 반환
   - 동적으로 선언되는경우를 최대한 지양하고 객체의 타입도 필요에 따라 좁혀야 한다.

   - 타입스크립트의 핵심 원칙은 타입 체크를 할 때 그 값이 가진 형태에 집중하는것. 이러한 것을 덕타이핑 또는 구조적 타이핑이라고 함
   - 자바스크립트는 객체의 타입에 구애받지 않고 객체의 타입에 열려있으므로 타입스크립트도 이러한 자바스크립트의 특징을 맞춰야함
   - 모든 키가 들어올수 있는 가능성이 열려있는 객체의 키에 포괄적으로 대응하기 위해 string[] 타입을 제공

1.7.3 타입스크립트 전환가이드
   - 단 한번에 타입스크립트로 넘어가긴 힘드므로 점진적으로 타입스크립트로 전환

   tsconfig.json 작성하기
   - 타입스크립트를 먼저 작성할수있는 환경을 만들기

   JSDoc과 @ts-check를 활용해 점진적으로 전환하기
   - 타입스크립트로 전환하지 않고 타입을 체크

   타입 기반 라이브러리 사용을 위해 ©types 모듈 설치하기

   파일 단위로 조금씩 전환하기

1.7.4 정리
   - 타입스크립트의 중요성은 갈수록 커지고 있으나 어디까지나 슈퍼셋 언어로 타입스크립트는 자바스크립트기반으로 작동함
   - 자바스크립트를 이해하지 못하고 타입스크립트를 사용하는건 어불성설이므로 자바스크립트를 충분히 이해하고 그 뒤에 타입스크립트를 학습에 적용해보자

2장 리액트 핵심요소 깊게 살펴보기 143 ~ 215

2.1 JSX란
   - JSX는 개발자들이 알고 있는 XML과 유사한 내장형 구문, 리액트에 종속적이지 않은 독자적인 문법
   - JSX는 자바스크립트 표준 코드가 아닌 페이스북이 임의로 만든 새로운 문법이라 반드시 트랜스파일러를 거쳐야 자바스크립트 런타임이 이해할 수있는 자바스크립트 코드로 변환된다.
   - JSX의 목표는 JSX내부에 트리 구조로 표현하고 싶은 다양한 것들을 작성해두고 트랜스파일과정을 거쳐 자바스크립트가 이해할 수 있는 코드로 변경하는것이 목표
   - JSX는 자바스크립트 내부에서 표현하기 까다로웠던 XML 스타일의 트리 구문을 작성하는 데 많은 도움을 주는 새로운 문법

2.1.1 JSX의 정의

JSXElement
   - JSX를 구성하는 가장 기본 요소, HTML의 요소와 비슷한 역할을 함

   JSXOpeningElement: 일반적으로 볼 수 있는 요소다. 

   JSXClosingElement： JSXOpeningElement가 종료됐음을 알리는 요소로로, 반드시 JSXOpeningElement와 쌍으로 사용 돼야 한다.

   JSXSelfClosingElement: 요소가 시작되고, 스스로로 종료되는 형태를 의미한다. <script/>와 동일한 모습을 띠고 있다. 이는 내부적으로 자식을 포함할 수 없는 형태를 의미한다.

   JSXFragment: 아무런 요소가 없는 형태로, JSXSelfClosingElement 형태를 띨 수는 없다. </>는 불가능하다. 단 <></> 는 가능하다.

- JSXElementName: JSXElement의 요소 이름으로 쓸수 있는것

   JSXIdentifier： JSX 내부에서 사용할 수 있는 식별자를 의미한다. 

   JSXNamespacedName： JSXIdentifier: JSXIdentifier의 조합, 즉 :을 통해 서로 다른 식별자를 이어주는 것도 하나의 식별자로 취급된다. 두 개 이상은 올바른 식별자로 취급되지 않는다.

   JSXMemberExpression： JSXIdentifier.JSXIdentifier의 조합, 즉 .을 통해 서로 다른 식별자를 이어주는 것도 하나의 식별자로 취급된다..을 여러 개 이어서 하는 것도 가능하다. 단 JSXNamespacedName과 이어서 사용하는 것은 불가능하다.

JSXAttributes
   - JSXElement에 부여할 수 있는 속성, 단순히 속성을 의미하므로 필수값이 아님

   JSXSpreadAttributes： 자바스크립트의 전개 연산자와 동일한 역할을 한다고 볼 수 있다.

   JSXAttribute： 속성을 나타내는 키와 값으로 짝을 이루어서 표현한다. 키는 JSXAttributeName, 값은 JSXAttributeValue 로 불린다
       JSXAttributeName：속성의 키 값 키로는 앞서 JSXElementName에서 언급했던 JSXIdentifier와 JSXNamespacedName이 가능하다. 여기서도 마찬가지로 :을 이용해 키를 나타낼 수 있다
       JSXAttributeValue：속성의 키에 할당할 수 있는 값, 아래중 하나 만족해야함
         - "큰따옴표로 구성된 문자열": 자바스크립트의 문자열과 동일하다. 안에 아무런 내용이 없어도 상관없다.
         - ’작은따옴표로 구성된 문자열,:자바스크립트의문자열과 동일하다. 안에 아무런 내용이 없어도 상관없다.
         - { AssignmentExpression }: 자바스크립트의 AssignmentExpression을 의미한다. AssignmentExpression은 자바스크립트에서 값을 할당할 때 쓰는 표현식을 말한다. 즉, 자바스크립트에서 변수에 값을 넣을 수 있는 표현식은 JSX 속성의 값으로도 가능하다.
         - JSXElement: 값으로 다른 JSX 요소가 들어갈 수 있다. 리액트에서 자주 볼 수 없는 코드지만 다음과 같은 형태도 가능하다
   
JSXChildren
   - JSXElement의 자식 값을 나타냄, JSX는 속성을 가진 트리구조를 나타내기 위해 만들어졌기 때문에 부모와 자식 관계를 나타낼수있고 자식이 JSXChildren

   JSXChild: JSXChildren을 이루는 기본 단위다. 단어의 차이에서 알 수 있듯이 JSXChildren은 JSXChild를 0개 이상 가질 수 있다. JSXChildren은 JSXChild가 없어도 상관없다.
   JSXText: {, <, >, }을 제외한 문자열. 이는 다른 JSX 문법과 혼동을 줄 수 있기 때문이다.
   JSXElement: 값으로 다른 JSX 요소가 들어갈 수 있다.
   JSXFragment: 값으로 빈 JSX 요소인 <></>가 들어갈 수 있다.
   { JSXChildExpression (optional) }: 이 JSXChildExpression은 자바스크립트의 AssignmentExpression을 의미한다.

JSXStrings
   - HTML에서 사용 가능한 문자열은 모두 JSXStrings에서도 가능
   - 정의된 문자열이라 함은, "큰따옴 표로 구성된 문자열", '작은따옴표로 구성된 문자열' 혹은 JSXText를 의미
   - 자바스크립트와는 한 가지 중요한 차이점이 발생하는데 바로 \로 시작하는 이스케이프 문자 형태. 
   - \는 자바스크립트에서 특수문자를 처리할 때 사용되므로 몇 가지 제약 사항(\를 표현하기 위해서는 \\로 이스케이프해야 함)이 있지만 HTML에서는 아무런 제약 없이 사용할 수 있다.

2.1.2 JSX예제
   // 하나의 요소로 구성된 가장 단순한 형태
   const ComponentA = <A>안녕하세요.</A>

   // 자식이 없이 SelfClosingTag로 닫혀 있는 형태도 가능하다.
   const ComponentB = <A />

   // 옵션을 { }와 전개 연산자로 넣을 수 있다.
   const ComponentC = <A {...{ required: true }} />

   // 속성만 넣어도 가능하다.
   const ComponentD = <A required />

   // 속성과 속성값을 넣을 수 있다.
   const ComponentE = <A required={false} />

   const ComponentF = (
      <A>
      {/* 문자열은 큰따옴표 및 작은따옴표 모두 가능하다. */}
      < B text="리액트" />
      </A>
   )

   const ComponentG = (
      <A>
      {/* 옵션의 값으로 JSXElement를 넣는 것 또한 올바른 문법이다. */}
      < B optionalChildren={<>안녕하세요.</>} />
      </A>
   )

   const ComponentH = (
      <A>
      {/* 여러 개의 자식도 포함할 수 있다. */}
      안녕하세요
      < B text="리액트" />
      </A>
   )

2.1.3 JSX는 어떻게 자바스크립트에서 변환될까

   - JSX 코드
   const ComponentA = <A required={true}>Hello World</A>
   const ComponentB = <>Hello World</>
   const ComponentC = (
      <div>
      <span>hello world</span>
      </div> 
   )

   - 변환결과
   'use strict’
   var ComponentA = React.createElement(
      A,
      { 
         required: true,
      },
      'Hello World1, 
   )
   var ComponentB = React.createElement(React.Fragment, null, 'Hello World')
   var ComponentC = React.createElement(
      'div',
      null.
      React.createElement('span', null, 'hello world'), 
   )

2.1.4 정리
   - JSX는 자바스크립트 코드 내부에 HTML과 같은 트리구조를 가진 컴포넌트를 표현할수 있다

2.2 가상 DOM과 리액트 파이버
   - 리액트의 특징중하나는 실제DOM이 아닌 가상DOM을 운영하는것

2.2.1 DOM과 브라우저 렌더링 과정
   - DOM은 웹페이지에 대한 인터페이스로 브라우저가 웸페이지의 콘텐츠와 구조를 어떻게 보여줄지에 대한 정보를 담고있음

2.2.2 가상DOM의 탄생과 배경
   - 브라우저가 웹페이지를 렌더링하는 과정은 매우 복잡하고 많은 비용이 듬
   - 렌더링 이후 추가 렌더링 작업은 싱글 페이지 애플리케이션에서 더욱 많아짐
   - 사용자는 자연스러운 웹페이지 탐색을 할 수 있으나 DOM을 관리하는 과정에서는 부담해야할 비용이 커짐
   - 이러한 문제들을 해결하기 위해 나온것이 가상DOM
   - 실제 브라우저DOM이 아닌 리액트가 관리하는 가상 DOM, 웹페이지가 표시해야 할 DOM을 일단 메모리에 저장하고 리액트가 실제 변경에 대한 준비가 완료됐을때 실제 브라우저 DOM에 반영
   - 이러한 계산을 브라우저가 아닌 메모리에서 계산하면 여러번 발생했을 렌더링 과정을 최소화 할 수 있고 브라우저와 개발자의 부담을 덜수있음.
   - 가상DOM이 DOM을 관리하는 브라우저보다 빠르다는 얘기가있는데 이는 사실이 아님. 무조건 빠른것이 아니라 대부분의 상황에서 웬만한 애플리케이션을 만들수 있을정도로 충분히빠른것

2.2.3 가상 DOM을 위한 아키텍처, 리액트 파이버
   - 가상DOM과 렌더링과정 최적화를 가능하게 해주는것이 바로 리액트파이버

리액트파이버
   - 리액트에서 관리하는 자바스크립트 객체,파이버는 파이버재조정자가 관리함 가상DOM과 실제DOM을 비교해 변경사항을 수집하며 둘사이에 차이가 있으면 변경에 관련된 정보를 가지고 있는 파이버를 기준으로 화면에 렌더링을 요청
   - 리액트 파이버의 목표는 리액트 웹 애플리케이션에서 발생하는 애니메이션,레이아웃,사용자인터렉션에 올바른 결과물을 만드는 반응성 문제를 해결하는것

   파이버가 할 수 있는 일
   - 작업을 작은 단위로 분할하고 쪼갠 다음, 우선순위를 매긴다.
   - 이러한 작업을 일시 중지하고 나중에 다시 시작할 수 있다.
   - 이전에 했던 작업을 다시 재人요하거나 필요하지 않은 경우에는 폐기할 수 있다
   - 이러한 과정들이 모두 비동기로 일어남

   - 파이버는 리액트 요소와 유사하다 느낄수 있으나 리액트 요소는 렌더링이 발생 될때마다 새롭게 생성되지만 파이버는 가급적이면 재사용됨
   - state가 변경되거나 생명주기 메서드가 실행되거나 DOM의 변경이 필요한 시점등에 실행됨
   - 리액트가 파이버를 처리할 때마다 직접 바로 처리하기도 하고 스케줄링하기도함. 이러한 작업들은 유연하게 처리됨
   - 변수에 UI관련 값을 보관하고 리액트의 자바스크립트 코드 흐름에 따라 이를 관리하고 표현하는것이바로 리액트

리액트 파이버트리
   - 파이버트리는 두개가 존재 , 현재 모습을 담은 파이버트리 ,작업중인 상태를 나타내는 workInProgress 트리
   - 리액트 파이버 작업이 끝나면 포인터만 변경해 workInProgress 트리를 현재 트리로 바꿔버림 -> 이를 더블 버퍼링이라고 함
   - 그래픽을 통해 화면에 표시되는것을 그리려면 내부적으로 처리를 거쳐야 하는데 이러한 경우 사용자에게 다 그리지 못한 모습을 보일수 있음 그래서 이러한 상황을 방지하기 위해 보이지 않는 곳에서 그다음으로 그려야할 그림을 그린다음 완성되면 현재상태를 새로운 그림으로 바꾸는 기법
   - 리액트에서도 더블 버퍼링 기법을쓰고 커밋단계에서 수행됨

2.2.4 파이버와 가상 DOM
   - 리액트 컴포넌트에 대한 정보를 1:1로 가지고 있는것이파이버, 리액트 아키텍처 내부에서 비동기로 이뤄짐
   - 실제 브라우저 구조인 DOM에 반영하는것은 동기적으로 일어나야하고 처리하는작업이많아화면에 불완전하게 표시될수 있으므로 작업을 가상에서 즉 메모리에서 먼저 수행해서 최종 결과물만 실제 브라우저 DOM에 적용하는것

2.2.5 정리
   - 가상 DOM과 리액트의 핵심은 브라우저의 DOM을 더욱 빠르게 그리고 반영하는 것이 아니라 바로 값으로 UI를 표현하는 것. 
   - 화면에 표시되는 이를 자바스크립트의 문자열, 배열 등과 마찬가지로 값으로 관리하고 이러한 흐름을 효율적으로 관리하기 위한 메커니즘이 바로 리액트의 핵심

2.3 클래스 컴포넌트와 함수 컴포넌트

2.3.1 클래스 컴포넌트
   - 클래스 컴포넌트 구조

   import React from 'react'
   class Samplecomponent extends React.Component {
      render() {
         return <h2>Sample Component</h2> 
      } 
   }

   - 클래스 컴포넌트를 만드려면 클래스를 선언하고 extends로 만들고 싶은 컴포넌트를 extends해야함

   - 클래스 컴포넌트 예제

   import React from 'react'

   // props 타입을 선언한다.
   interface SampleProps { 
      required?: boolean 
      text: string
   }

   // state 타입을 선언한다.
   interface Samplestate { 
      count: number 
      isLimited?: boolean 
   }

   // Component에 제네릭으로 props, state를 순서대로 넣어준다.
   class Samplecomponent extends React.ComponentxSampleProps, SampleStato {
      // constructor에서 props를 넘겨주고, state의 기본값을 설정한다.
      private constructor(props: SampleProps) { 
         super(props) 
         this.state = { 
            count: 0, 
            isLimited: false,
         }
      }
      
      // render 내부에서 쓰일 함수를 선언한다.
      private handleClick =()=>{
         const newValue = this.state.count + 1
         this.setState({ count: newValue, isLimited: newValue >= 10 }) 
      }

      // render에서 이 컴포넌트가 렌더링할 내용을 정의한다.
      public render() {
         // props와 state 값을 this, 즉 해당 클래스에서 꺼낸다.
         const {
            props: { required, text },
            state: { count, isLimited },
         } = this
         return (
            <h2>
            Sample Component
            <div>{required ? ’필수’ : ’필수아님’}</div>
            <div>문자: {text}</div>
            <div>count: {count}</div>
            <button onClick={this.handleClick} disabled={isLimited}> 
            증가
            </button>
            </h2>
         )
      }
   }

   constructor 
   - 컴포넌트 내부에 위 생성자함수가 있다면 컴포넌트가 초기화 되는 시점에 호출됨
   - 컴포넌트의 state를 초기화 할 수 있음
   - 선언돼있는 super는 컴포넌트를 만들면서 상속받은 상위 컴포넌트, React.Component의 생성자 함수를 먼저 호출해 필요한 상위 컴포넌트에 접근할수 있게함

   props
   - 컴포넌트에 특정 속성을 전달하는 용도로 쓰임

   state
   - 클래스 컴포넌트 내부에서 관리하는 값을 의미, 이 값은 항상 객체여야 함, 값에 변화가 있을때 마다 리렌더링이 발생한다

   메서드
   - 렌더링 함수 내부에서 사용되는 함수, 보통 DOM에서 발생하는 이벤트와 함께 사용됨

클래스 컴포넌트의 생명주기 메서드
   - 생명주기 메서드가 실행되는 시점
      - 마운트
         - 컴포넌트가 마운팅(생성)되는 시점
      - 업데이트
         - 이미 생성된 컴포넌트의 내용이 변경(업데이트)되는 시점
      - 언마운트
         - 컴포넌트가 더이상 존재하지 않는 시점

   render()
      - 리액트 클래스 컴포넌트의 유일한 필수 값, UI를 렌더링 하기위해 쓰임 마운트와 업데이트과정에서 일어남
      - 항상 순수해야 하고 부수 효과가 없어야 한다

   componentDidMount()
      - 클래스 컴포넌트가 마운트 되고 그다음으로 호출되는 생명주기 메서드
      - render와는 다르게 setState로 state값 변경 가능

   componentDidUpdate()
      - 컴포넌트 업데이트가 일어난 후 바로 실행
      - state나 props의 변화에 따라 DOM을 업데이트 하는데 쓰임, setState 사용 가능하다

   componentWillUnmount()
      - 컴포넌트가 언마운트 되거나 더이상 사용되지 않을때 호출됨
      - 메모리 누수나 불필요한 작동을 막기 위한 클린업 함수를 호출하기 위한 최적의 위치

   shouldComponentUpdate()
      - state나 props의 변경으로 리액트 컴포넌트가 다시 리렌더링 되는것을 막기 위해 사용하는 생명주기 메서드
      - 이 메서드를 활용하면 컴포넌트에 영향을 받지않는 변화에 대해 정의할수 있음

   static getDerivedStateFromProps()
      - render()를 호출하기 직전에 호출됨
      - static으로 선언되어 this에 접근할 수 없다. 반환하는 객체는 내용이 모두 state로 들어가게 됨, null을 반환하면 아무일도 일어나지 않음
      - 이 메서드도 render 실행시에 호출됨

   getSnapShotBeforeUpdate()
      - DOM이 업데이트 되기 직전에 호출됨
      - 반환되는값은 componentDidUpdate()로 전달됨.
      - DOM에 렌더링 되기 전 윈도우 크기를 조절하거나 스크롤 위치를 조정하는 등의 작업을 처리하는데 유용

   getDerivedStateFromError()
      - 에러상황에서 실행되는 메서드
      - getDerivedStateFromError(), componentDidCatch() , getSnapShotBeforeUpdate() 이 메서드들은 리액트 훅으로 구현돼 있지 않기때문에 반드시 클래스 컴포넌트를 사용해야 함
      - 자식 컴포넌트에서 에러가 발생했을때 호출되는 에러메서드
      - 반드시 state 값을 반환해야하는데 그 이유는 실행 시점 때문

   componentDidCatch()
      - 자식 컴포넌트에서 에러가 발생했을때 실행되고 getDerivedStateFromError에서 에러를 잡고 state를 결정한 후에 실행됨
      - 두개 인수를 받는데 첫번째는 error 두번째는 어떤 컴포넌트가 에러를 발생시켰는지 정보를 가지고 있는 info
      - getDerivedStateFromError는 render 단계에서 실행되지만 componentDidCatch는 커밋 단계에서 실행되기 떄문에 부수효과를 수행할 수 있음

클래스 컴포넌트의 한계
   - 데이터의 흐름을 추적하기 어려움
      - state가 어떤 식의 흐름으로 변경돼서 렌더링이 일어나는지 혹은 일어나지 않는지를 판단하기 어려움

   - 애플리케이션 내부 로직의 재사용이 어려움
      - 애플리케이션의 규모가 커질수록 재사용할 로직도 많아지는데 클래스 컴포넌트 환경에서 매끄럽게 처리하기가 쉽지 않음

   - 기능이 많아질수록 컴포넌트의 크기가 커짐
      - 로직이 많아질수록 , 생명주기 메서드 사용이 잦아질수록 컴포넌트의 크기가 기하급수적으로 커짐

   - 클래스는 함수에 비해 상대적으로 어려움
      - 자바스크립트 개발자들은 클래스보다 함수에 익숙함, 함수에 비해 클래스 사용이 어렵고 일반적이지 않음

   - 코드 크기를 최적화 하기 어려움
      - 최종결과물인 번들 크기를 줄이는데도 어려움

   - 핫리로딩을 하는데 상대적으로 불리함
      - 핫 리로딩이란 코드에 변경사항 발견했을때 앱을 다시 시작하지 않고 변경된 코드만 업데이트해 빠르게 적용하는 기법
      - 클래스컴포넌트는 설계특성상 불리함

2.3.2 함수 컴포넌트
   - 무상태 컴포넌트를 구현하기 위한 하나의 수단이었으나 리액트 16.8이후 훅이 등장하면서 유용하게 쓰이는중

   - 함수컴포넌트 예제
   import { useState } from 'react'
      type SampleProps = {
      required?: boolean
      text: string
   }
   export function SampleComponent({ required, text }: SampleProps) {
      const [count, setCount] = useState<number>(0)
      const [isLimited, setlsLimited] = useState<boolean>(false)
      function handleClick() {
         const newValue = count + 1
         setCount(newValue)
         setIsLimited(newValue >= 10)
      }
      return (
         <h2>
         Sample Component
         <div>{required ? '필수, : ’필수 아님,}</div>
         <div>문자: {text}</div>
         <div>count: {count}</div>
         <button onClick={handleClick} disabled={isLimited}>
         증가
         </button>
         </h2>
      ) 
   }

2.3.3 함수컴포넌트 vs 클래스 컴포넌트

생명주기 메서드의 부재
   - 클래스 컴포넌트의 생명주기 메서드가 함수 컴포넌트에서는 존재하지 않음
   - 함수 컴포넌트는 props를 받아 리액트 요소만 반환하는 함수, 클래스 컴포넌트는 render 메서드가 있는 React.Component를 상속받아 구현하는 자바스크립트 클래스
   - 생명주기 메서드는 React.Component에서 오는것이기 때문에 함수 컴포넌트에는 존재하지 않음
   - useEffect 훅을 사용해서 비슷하게 만들수는 있으나 비슷할뿐 똑같다고는 할 수 없다.

함수 컴포넌트와 렌더링된 값
   - 함수 컴포넌트는 렌더링된 값을 고정하고, 클래스 컴포넌트는 그렇지 못함
   - 클래스 컴포넌트는 props의 값을 this로부터 가져옴으로 render 메서드를 비롯한 리액트의 생명주기 메서드가 변경된 값을 읽을수 있게 됨
   - 함수 컴포넌트는 this에 바인딩 된 props를 사용하는 클래스 컴포넌트와 다르게 props를 인수로 받음
   - 인수로 받기때문에 값을 변경할 수 없고 해당 값을 그대로 사용하게 된다. 이러한 특성은 state도 마찬가지
   - 함수 컴포넌트는 렌더링 될때마다 그 순간의  props와 state를 기준으로 렌더링됨, props와 state가 변경된다면 그 값을 기준으로 함수가 호출된다고 볼수있음
   - 클래스 컴포넌트는 시간의 흐름에 따라 변화하는 this를 기준으로 렌더링이 일어남

클래스 컴포넌트를 공부해야 하나?
   - 이제 리액트를 막 배우기 시작했으면 함수 컴포넌트로 시작하되  리액트의 오랜 역사 동안 많은 코드들이 클래스 컴포넌트로 작성됐으며 이러한 흐름을 알기 위해서는 어느 정도의 클래스 컴포넌트에 대한 지식도 필요함
   - 자식 컴포넌트에서 발생한 에러에 대한 처리는 현재 클래스 컴포넌트로만 가능하므로 에러 처리를 위해서라도 클래스 컴포넌트에 대한 지식은 어느 정도 필요하다고 볼 수 있음

2.3.4 정리
   - 함수 컴포넌트를 기반으로 코드를 작성해 보면서 리액트를 익히고 이후 클래스 컴포넌트까지 익혀본다면 매우 좋을듯

2.4 렌더링은 어떻게 일어나는가?
   - 브라우저에서 렌더링이란
      - HTML과 CSS 리소스를 기반으로 웹페이지에 필요한 UI를 그리는 과정을 의미
   - 리액트에서 렌더링이란
      - 브라우저가 렌더링에 필요한 DOM트리를 만드는과정
      - 시간과 리소스를 소비해 수행됨. 해당 비용은 웹 애플리케이션을 방문하는 사용자에게 청구되며 시간이 길어지고 복잡해질수록 사용자의 경험을 저해함
      - 렌더링이 어떻게 왜 어떤 순서로 일어나는지 알아야 하고 렌더링과정을 최소한으로 줄여야함

2.4.1 리액트의 렌더링이란?
   - 리액트에서 렌더링이란 리액트 애플리케이션 트리 안에 있는 모든 컴포넌트들이 현재 자신들이 가지고 있는 는 props와 state의 값을 기반으로 어떻게 UI를 구성하고 이를 바탕으로 어떤 DOM 결과를 브라우저에 제공할 것인지 계산하는 일련의 과정을 의미

2.4.2 리액트의 렌더링이 일어나는 이유
   - 렌더링은 언제 발생하느냐?

   최초렌더링
      - 사용자가 처음 애플리케이션에 진입하면 브라우저에 정보를 제공하기 위해 최초 렌더링을 수행
   리렌더링
      - 처음 애플리케이션에 진입했을때 최초 렌더링이 발생한 이후로 발생하는 모든 렌더링
      - 리렌더링이 발생하는 경우
         - 클래스 컴포넌트의 setState가 실행되는 경우
         - 클래스 컴포넌트의 forceUpdate가 실행되는 경우
         - 함수 컴포넌트의 useState가 두번째 배열 요소인 setter가 실행되는 경우
         - 함수 컴포넌트의 useReducer의 두번째 배열 요소인 dispatch가 실행되는 경우
         - 컴포넌트의 key props가 변경되는 경우
            - 리액트에서 배열의 key는 리렌더링이 발생하는 동안 형제 요소들 사이에서 동일한 요소를 식별하는 값값
         - props가 변경되는 경우
         - 부모 컴포넌트가 렌더링될 경우
      
      -useState등으로 관리되지 않은단순한 변수는 아무리 변경된다 해도 리렌더링 발생시키지 않아 변경된 값을 렌더링된 DOM에서 확인할 수 없음

2.4.3 리액트의 렌더링 프로세스
   - 렌더링 프로세스가 시작되면 리액트는 컴포넌트 루트에서 부터 아래쪽으로 내려가면서 업데이트가 필요하다고 지정돼있는 모든 컴포넌트를 찾음
   - 업데이트가 필요한 컴포넌트를 발견하면 클래스 컴포넌트 인 경우 render 함수 실행, 함수 컴포넌트인 경우 FunctionComponent 자체를 호출한 뒤 결과물에 저장
   - 각 컴포넌트의 렌더링 결과물을 수집한 후 리액트의 새로운 트리인 가상DOM과 비교해 실제 DOM에 반영하기 위한 모든 변경사항을 수집함
   - 위와 같은 계산하는 과정을 리액트의 재조정 이라고 함, 끝나면 모든 변경사항을 하나의 동기 시퀀스로 DOM에 적용해 결과물을 보임
   - 리액트의 렌더링은 렌더 단계와 커밋 단계 두개로 분리되어 실행된다.

2.4.4 렌더와 커밋
   - 렌더단계
      - 컴포넌트를 렌더링하고 변경 사항을 계산하는 모든 작업
      - 렌더링 프로세스에서 컴포넌트를 실행해 결과와 이전 가상 DOM을 비교하는 과정을 거쳐 필요한 컴포넌트를 체크하는 단계 (type,props,key를 비교)
   - 커밋단계
      - 렌더단계의 변경사항을 실제 DOM에 적용해 사용자에게 보여주는 과정
      - 이 단계가 끝나야 브라우저의 렌더링이 발생

   - 리액트의 렌더링이 일어난다고 해서 무조건 DOM 업데이트가 일어나는것이 아님!
   - 렌더링을수행했으나 커밋 단계까지 갈 필요가 없다면, 즉 변경 사항을 계산했는데 아무런 변경 사항이 감지되지 않는다면 이 커밋 단계는 생략될 수 있다
   - 가시적인 변경이 일어나지 않아도 리액트의 렌더링은 발생할 수 있음
   - 리액트의 렌더링은 항상 동기식으로 작동
      - 렌더링과정이 길어질수록 애플리케이션의 성능저하로 이어지고 그만큼 다른 브라우저의 작업을 지연시킬 가능성이 있음
   - 리액트의 렌더링이 비동기로 이루어진다면 사용자는 하나의 상태에 대해서 여러가지 다른 UI를 확인할수도있음
   - 이는 사용자에게 혼란을 줄 수 있음 하지만 특정 상황에서는 유효할수도 있다

2.4.5 일반적인 렌더링 시나리오

   - 예제
   import { useState } from 'react'

   export default function A() {
      return (
         <div className="App">
            <hl>Hello React!</hl>
            <B />
         </div>
      )
   }

   function B() {
      const [counter, setCounter] = useState(0)
      function handleButtonClickQ {
         setCounter((previous) => previous + 1)
      }
      return (
         <>
            <label>
            <C number={counter} />
            </label>
            <button onClick={handleButtonClick}>+</button>
         </>
      )
   }

   function C({ number }) { 
      return (
         <div>
            {number} <D />
         </div>
      )
   }

   function D() {
      return <>리액트 재밌다!</>
   }

   - 컴포넌트를 렌더링하는 작업은 별도로 렌더링을 피하기 위한 조치가 돼 있지 않는 한 하위 모든 컴포넌트에 영향을 미친다. 
   - 부모가 변경됐다면 props가 변경됐는지와 상관없이 무조건 자식 컴포넌트도 리렌더링됨

2.4.6 정리
   - 리액트는 생각한것보다 꽤나 자주 리렌더링이 일어남
   - state나 props 등의 변화가 리액트 애플리케이션 전반에 어떠한 영향을 미치는지 살펴 볼것

2.5 컴포넌트와 함수의 무거운 연산을 기억해두는 메모이제이션
   - memo는 리액트에서 발생하는 렌더링을 최소한으로 줄이기 위해서 제공됨
   - 메모이제이션이 필요하다 vs 메모이제이션을 섣불리 해서는 안된다

2.5.1 섣부른 최적화는 독, 필요한 곳에만 메모이제이션을 추가하자
   - 메모이제이션은 어디까지나 비용이 드는 작업이므로 최적화에 대한 비용을 지불할때 신중해야 한다는 주장
   - 간단한 연산을 수행하는 함수가 있다고 가정할때 위 함수를 메모이제이션을 하는게 맞는가?
   - 메모이제이션은 비용이 든다. 값을 비교하고 렌더링 또는 재계산이 필요한지 확인하는 작업, 그리고 이전에 결과물을 저장해 두었다가 다시 꺼내와야 하는 두가지 비용이 있음
   - 메모이제이션은 신중하게 접근해야 하며 섣부른 최적화는 항상 경계해야 한다
   - 메모이제이션은 항상 어느 정도의 트레이드 오프가 있는 기법이라고 보는 것이 옳다. 이전 결과를 캐시로 저장해 미래에 더 나은 성능을 위해 메모리를 차례대로 점유하게 된다. 렌더링도 비용이지만 메모리에 저장하는 것도 마찬가지로 비용이다. 메모이제이션으로 인한 성능 개선이 렌더링보다 낫지 않다면 결국 안하느니만 못하는 상황을 마주하게 되는 것

2.5.2 렌더링 과정의 비용은 비싸다, 모조리 메모이제이션 하자
   - 일부 컴포넌트에서는 메모이제이션을 하는것이 성능에 도움이 된다.
   -  해당 컴포넌트가 렌더링이 자주 일어나며 그 렌더링 人］이에 비싼 연산이 포함돼 있고, 심지어 그 컴포넌트가 자식 컴포넌트 또한 많이 가지고 있다면 memo나 다른 메모이제이션 방법을 사용하는 것이 이 점이 있을 때가 분명히 있다
   - 위와같은 상황일때 선택지가 두가지 있음
   - memo를 다 적용 or memo를 컴포넌트의 사용에 따라 잘 살펴보고 일부에만 적용

   memo를 컴포넌트의 사용에 따라 잘 살펴보고 일부에만 적용
   - 위 방식의 경우는 이상적이지만 규모가 커지고 개발자가 많아지며 컴포넌트의 복잡성이 증가하는 상황에 해당 경우는 불가능할것으로 보임
   - 모든 개발자들이 최적화나 성능향상에 쏟을 시간이 많지 않음

   - 따라서 일단 memo를 감싼뒤에 생각해봄
   - 잘못된 memo를 지불해야 하는 비용은 props에 대한 얕은 비교가 발생하면서 지불해야하는 비용
   - 리액트는 기본적인 알고리즘 때문에 이전 결과물은 어떻게든 저장하고 있고 따라서 memo로 지불해야 하는 비용은 props에 대한 얕은 비교밖에없음(물론 props가 크고 복잡해진다면 비용이 커질수 있음)
   
   - memo를 하지 않았을때 발생하는 문제 
   - 렌더링을 함으로써 발생하는 비용
   - 컴포넌트 내부의 복잡한 로직의 재실행
   - 그리고 위 두 가지 모두가 모든 자식 컴포넌트에서 반복해서 일어남
   - 리액트가 구 트리와 신규 트리를 비교

   - 위만 봐도 memo를 하지 않았을때 처리해야 할 잠재적인 위험비용이 더 큼

   - useMemo와 useCallback을 사용해 의존성 배열을 비교하고, 필요에 따라 값을 재계산하는 과정과 이러한 처리 없이 값과 함수를 매번 재생성하는 비용 중에서 무엇이 더 저렴한지 매번 계산해야 함
   - 리렌더링이 발생할때 메모이제이션이 없으면 모든 객체는 재생성되고 결과적으로 참조는 달라지게 됨
   - 메모이제이션은 컴포넌트 자신의 리렌더링 뿐 아니라 이를 사용하는 쪽에서도 변하지 않는 고정된 값을 사용할 수 있다는 믿음을 줄수있음
   
   - 메모이제이션을 하지 않았을 때보다 메모이제이션을 했을때 더욱 많은 이점을 누릴수있음
   - 이는 안했을때 치러야 할 위험 비용이 더 크기때문에 최적화에 대한 확신이 없다면 가능한 모든곳에 메모이제이션을 활용해 최적화를 하는것이 좋다고 봄

2.5.3 결론 및 정리
   - 두 의견 모두 메모이제이션이 리액트 애플리케이션에서 할 수 있는 성능 최적화라는 사실을 알음
   - 리액트를 배우고 있거나 리액트를 이해하고 싶은경우, 이를 위해 시간을 투자할 여유가 있으면
      - 섣부른 메모이제이션을 지양하고 어느 지점에서 성능상 이점을 누릴수 있는지 살펴보는식으로 메모이제이션을 적용하는 것을 권장
      - 웹 애플리케이션 성능에 어떠한 영향을 미치는지 살펴보면서 진행하면 리액트에 대한 이해도와 웹 애플리케이션에 접근하는 관점을 넓히는 좋은 기회가 될것
   - 현업에서 리액트를 사용하고있거나 성능에 대해 시간적 여유가 없는 경우
      - 의심스러운 곳에는 메모이제이션을 다 적용해볼것
      - props에 대한 얕은 비교를 수행하는 것보다 리액트 컴포넌트의 결과물을 다시 계산하고 실제 DOM과 비교하는 작업이 더 무겁고 비쌈
      - 성능에 대해서 지속적으로 모니터링 하고 관찰하는것 보다 섣부른 메모이제이션 최적화가 주는 이점이 더 클 수 있음