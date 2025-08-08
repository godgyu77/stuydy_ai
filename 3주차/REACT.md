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

   87p부터 시작