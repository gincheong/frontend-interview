## 웹 페이지가 렌더링되는 과정

---

[링크](https://boxfoxs.tistory.com/408)  
브라우저는, 전달되는 HTML과 CSS 명세에 따라 HTML 파일을 **렌더링 엔진**으로 해석해서 표시한다.

- 브라우저 별 다른 엔진을 사용함
- 파이어폭스는 모질라에서 만든 게코(Gecko), 사파리와 크롬은 웹킷(Webkit)
  (크롬은 특이하게도 브라우저의 탭 별로 렌더링 엔진 인스턴스가 존재한다.)

렌더링 엔진은 통신으로부터, 요청한 문서의 내용을 얻는 것으로 작업을 시작함 (문서는 보통 8KB단위로 전송됨)

```
# 렌더링 엔진의 동작 과정
[DOM, CSSOM 생성(HTML 파싱)] -> [Render Tree 구축] -> [Render Tree 배치] -> [Render Tree 그리기]
```

1. DOM, CSSOM 생성

- 서버로부터 받은 HTML, CSS를 다운받고, 연산 및 관리를 위해 Object Model로 만든다. HTML은 DOM Tree로, CSS는 CSSOM으로
- 렌더링 엔진은 모든 데이터가 전송되기를 기다리지 않고, 전송되는 대로 HTML과 CSS를 파싱하여 일부 내용이라도 사용자에게 먼저 보여줄 수 있도록 작동한다.

2. Render Tree 생성

- DOM Tree와 CSSOM Tree가 만들어지면, 이 둘을 이용하여 Render Tree가 생성됨
- 각 요소에 스타일 정보가 설정되어 실제로 화면에 표시될 노드로만 구성됨
  (ex. display: none 인 요소는 제외됨)

3. Render Tree 배치 (Layout)

- 브라우저의 Viewport에서의 각 노드들의 위치와 크기를 계산함

4. Render Tree 그리기 (Paint)

- 설정된 스타일, 계산된 위치값을 기반으로 픽셀 값을 채워넣기 시작함
- 여기서 색, 이미지, 그림자 등이 모두 처리되며 그려짐, 스타일이 복잡할수록 Paint 단계가 오래 걸림

[브라우저는 어떻게 동작하는가?](https://d2.naver.com/helloworld/59361)

---

## 호이스팅이란

---

`함수 안에 있는 선언들을 모두 끌어올려, 해당 함수 유효 범위의 최상단에 선언하는 것`

Javascript는 변수의 생성과, 초기화 작업(선언과 할당)이 분리되어 진행된다.

Javascript가 실행될 때, 내부적으로 이루어지는 과정이며 실제로 코드가 위쪽에 실행되거나 하는 것이 아님, 따라서 실행 과정이나 메모리에 영향을 주지는 않는다.

`var` 변수와 _함수 선언문_ 에서만 호이스팅이 발생하며, `let`, `const` 변수와 _함수표현식_ 에서는 호이스팅이 발생하지 않는다. 또한 `class` 선언도 마찬가지로 호이스팅이 발생하지 않는다.  
(! 호이스팅이 발생하지 않는 것은 아님, 정정 필요)

```javascript
// 함수 선언문
function foo() {
  console.log('bar');
}

// 함수 표현식
var foo = function () {
  console.log('bar');
};
```

호이스팅 덕에 기본적으로 함수를 코드 위쪽에서 사용하고, 선언은 아래쪽에 해도 오류 없이 작동한다. 다만 예외가 되는 _함수 표현식_ 의 경우에는 오류가 발생할 여지가 있다.

```javascript
function foo() {
  var result = bar();
  var bar = function () {
    return 123;
  };
}
// 호이스팅 후
function foo() {
  var bar; // 변수는 호이스팅 발생
  var result = bar(); // 여기서 bar가 선언되지 않았기에 오류 발생
  // 'bar' is not a function
  bar = function () {
    return 123;
  }; // 함수 표현식은 호이스팅 발생 안 함
}
```

만약 아래 `bar()`함수가 `let`이나 `const` 변수로 선언되었다면 변수 자체의 호이스팅도 발생하지 않아 `'bar' is not defined'` 에러가 발생한다.

호이스팅에는 우선순위가 존재하며, **변수 선언**이 **함수 선언**보다 우선순위를 가진다.  
또한 값이 할당되어있는 변수는, 나중에 선언되는 같은 이름의 함수를 무시한다.

```javascript
var foo = 'this is a string.';
function foo() {
  console.log('this is a function');
}
console.log(typeof foo); // 'string'
```

---

## 클로저란

---

[링크](https://hyunseob.github.io/2016/08/30/javascript-closure/)

`클로저란 독립적인 (자유)변수를 가리키는 함수이다. 또한 클로저 안에 정의된 함수는 만들어진 환경을 '기억'한다.`  
`반환된 내부함수가, 선언될 때의 스코프를 기억하여 호출될 때에도 기억한 스코프에 접근할 수 있는데, 이 내부함수를 클로저라고 부른다.`

외부함수의 변수에 접근하는 내부함수가, 외부함수에서 반환되면  
그 반환된 함수를 closure라고 부른다.  
이 함수는, 외부함수의 라이프사이클이 끝나도 그 안에서 접근했던 변수에 계속 접근할 수 있다.

외부에서 제어할 수 없는 진정한 의미의 private한 변수를 갖게 됨

```javascript
function foo(number) {
  var text = `input number is: ${number}`;
  return function () {
    return text;
  };
}
var closure = foo(77);
console.log(closure()); // 'input number is 77'
```

`foo()`함수 안에서 정의되어 반환되는 함수를 `클로저`라고 부른다. 또한 `closure` 함수가 출력될 때 `77`이라는 입력값을 기억하는 것으로 **만들어진 환경을 기억**한다고 할 수 있다.

```javascript
function hello(name) {
  this._name = name;
}
hello.prototype.say = function () {
  console.log(`hello ${this._name}`);
};

var hello1 = new hello('gincheong');
hello1.say(); // 'hello gincheong'
```

### 클로저를 통한 은닉화

자바스크립트에서는 위와 같이 객체를 만들어 사용할 수 있는데, `hello` 객체의 `_name` 변수는 앞에 언더스코어를 붙임으로 `private`하게 사용하려는 변수임을 알 수 있다. 하지만

```javascript
hello1._name = 'asd';
```

와 같이 값을 쉽게 바꿀 수가 있어 실제로는 private하지 못하다.

여기서 **환경을 기억**하는 클로저를 이용하면, 외부에서 변수에 접근하지 않도록 private한 변수를 만들 수 있다.

```javascript
function hello(name) {
  var _name = name;
  return function () {
    console.log(`hello ${_name}`);
  };
}
var hello1 = new hello('gincheong');
hello1(); // 'hello gincheong'
```

클로저는 환경을 기억하기 위해 `hello`함수의 `_name` 지역변수를 메모리에 저장하게 된다. 클로저가 계속 존재하는 이상, `_name` 변수는 메모리에서 삭제되지 않기 때문에 메모리 누수가 발생할 수도 있다. 따라서 클로저를 더 사용하지 않게 되면. `hello1 = null;` 과 같이 클로저의 참조를 제거해주는 것이 좋다.

[링크](https://velog.io/@open_h/closure-and-scope)

Private Method 흉내내기

자바스크립트에서는 접근 지정자 개념이 따로 없기 떄문에, private한 멤버 변수나, 메소드를 실질적으로 만들어내는 기능은 없으나 클로저를 이용하여 비슷하게 구현할 수가 있다고 했다.

위쪽 hello 함수의 경우에는 반환되는 함수가 하나였기 때문데, 만들 수 있는 클로저가 하나였으나

```javascript
function Counter() {
  let counter = 0;
  this.increase = function () {
    return ++counter;
  };
  this.decrease = function () {
    return --counter;
  };
}

const counter = new Counter();
console.log(counter.increase()); // 1
console.log(counter.increase()); // 2
console.log(counter.decrease()); // 1
```

이런 식으로 만들면, private 멤버 변수를 제어하는 메소드를 여러 개 만들 수 있다.

---

## 프로토타입이란

---

[링크](https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67)

`Javascript의 객체는 모두 'Object' 의 하위 객체이며, Prototype Obejct을 통해 연결되어 있다. 이를 Prototype Chain이라 한다.`  
`인스턴스가 공통적으로 사용할 property나 method를 프로토타입에 미리 구현해두는 것으로, 인스턴스에서 추가적으로 생성하지 않게 할 수 있음. 이는 메모리 이득이다.`

```javascript
function myNumber() {
  this.min = 0;
  this.max = 100;
}

var num1 = new myNumber();
var num2 = new myNumber();

console.log(num1.min); // 0
console.log(num2.max); // 100
```

Javascript는 **Class** 가 없지만, 위처럼 `new` 키워드를 통해 클래스를 비슷하게 따라할 수 있다.  
하지만 `min`, `max` 변수는 num1, num2 객체에서 공통적으로 사용하며, 같은 값을 가지고 있지만 메모리에는 따로 할당되어 사용된다. 따라서 객체가 늘어날 수록 메모리에 중복된 값들이 많아진다.

```javascript
function myNumber() {}

myNumber.prototype.min = 0;
myNumber.prototype.max = 100;

var num1 = new myNumber();
var num2 = new myNumber();

console.log(num1.min); // 0
console.log(num2.max); // 100
```

여기서 위와 같이 `prototype`을 사용하면, `num1`, `num2` 객체 각각에 대해 `min`, `max` 변수가 따로 메모리에 할당되지 않고, 한 곳에 있는 변수를 공유해서 사용하게 된다.

- 모든 객체는 `Prototype Object` 을 가지며, `prototype` 속성으로 접근할 수 있다.
- `Prototype Object`는 기본 속성으로 `constructor` 와 `__proto__` 를 갖는다.  
  <br>

- `constructor`는 객체를 생성했던 함수를 가리킨다.
- `__proto__` 는, 객체가 생성될 때 상위 객체의 `Prototype Object`를 가리킨다.
- `prototype`을 통해 객체의 속성에 접근하려고 하면, `__proto__`를 통해서 속성이 발견될 때까지 상위 객체들을 탐색한다.  
  (`num1.min` 에서, 상위 객체를 돌며 `min`이 있는 곳을 찾은 것)

결과적으로 모든 객체는, 자바스크립트 기본 제공인 `Object` 객체라고 할 수 있다.  
**proto**를 통해 상위 속성에 접근한다는 개념은, 모든 객체들에서 `.toString()` 함수를 호출할 수 있다는 점을 생각하면 이해하기 쉽다.

`.__proto__` 는 모든 객체에 존재하며, 객체 입장에서 자신의 부모 역할을 하는 객체를 가리킴,  
`.prototype`은 함수형 객체에만 존재하며, 그 객체가 생성자로 쓰일 때 생성된 객체의 부모 역할을 하는 객체를 가리킴  
(그러면 함수형 객체 자기 자신이 아닌가? 왜 이렇게 표현한지는 잘 모르겠음)

\*\* 참고사항
`__proto__`는 ES6에서 deprecated되었으며, `Object.getPrototypeOf()` 와 `Object.setPrototypeOf()`를 쓰라고 함

그리고 ES6에서는 Class 문법이 생기면서, .prototype 으로 직접 접근하여 프로퍼티를 정의하는 방식은 안 쓰게 되었다.  
어차피 Class도 prototype 기반인 건 동일함

---

## GET과 POST의 차이

---

### GET

- 데이터를 **조회**하기 위해 설계된 메소드
- 쿼리스트링을 통해 데이터를 전송하며, 데이터 크기에 제한이 있음 (브라우저 별 상이)
- 캐싱될 수 있음

### POST

- 데이터를 **생성/변경**하기 위해 설계된 메소드
- HTTP 메세지의 Body를 통해 데이터를 전송하며, 데이터 크기에 제한이 없음
- 캐싱되지 않음

데이터가 쿼리스트링에 노출되지 않고, Body에 전송되는 POST 방식이 보안성이 좋다고 할 수 있다.

```
GET은 Idempotent, POST는 Non-idempotent
```

Idempotent(멱등)은 수학젹 개념으로, `연산을 여러 번 적용하여도 결과가 달라지지 않는 성질` 을 뜻한다.  
`GET`은 데이터를 조회하기만 하기 때문에 멱등성을 갖고, `POST`는 데이터를 변경하기 때문에 멱등성을 갖지 못한다. 그렇기에 GET만 캐싱된다.

HTML이나, CSS 데이터들이 주로 GET으로 요청되며, 캐싱된다.

---

## 브라우저 저장소들의 차이점

---

### 공통점

- 도메인 별로 따로 저장됨
- 사용자가 값을 변경할 수 있음

### Cookie

- 서버로 매번 전송됨
- 갯수(20개)와 크기(4KB)에 제한이 있음
- 데이터가 유지되는 제한 시간이 있음 (만료 시간)

### WebStorage (LocalStorage, SessionStorage)

- 객체화된 데이터를 저장할 수 있음
- 갯수와 크기에 제한이 없음
- 브라우저 별로 따로 저장되기 때문에, 같은 도메인을 접속한 상태여도 Storage가 각각임
- ### LocalStorage
  - 사용자가 데이터를 지정하여 삭제하지 않는 이상 영구적으로 존재함
  - 다른 탭 들에서 모두 공유됨
- ### SessionStorage
  - 지속 시간이 정해져있지는 않으나, 브라우저가 종료될 때 자동으로 삭제됨
  - 정확히는 브라우저의 "탭" 별로 따로 구성됨

---

## React와 Vue

---

[차이점 실전편?](https://erwinousy.medium.com/%EB%82%9C-react%EC%99%80-vue%EC%97%90%EC%84%9C-%EC%99%84%EC%A0%84%ED%9E%88-%EA%B0%99%EC%9D%80-%EC%95%B1%EC%9D%84-%EB%A7%8C%EB%93%A4%EC%97%88%EB%8B%A4-%EC%9D%B4%EA%B2%83%EC%9D%80-%EA%B7%B8-%EC%B0%A8%EC%9D%B4%EC%A0%90%EC%9D%B4%EB%8B%A4-5cffcbfe287f)

[비교1](https://velog.io/@vraimentres/react-vs-vue-1)

### 공통점

- 컴포넌트 기반의 SPA 라이브러리(React)/프레임워크(Vue)
  - 컴포넌트 단위로 데이터 변화를 감지하고, 렌더링이 필요한 부분만 부분적으로 변화시켜줌
- `prop`, `state`, 그리고 라이프사이클
  - 컴포넌트 단위 개발이라는 공통점 탓에, 사용법은 달라도 기본적인 구조 자체는 비슷함
  - 리액트의 라이프사이클 (Mount -> Props Update -> State Update -> Unmount)
- Virtual DOM 사용
  - Virtual DOM을 사용해서 미리 DOM을 가상으로 그려본 후에, 실제 DOM과의 차이점을 계산함
  - 결과적으로 실제 DOM에서는 갱신이 필요한 부분만 부분적으로 렌더링을 다시 수행하기 때문에 성능 상 이득

### 차이점

1. 컴포넌트의 데이터를 바꿀 때

- Vue는, `this.{변수} = {값}` 의 형태로 바로 값을 변경할 수 있다.
- React는, `this.setState()` 함수를 통해 데이터를 바꾼다, setState함수를 써야만 React의 라이프사이클이 정상적으로 진행된다.
- Vue에도 물론 비슷한 형태의 라이프사이클이 존재하지만, 값을 단순히 대입하는 것으로도 라이프사이클이 진행되도록 되어 있음

2. HTML을 표현하는 방식

- Vue는, 기본 HTML 문법을 그대로 따르기 때문에, 특정 환경이 없이도 Vue 메인 스크립트를 불러오기만 하면 어디에서나 작동한다.
- React는, JSX라는 개별적인 표현 문법을 사용하는데, 그렇기 때문에 Vue와 달리 단순 실행으로는 작동하지 않음. `react-scripts start` 명령어 같은 걸로 따로 JSX를 해석할 환경? 도구가 필요하다.

3. 스타일 적용

- Vue는, 각 요소에 unique한 속성을 추가하여 스타일 적용을 제한할 수 있다.
- React는, 다른 라이브러리를 추가해야만 컴포넌트 단위의 스타일 적용이 가능하다.

4. state 변경 시의 리렌더링

- Vue는, 컴포넌트의 종속성이 자동으로 추적되어 실제로 다시 렌더링이 필요한 컴포넌트를 정확히 알고 있음 (공식문서에서 그렇게 나왔는데 어떤 방식인지는 모르겠음)
- React는, `shouldComponentUpdate` 함수를 오버라이드해서 컴포넌트가 리렌더링될 지, 아닐 지를 개발자가 직접 결정해야 하며 그런 작업이 필요한 컴포넌트를 스스로 추적해야 한다.

---

## React와 Redux

---

[링크](https://d2.naver.com/helloworld/1848131)

기본 폴더 구조는 **component, store, reducer, action** 폴더로 나뉨

### action

`Reducer에게 보내는 명령, 여기서 보낸 대로 Reducer가 저장소에 작업을 수행한다.`  
API 통신 등의 'action method' 를 정의하는 스크립트 모음, 여기서 실행되는 dispatch 함수 등으로 데이터가 제어된다.

### component

예시 코드 하나 보기

```javascript
class TODOList extends Component {
  render() {
    const { todos, onClick } = this.props;
    return (
      <ul>
        {todos.map((todo) => (
          <Todo key={todo.id} onClick={onClick} {...todo} />
        ))}
      </ul>
    );
  }
}

// 컨테이너 컴포넌트에서 프레젠테이션 컴포넌트로 전달하는 state
const todolistStateToProps = (state) => {
  return {
    todos: state.todos,
  };
};

// 컨테이너 컴포넌트에서 프레젠테이션 컴포넌트로 액션을 보내는 함수
const todolistDispatchToProps = (dispatch) => {
  return {
    onClick(data) {
      dispatch(complete(data)); // 액션 메서드
    },
  };
};

// 컴포넌트에서 store를 구독하여, 데이터를 가져다 쓸 수 있게 된다.
export default connect(todolistStateToProps, todolistDispatchToProps)(TODOList);
```

### reducer

`React 저장소에 유일하게 접근할 수 있는 객체로, 들어오는 action에 따라 행동한다.`  
리듀서들로 이루어진 폴더, 리듀서를 정의한 것을 보자

```javascript
import todoAction from '../action/index';
const { ADD_TODO } = todoAction.todo;

const todo = (state, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
        text: action.text,
        completed: false,
      };
    default:
      return state;
  }
};

const todos = (state = [], action) => {
  switch (action.type) {
    case ADD_TODO:
      return [...state, todo(undefined, action)];
    default:
      return state;
  }
};
```

### store

`전역 상태를 저장하는 저장소. Reducer를 통해서만 접근할 수 있다.`  
하나의 앱에 저장소도 하나이기 때문에, 보통 store 폴더에는 `index.js` 파일 하나만 생성한다.
여기서 미들웨어도 지정 가능하며, 미들웨어란 간단하게는 `dispatch()` 함수를 실행하기 전에 원하는 작업을 할 수 있게 하는 도구라고 생각하면 된다.

---

## state와 prop

---

```
state는 독립적인 컴포넌트의 상태
prop는 외부(부모) 컴포넌트에게 받은 속성
```

Redux 프로젝트에서의 prop는 앱에서 전역으로 관리하는 state라고 생각하면 된다.

컴포넌트 자체의 상태(색상, 애니메이션 등)는 state로 관리  
서버와의 통신 등 동기화해야 하는 데이터들은 부모 컴포넌트에서 prop로 받아서 사용

prop를 변경하면 App 전체가 갱신되기 때문에, state 사용보다 느리다.

---

## Redux vs Context API

---

[링크](https://velog.io/@cada/React-Redux-vs-Context-API)

Redux에서는, 단계적으로 prop를 전달하는 방식을 해소하기 위해 전역에서 접근할 수 있는 데이터 컨테이너(store)를 사용한다.

Context API 또한 Redux와 마찬가지로 state를 중앙 관리하는 도구이며, React에서만 사용할 수 있다. 또한 Redux와는 다르게 저장소가 여러 개 존재할 수도 있다.

Context API는 크게, 상태가 저장되는 `context`, 상태를 제공하는 `Provider`, 그리고 상태를 받아 사용하는 `Consumer`로 나뉘어져 있다.

- 전역 state 관리라는 점은 유사하나,
- React의 경우에는 'react-thunk'와 같은 추가 dependancy를 이용하여 비동기 처리 등을 쉽게 할 수 있다.
- Context API의 경우에는 추가 dependancy가 필요 없으나, 여러 state를 동시에 컴포넌트에 전달하려고 할 때에 Provider를 중첩해서 사용해야 함

---

## SASS, SCSS

---

```
Syntatically Awesome Style Sheets (문법적으로 쩌는 스타일시트)
Sassy CSS (쩌는 CSS)

CSS 전처리기라고도 함
```

CSS와 비교하여 ...

- 변수를 사용할 수 있다.
- 조건문과 반복문을 사용할 수 있다.
- Import를 사용할 수 있다.
- Mixin을 사용할 수 있다. (함수처럼 preset을 만들어 적용할 수 있음)
- Extend/Inheritance를 사용할 수 있다.

다만 바로 사용할 수 없고, 전처리기라는 말대로 컴파일이 필요하다.  
React와 사용한다면 webpack 설정을 통해서 서버 실행 시 SCSS를 자동 컴파일할 수 있다. [링크](https://medium.com/@jsh901220/react%EC%97%90-scss-%EC%A0%81%EC%9A%A9%EB%B0%8F-%EA%B8%B0%EB%B3%B8-%EC%82%AC%EC%9A%A9%EB%B2%95-1-c7bd2895f5a6)

---

## CORS

---

`Cross-Origin Resource Sharing`

"출처가 다른" 두 서버가 통신할 때, CORS 정책 위반으로 인한 오류가 발생할 수 있다.

### 출처가 같다? 다르다?

두 URL의 구성 요소 중에 `Scheme`, `Host`, `Port`, 3가지가 동일하면 "같은 출처"라고 판단한다.

```markdown
https://github.com:80/gincheong/frontend-interview

Scheme: https://
Host: github.com
Port: 80
```

포트가 명시되어있지 않은 경우에는, 각 브라우저의 정책에 따라 나뉘게 된다.

여기서 알 수 있는 사실, 이 CORS 정책을 위한 출저 비교 로직이 서버사이드가 아니라 브라우저에 구현된 것이라는 사실.  
그래서 만약 CORS 정책을 위반하는 일이 발생해도, 서버에서는 정상적으로 데이터를 반환하는 것으로 로그가 남게 된다.

내가 만든 앱에서, 서버로 요청을 보내는 상황을 가정해보자.  
내 앱 주소는 `https://gincheong.com`이다. 그리고 요청을 받을 서버는 `https://github.com` 이다.

내 앱에서 서버로 요청을 보내면, 그에 대한 응답으로 서버에서는 Header에 `Access-Control-Allow-Origin` 이라는 항목을 반환하는데, 이 안에 담긴 주소들이 서버에서 허용한 출처이다.  
브라우저는 Response Header에 담긴 허용 출처를 확인하고, 서버로 요청을 보냈던 Origin(`https://gincheong.com`)과 비교하여 "같은 출처" 가 아니라면 CORS 정책 위반 오류를 발생시킨다.

### CORS 위반을 피하려면?

1. 서버에서 `Access-Control-Allow-Origin`에 주소를 입력하기

- `*` 을 입력해서 모든 주소에 대해 요청을 허용하는 방법도 있지만, 실질적으로 아무나 다 쓰게끔 서버를 열어두려는 게 목적이 아니라면 비추

2. 프록시 기능으로 브라우저 속이기

- `http-proxy-middleware` 같은 라이브러리를 사용하여 해소할 수 있다.
- ?

---

## RESTful API

---

`REST: Representational State Transfer`  
`API: Application Programming Interface`

REST 아키텍처의 제약 조건을 준수하는 API

RESTful API 설계 시에는 ...

1. URI는 정보의 자원을 표현해야 한다.
2. 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE)로 표현한다.

URI에는 자원에 대한 행위를 표시하지 말라는 뜻.

예를 들어, `GET /musics/delete/1` 처럼, "delete" 한다는 행위를 URI에 담지 말라는 뜻.  
`DELETE /musics/1` 처럼 쓸 수 있게 해야 한다.

GET은 조회, POST는 생성, PUT은 수정, DELETE는 삭제 메소드

PUT은 이미 존재하는 데이터를 수정할 수 있도록 식별자를 URI에 추가하여(`PUT /musics/1`) 요청하도록 하지만, 식별자(예시에서 `1`)에 해당하는 데이터가 존재하지 않을 경우에는 POST와 마찬가지로 데이터를 새로 생성하게 된다.  
POST는 PUT과 달리, 식별자를 입력받지 않기 때문에 2번 실행하면 2개의 데이터가 생성된다.

[더 많은 메소드들과 설명](https://javaplant.tistory.com/18)

---

## Event Bubbling/Capture/Delegation

---

**Event Bubbling, 이벤트 버블링**

- 이벤트가 상위 요소로 전달되는 것
- 특정 요소에서 이벤트가 발생하면, 최상위 객체까지 이벤트를 전파시킨다.

**Event Capture, 이벤트 캡쳐**

- 버블링과 반대로, 하위 요소로 이벤트가 전달되는 것
- 특정 요소에서 이벤트가 발생하면, 해당 요소에 도달할 때까지 최상위 요소로부터 하위 요소로 탐색하며 이벤트를 전파시킨다.
- `addEventListener` 함수에 option을 지정하는 것으로 사용할 수 있다.

```javascript
$element.addEventListener('click', callbackFunc, { capture: true });
```

**Event Delegation, 이벤트 위임**

- `하위 요소에 각각 이벤트를 붙이지 않고, 상위 요소에서 하위 요소의 이벤트를 제어하는 방식`
- 기본적으로 이벤트 버블링 방식으로 작동하기 때문에, 하위 요소에서 발생하는 이벤트를 상위 요소에서 감지할 수 있다.  
  따라서 하위 요소의 각각에 이벤트 리스너를 등록할 필요가 없어진다.
- 유동적으로 element가 추가되는 환경에서, element 생성과 동시에 매번 이벤트 리스너를 새로 등록하는 번거로운 짓을 하지 않아도 됨

`event.stopPropagation()` : 이벤트 전파를 중단시키는 함수  
Bubbling의 경우에는 이벤트가 발생한 요소 자체에만 이벤트를 발생시키며,  
Capture의 경우에는 이벤트가 발생한 요소의 최상위 요소에만 이벤트를 발생시킨다.

---

## ES6 주요 문법

---

1. 변수 `let`과 `const`

- 범위가 전역인 `var`와 달리 `let`, `const`는 선언한 내부 블록에서만 유효함.
- 정확한 표현으로 `var`는 `Function-level Scope`, `let`, `const`는 `Block-level Scope`
- `let`은 재정의가 불가능하고, `const`는 상수로서 값 변경이 불가능하다.

```javascript
var number = 2;
var number = 3; // (O)
number = 4;

let number = 2;
let number = 3; // (X)
number = 4; // (O)

const number = 2;
const number = 3; // (X)
number = 4; // (X)
```

또한 `var` 변수는 호이스팅에 의해서, 초기화를 하지 않아도 값을 호출할 수 있다.  
반면에 `let`과 `const`는 초기화 전에 값을 호출하려고 하면 오류가 발생한다.

```javascript
console.log(asd); // ReferenceError: 'asd' is not defined
```

```javascript
console.log(asd); // undefined
var asd = 123; // 호이스팅시에 자동으로 undefined로 초기화됨
```

```javascript
console.log(asd); // ReferenceError: Cannot access 'asd' before initialization
let asd = 123; // const 도 마찬가지
```

`let`과 `const`에는 호이스팅이 발생하지 않는다고 오해할 수도 있는데, 호이스팅을 모두 올바르게 하고 있기 때문에 `before initialization` 같은 에러 메시지를 내놓을 수 있는 것임.

2. Backtick( ` )을 이용한 템플릿 리터럴

- 문자열 덧셈을 + 없이 간편하게 할 수 있다.
- 또한 문자열 내에 변수 값 삽입도 간편함

```javascript
const innerHTML = `<input type="number" value=${inputValue} />`;
```

3. 함수 기본 매개변수 값 지정

```javascript
function myFunc(a = 10, b = 20) {
  ...
}
```

4. 화살표 함수

```javascript
const numbers = [1, 2, 3];

const newNumbers = numbers.map((val) => val + 1);
const plusNumber = (number) => number + 1;
// 중괄호를 생략해서 바로 return할 수 있음
```

익명 함수나 콜백 함수로 사용할 수 있다.

어차피 변수에 익명 함수 대입할 수 있음

5. 비구조화 할당

```javascript
const album = {
  price: 50000,
  name: 'One Reeler',
  titleTrack: 'panorama',
};
const { name, titleTrack } = album;
// album 객체의 name, titleTrack이 할당, key 값이 동일해야 함

const numbers = [1, 2, 3];
const [one, two, three] = numbers;
// numbers의 값이 순서대로 할당됨
```

6. Promise

- 자바스크립트의 비동기 처리에 이용되는 객체

```javascript
const connect = new Promise((resolve, reject) => {
  /* {서버와 통신하여 데이터를 가져온다고 가정하자} */

  if (response.ok) {
    // 작업이 성공하면
    resolve(response.data);
  } else {
    // 작업이 실패하면
    reject('Error Occured');
  }
});

connect
  .then((success) => {
    // resolve함수에서 전달된 response.data 값이 전달됨
    console.log(success);
  })
  .catch((error) => {
    console.error(error); // 출력: Error Occured
  });
```

7. reduce 함수

- 배열의 각 값에 대해, 메소드를 실행하고 결과값을 하나로 합쳐 반환하는 함수

```javascript
// reduce의 콜백함수의 인자들
/**
 * @param {any} acc accumulator 누산기
 * @param {any} cur 배열에서 참조 중인 값
 * @param {number} idx 참조 중인 배열의 인덱스
 * @param {array} src 원본 배열
 * @return {any} acc값이 반환됨
 */
const callback = (acc, cur, idx, src) => ;
```

사용예시

```javascript
// 배열 원소들의 총합을 반환
const arr = [1, 2, 3, 4, 5];

const result = arr.reduce((acc, cur) => {
  return acc + cur; // 배열의 각 값을, acc에 누적 덧셈
});

console.log(result); // 15
```

---

## 화살표 함수와 function 키워드 함수

---

[링크](https://poiemaweb.com/es6-arrow-function)

`this`의 차이

this란, 함수나 객체를 호출하는 "주체" 가 저장되는 property라고 볼 수 있다.

자바스크립트의 this 키워드는 함수 자체에서 정해져 있는 것이 아니라, 함수가 호출될 때의 환경에 따라 동적으로 정의된다.

기본적으로는 `this`는 전역 객체에 바인딩된다. (Browser Side에서는 `window`, NodeJS와 같은 Server Side에서는 `global`)  
정확하게는 생성자 함수와 객체의 메소드를 제외한 모든 함수(내부 함수, 콜백 함수 포함) 내부의 this는 전역 객체를 가리킨다.

```javascript
function foo() {
  // 전역 함수에서는 전역 객체가 바인딩
  console.log(this); // window

  function bar() {
    // 내부 함수에서도 상위 객체(함수)가 바인딩되지 않고 전역 객체가 바인딩됨
    console.log(this); // window
  }
  bar();
}
foo();
```

```javascript
var value = 1;
const obj = {
  value: 2,
  foo: function () {
    const that = this; // 내부함수의 바인딩을 회피하는 방법

    // 메소드를 호출한 객체에 바인딩됨
    console.log(this, this.value); // obj 2

    function bar() {
      // 내부함수로서 this에 전역 객체 바인딩
      console.log(this.this.value); // window 1
      console.log(that, that.value); // obj 2
    }
    bar();
  },
};
obj.foo();
```

위처럼 일반 함수들은 동적으로 `this`가 가리키는 객체가 다르기 때문에 적절히 조절해서 사용해야 한다.

반면에 화살표 함수로 선언한 객체(함수)는 내부의 `this`가 `상위 스코프의 this`를 가져오도록 정적으로 정의된다.

```javascript
var value = 1;
const obj = {
  value: 2,
  foo: function () {
    console.log(this, this.value); // obj 2

    // 화살표 함수로 변경
    bar = () => {
      console.log(this, this.value); // obj 2
    };
    bar();
  },
};
obj.foo();
```

위의 예시에, 이전과 달리 내부함수를 화살표 함수로 정의했기 때문에 상위 스코프(foo 메소드)의 `this`인 `obj`를 가져온다.

따라서 다음과 같은 경우에는 화살표 함수를 사용하는 것을 피하는 것이 좋다.

### 메소드 정의 시

```javascript
// Bad
const person = {
  name: 'Lee',
  sayHi: () => console.log(`Hi ${this.name}`),
};

person.sayHi(); // Hi undefined
```

화살표 함수가 `sayHi` 메소드 자체에 대입되므로(?) `this` 또한 현재 `sayHi` 메소드가 있는 객체의 "상위 객체" 를 참조하게 된다. 따라서 화살표 함수 안의 `this`는 전역 객체인 `window`가 들어가므로, name 변수를 찾지 못해 undefined가 출력된다.

위와 같은 경우에는 화살표 함수를 사용하지 않아야 원하는 대로 `this.name`을 가져올 수 있다. 대신 function 키워드를 사용하지 않는 축약 표현이 ES6에 존재하는데, 다음과 같이 사용할 수 있다.

```javascript
// Bad
const person = {
  name: 'Lee',
  sayHi() {
    console.log(`Hi ${this.name}`);
  },
};

person.sayHi(); // Hi Lee
```

### 생성자 함수로 사용

```javascript
const Foo = () => {};

// 화살표 함수는 prototype 프로퍼티가 없다
console.log(Foo.hasOwnProperty('prototype')); // false

const foo = new Foo(); // TypeError: Foo is not a constructor
```

화살표 함수는 최상위 객체로부터 `prototype` 객체를 가지고 있지 않아, `prototype` 객체로부터 받아 사용하는 생성자 함수(`constructor()`)를 사용할 수 없다. 따라서 생성자가 될 수 없다.

### 이벤트리스너의 콜백 함수로 사용

```javascript
var button = document.getElementById('myButton');

button.addEventListener('click', () => {
  console.log(this === window); // => true
  this.innerHTML = 'Clicked button'; // 작동하지 않음
});
```

위와 같이, 이벤트리스너 콜백 함수에 화살표함수를 사용하면 `this`가 전역 객체를 가리키기 떄문에 원하는 작업을 할 수 없다.

```javascript
var button = document.getElementById('myButton');

button.addEventListener('click', function () {
  console.log(this === window); // => false
  this.innerHTML = 'Clicked button'; // 올바르게 작동함
});
```

맨날 콜백 함수 화살표함수로 만들고 인자로 event 받아서 event.target으로 element 찾았는데 멍청한 짓이었음

---

## async, await

---

`ES8에서 소개된, 비교적 최신의 자바스크립트 비동기 처리 방식`

`async` 키워드는 함수 앞에 붙으며, 해당 함수가 Promise 객체를 반환하도록 한다.

```javascript
async function myFunc() {
  return 123;
} // fulfilled 상태의 Promise 객체가 반환됨
```

`await` 키워드는 `async` 함수 내에서 사용되며, Promise 객체를 반환하는 메소드 앞에 붙여 사용한다.

```javascript
async function returnData(url) {
  const data = await fetch(url); // fetch가 완료될 때까지 대기함

  return data;
}
```

위의 코드는, `fetch()` 부분에서 await로 대기 명령(?)을 걸지 않으면, 자바스크립트의 동기 처리에 따라 `data` 변수를 가져오기도 전에 `return`이 실행되어, **undefined**를 반환하게 된다.

**예외 처리**  
async, await 에서는 `try - catch`로 예외처리를 한다.

```javascript
async function returnData(url) {
  try {
    const data = await fetch(url);
    return data;
  } catch (err) {
    // error handling
    console.error(err);
  }
}
```

---

## .apply, .bind, .call

---

[링크](https://velog.io/@rohkorea86/this-%EC%99%80-callapplybind-%ED%95%A8%EC%88%98-mfjpvb9yap)

- apply, bind, call은 `Function.Prototype`으로부터 상속받는 모든 함수에 포함된 메소드이다.
- **함수의 실행영역**을 지정하는 메소드다.

```javascript
const student = { age: 28 };
const introduce = function (name) {
  console.log(`my name is ${name} and I'm ${this.age}`);
};

introduce('ginc'); // my name is ginc and I'm undefined
introduce.call(student, 'ginc'); // my name is ginc and I'm 28
introduce.apply(student, ['ginc']); // my name is ginc and I'm 28

const newFunction = introduce.bind(student);
newFunction('ginc'); // my name is ginc and I'm 28
```

`call`, `apply` 는 실행 영역을 첫 번째 인자로 받아, 해당 함수를 바로 실행하지만  
`bind`는 실행 영역을 지정한 새로운 함수를 반환한다.

여기서 `call`은 함수에 대한 인자를 `,` 로 구분하여 받고, `apply`는 배열로 인자를 받는다는 차이가 있다.

---

## webpack

---

```
webpack은 웹에서 사용되는 모든 자원(assets)을 번들링하는 도구
번들링이란 여러 개의 파일 중에서 종속성이 존재하는 파일을 하나의 파일로 묶어 패키징하는 과정을 의미한다.
```

웹팩의 주요 네 가지 개념

1. entry

- 의존성(종속성)을 갖는 파일들 중에, 이 의존성의 시작점을 웹팩에서는 entry라고 하며, 이 entry를 기준으로 패키징을 한다.

2. output

- 번들링이 완료된 결과물을 output에 저장하고, html에서는 이 번들링 결과물을 불러오면 된다.

3. loader

- 자바스크립트 이외의 이미지, 폰트, 스타일시트 파일도 번들링하기 위하여 loader를 사용한다. 대표적인 예시로
- **babel-loader**
  - ES`6`문법을 ES`5`문법으로 변환할 때 사용한다.
- **style-loader**, **css-loader**
  - css-loader는 `css` 파일을 `js` 파일로 변환해주고
  - style-loader는 css-loader에서 변환된 `js` 파일을 DOM에 추가해준다.

4. plugin

- 번들링이 완료된 결과물 파일을 처리하는 부분
- 대표적으로, 난독화를 위한 **UglifyJsPlugin** 이 있다.

---

## Javascript는 싱글 스레드

---

C나, Python같은 언어는 프로그래머가 코딩한 line 순서대로 실행되는 **동기**적 특성을 가진다.  
그와 반대로 Javascript는 blocking이 따로 없는 **비동기**적인 특성을 가진다.

```javascript
setTimeout(() => console.log(1), 2000);
console.log(2);
```

1초 기다리고 `1`을 출력하라고 했는데, Javascript는 이 1초를 기다리면서도 다음 라인의 `console.log(2)`를 바로 실행한다.  
Javascript는 싱글 스레드인데 어떻게 이런 비동기적 실행이 가능한 것인가?

### Web API, Stack, Event Loop, Callback Queue

브라우저에는 자바스크립트 엔진의 실행을 보조하는 Web API라는 녀석이 존재한다. 이 Web API가 별도의 스레드에서 실행되어 자바스크립트의 비동기 처리를 돕게 된다.

비동기 처리가 필요한 함수로 위에서 사용되었던 `setTimeout` 함수를 들 수 있다. 기본적으로 자바스크립트 엔진은 실행할 코드를 `call stack`에 가져와서 하나씩 실행을 하는데, 비동기 처리가 필요한 함수들은 자바스크립트 엔진의 `call stack`에서 바로 실행되지 않고 `Web API`로 전달된다.

(소스 코드 실행 중에 오류가 발생하면, `stack trace`에 오류가 발생한 위치들이 기록되는데, 이것이 `call stack`을 따라 올라가면서 만들어진다)

비동기 작업을 전달받은 Web API는 해당 작업을 자바스크립트 엔진과는 별개로 수행하기 때문에, 이 때 다른 코드들이 다시 `call stack`으로 호출되어 실행될 수 있다. 그리고 Web API에서 비동기 처리를 마치면, 그 결과가 `callback queue`로 전달되며 여기서 `Event Loop`가 자바스크립트 엔진의 `call stack`이 비어있는지 확인하고(결과물을 실행할 수 있는지), 비어있다면 결과물을 `call stack`으로 전달한다. `call stack`이 비어있는지를 반복 확인한다고 하여 `Loop`라는 이름이 붙었다고 한다.

또한 Web API는 그 자체가 멀티 스레드 방식이기 때문에, setTimeout 함수가 동시에 여러 개 실행되어도 무방하다.

위의 처리 방식을 이해했다면,

```javascript
setTimeout(() => console.log(1), 0); // interval을 0으로 했다
console.log(2);
```

이것의 출력이 `2 -> 1` 순이라는 것이 이해가 될 것이다.  
`setTimeout` 은 Web API를 통해 실행되기 때문에, console.log(1) 의 결과값이 `Callback Queue`로 전달되는 동안에도 시간이 걸리기 때문!

주요 키워드, `Call Stack`, `Web API`, `Callback Queue`, `Event Loop` 를 기억해두기  
위의 처리 순서를 실시간으로 볼 수 있는 [이곳](http://latentflip.com/loupe)

(추가)  
단순 예시이긴 한데, `forEach()` 로 오래 걸리는 작업을 실행시킨다고 하면  
forEach 내부의 각각의 작업들은 `Call Stack`을 차지하면서 실행되기 때문에 브라우저와의 상호작용이 block될 수 있다.

브라우저는 기본적으로(?) 1초에 60번 렌더링을 하는데, `Callback Queue`와 마찬가지로 별도로 `Render Queue` 라는 곳에 0.016초마다 렌더 작업이 추가되어, 동일한 방식으로 `Call Stack`이 비어있을 때를 기다린다.

여기서 위의 `forEach()` 안의 작업이 긴 시간이 걸리는 작업이여서, `Call Stack`이 오랫동안 비어있지 않게 된다면? `Render Queue`의 렌더 명령이 실행되지 않아서 페이지와의 상호작용이 멈추가 된다. (클릭이나, 애니메이션 등, 브라우저가 작동하지 않게 됨)

이럴 때 렌더링이 되지 않는 문제를 해결하기 위해서, forEach 내부의 각 작업에 interval을 0으로 준 `setTimeout`함수를 사용한다면?  
각 작업들이 Web API를 통해 `Callback Queue`로 이동하게 되면서, 원하는 대로 각 작업을 순차적으로 실행은 하지만 `Call Stack`을 점거하지 않기 때문에 렌더링 작업이 멈추지 않는다.

이 영상이 많이 도움된다. [유투부 링크](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

---

## Typescript는 뭐가 다른가

---

`type`이 존재한다는 점에서 다르다. 바닐라 Javascript에는 `typeof()`함수로 확인할 수 있는, 변수의 type자체는 존재하나 선언할 때 따로 지정하거나 하진 않는다.

```javascript
const a = 3;
const b = '2';
console.log(a * b); // 춢력: 6
```

따라서 위와 같은 코드에서, a는 `number`이고 b는 `string`인데 자바스크립트는 신기하게도 알아서 b를 `number`로 간주하여 계산한다. 어떻게 보면 좋기도 하지만, 엄밀히 따지면 개발자가 원하는 대로 작동하지 않았다고 볼 수도 있다.

typescript에서는 위와 같은 코드가 이런 식으로

```typescript
const a: number = 3;
const b: string = '2';
console.log(a * b); // 오류 발생
```

각 변수에 type을 명시하게 되어 있어서, number와 string을 곱셈 연산 시에 오류가 발생한다.

그리고 Typescript에는 `Class`가 존재한다.  
Javascript에도 ES6부터 Class가 생기긴 했는데, Typescript는 `protect`, `private`, `public` 과 같은 접근 지정자가 제공되기 때문에 차이가 있다.
사족인 것 같긴 한데 Typescript는 컴파일될때 ES5 문법으로 컴파일된다.

---

## SSR과 CSR

---

`SSR(Server Side Rendering)은 request마다 새로고침이 발생하며, 새로운 페이지를 불러올 때마다 전체 페이지에 대한 렌더링이 발생한다.`  
`CSR(Client Side Rendering)은 서버로부터 JSON파일만 받아, 렌더링 자체를 브라우저에서 수행하는 형태를 말한다.`

자꾸 개념이 헷갈리는데  
결과적으로 서버에 대한 요청이 `html` 그 자체로 오면 그건 `서버 사이드 렌더링`이라고 볼 수 있고,
서버에 대한 요청이 html이 아닌 JSON 데이터 등이 오면 `클라이언트 사이드 렌더링`이라고 볼 수 있음.. 이라고 일단 공부하는 중

상호작용을 통해서 새로운 html 페이지로 이동하는 서버 사이드 렌더링은 매 요청마다 전체 페이지를 다시 그려야 하기 때문에 클라이언트에 부하가 비교적 크다. 또한 서버에서도 부하가 더 크다!  
반면에 필요한 데이터만이 반환되는 클라이언트 사이드 렌더링은 모든 서비스를 위한 스크립트 파일 등을 최초 페이지 로드 시에 모두 불러오기 때문에 초기 로딩이 느린 대신에 요청에 따른 반응이 빠르다. 변화가 필요한 일부 부분만 렌더링할 수 있으니까!

`Single Page Application`이 클라이언트 사이드 렌더링 방식을 이용한 것으로, 요청에 따른 데이터로 원하는 부분만 다시 렌더링하면 되기 때문에 하나의 페이지에서 여러가지 서비스를 제공할 수 있다.

장점만 있어 보이지만, 클라이언트 사이드 렌더링은 검색 엔진 최적화(SEO)에는 좋지 못하다.  
구글과 같은 검색 엔진들은 데이터 수집을 위해서 여기저기 크롤링을 하며 데이터를 축적하는데, 클라이언트 사이드 렌더링은 서버로 요청을 보내도 html이 아닌 JSON데이터들을 반환하는 형태이기 때문에, 검색 엔진을 위한 데이터로서 사용할 수가 없다. (실제 컨텐츠에 JSON 데이터들이 포함되는지 확신할 수 없음) 자바스크립트만 실행하면 페이지를 로드할 수 있으나, 대부분의 웹 크롤러들은 자바스크립트를 실행할 수 없다.  
또한 자바스크립트를 실행한다고 해도, 페이지에서 어떤 상호작용을 해야 어떤 데이터를 가져오고 등.. 단순히 생각해도 쉽지 않아 보인다.

반면에 서버 사이드 렌더링은 매 요청이 html을 반환하기 때문에, 여기서 실제로 사용자에게 보이는 컨텐츠를 파싱하여  
그 컨텐츠들을 검색 엔진에 사용할 수 있다.

---

# require와 import의 차이

---

모듈 정의 방식의 차이, 두 키워드는 각각 **CommonJS**, **ES6**에서 사용하는 모듈을 불러오는 명령어임

우선 문법적인 차이가 있다.

## 모듈 내보내기

```javascript
// ES6

// 파일에서 모듈을 export하는 경우
const module = {};
export default module;
// default의 경우에는, "이 파일에서는 모듈이 하나만 export됩니다" 를 의미한다.

// 함수를 직접 export하는 경우
export function myFunc() {}

const myFunc = () => console.log('do something');
export { myFunc };
```

```javascript
// CommonJS

// 파일에서 module을 export하는 경우
module.exports = myModule;

// 함수를 직접 export하는 경우
exports.myFunc = function() { ... };
```

## 모듈 가져오기

```javascript
// ES6

// 모듈 전체를 import
import myModule; // as XXX 을 붙여서, 이름을 바꿔서 불러올 수도 있음

// 모듈의 모든 속성 import
import * from myModule;

// 특정 함수, 속성만 import
import { myFunc1, myFunc2 } from myModule;
```

```javascript
// CommonJS

// 모듈 전체를 import
const module = require('./myModule');

// 모듈의 모든 속성 import
/* 위의 방법으로 하면, module 밑에 모든 속성이 다 담겨서 온다 */

// 특정 함수, 속성만 import
/* 위에서 불러온 module을 사용해서 호출할 수 있음 */
module.myFunc1;
```

기본적으로 모든 언어에서 공통사항이겠지만, 저렇게 모듈을 불러올 때는 사용할 모듈만 가져오는 것이 국룰  
그래서 CommonJS의 방식이 더 안 좋아 보임, 실질적으로 메모리에 다 할당시키는지는 몰라도  
ES6의 방식은 사용하기를 원하는 것만 불러와서 사용할 수 있다. 어느 모듈의 어떤 속성을 사용할 것인지 확실히 지정할 수 있는 게 장점 아닐까?

---

# Javascript의 메모리 관리

---

[링크](https://developer.mozilla.org/ko/docs/Web/JavaScript/Memory_Management)

메모리의 생존주기, 메모리는

1. 할당
2. 사용 (읽기, 쓰기)
3. 해제

의 생존주기를 가진다. 이는 저수준/고수준 언어를 가리지 않고 공통적인 부분이다.

다만 저수준 언어에서는 **할당**과 **해제**가 소스 코드를 통혀 명시적으로 작성되는 반면, 대부분의 고수준 언어에서는 할당과 해제를 언어에서 자체적으로 해준다. Javascript는 여기서 고수준 언어인 후자에 속하며, `garbage collection` 이라는 자동 메모리 관리법을 사용한다.

자바스크립트에서는 변수를 선언할 때, 자동으로 메모리를 할당해 준다. C에서의 malloc과 같은 함수를 사용해 메모리를 할당하는 작업이 없음을 생각하면 된다.

위에서 할당된 메모리들이 적절히 사용되고 나서, `할당된 메모리가 필요없어지는 시점`을 찾아서 해당 메모리들을 해제해주면 쓸모없는 메모리 낭비를 막을 수 있다. 하지만 그 **시점**을 프로그래밍 언어에서는 정확히 알 수가 없다. 할당된 메모리를 언제 사용할 지는 전적으로 개발자의 코드에 달려 있기 때문이다. 자바스크립트가 개발자의 코드를 예측할 수는 없기 때문에.. 따라서 상기한 `garbage collection`을 통한 메모리 관리는 제한이 있다.

## Garbage Collection, 가비지 콜렉션

`가비지 콜렉션`의 알고리즘은 `참조`가 핵심 개념이다.  
A라는 메모리를 통해서, B에 접근할 수 있다면 `"B는 A에 참조된다"` 고 한다.

```javascript
const A = B;
// A 변수(메모리)를 통해서, B 변수(메모리)에 접근한다.
```

다른 예를 들면, 자바스크립트의 모든 객체는 `prototype`을 **암시적**으로 참조하고, 그 오브젝트의 속성을 **명시적**으로 참조한다.

<!-- 암시적 참조와 명시적 참조? -->

## Reference-counting (참조 세기)

이름에서 알 수 있듯이, 오브젝트가 `참조`되는 횟수를 계산하여 `다른 어떠한 오브젝트도 참조하지 않는 오브젝트`를 쓸모없는 오브젝트로 간주한다. 이 쓸모없는 오브젝트를 `가비지`라고 부르며, 이 `가비지`를 참조하는 다른 오브젝트도 없는 경우에 알고리즘에서 해당 오브젝트를 수집한다.

```javascript
var x = {
  a: {
    b: 2,
  },
};
// 2개의 오브젝트가 생성되었다. 하나의 오브젝트는 다른 오브젝트의 속성으로 참조된다.
// 나머지 하나는 'x' 변수에 할당되었다.
// 명백하게 가비지 콜렉션 수행될 메모리는 하나도 없다.

var y = x; // 'y' 변수는 위의 오브젝트를 참조하는 두 번째 변수이다.
x = 1; // 이제 'y' 변수가 위의 오브젝트를 참조하는 유일한 변수가 되었다.

var z = y.a; // 위의 오브젝트의 'a' 속성을 참조했다.
// 이제 'y.a'는 두 개의 참조를 가진다.
// 'y'가 속성으로 참조하고 'z'라는 변수가 참조한다.

y = 'yo'; // 이제 맨 처음 'y' 변수가 참조했던 오브젝트를 참조하는 오브젝트는 없다.
// (역자: 참조하는 유일한 변수였던 y에 다른 값을 대입했다)
// 이제 오브젝트에 가비지 콜렉션이 수행될 수 있을까?
// 아니다. 오브젝트의 'a' 속성이 여전히 'z' 변수에 의해 참조되므로
// 메모리를 해제할 수 없다.

z = null; // 'z' 변수에 다른 값을 할당했다.
// 이제 맨 처음 'x' 변수가 참조했던 오브젝트를 참조하는
// 다른 변수는 없으므로 가비지 콜렉션이 수행된다.
```

공식 자료 코드를 그대로 가져온 건데 주석이 매우 잘 달려 있다. 결과적으로 `a: { b:2 }` 객체가 가비지 콜렉션에 의해 수집된다.

### 한계점

Reference Counting은 명확한 한계점이 있는데, `순환 참조`가 발생하는 경우가 그렇다.

```javascript
function myFunc() {
  const obj1 = {};
  const obj2 = {};
  obj1.a = obj2;
  obj2.a = obj1;
  return 'hello world';
}

myFunc();
```

위와 같이 정의된 함수가 실행되고, 종료되면 안에 정의된 `obj1`과 `obj2` 오브젝트는 scope를 벗어나게 되므로 불필요해지며, 해제되어야 한다.  
하지만 두 객체가 서로 `참조`하고 있기 때문에 `가비지`가 될 조건을 갖추지 못 하여 Reference Counting의 알고리즘으로는 해제되지 못한다.

```javascript
var $div = document.createElement('div');
$div.onclick = function() {
  ...
  console.log(this); // $div object
}
```

위와 같은 경우에도 `참조 순환`이 발생할 수가 있다.
`$div` 오브젝트의 속성인 `onclick`을 통해 이벤트 리스너를 등록했는데, 이벤트 리스너의 콜백 함수에서 `this`를 통해 `$div`를 참조할 수 있다. `$div` 안에는 `onclick`이 있고... 반복  
인터넷 익스플로러 6, 7은 DOM 오브젝트에 Reference Counting 알고리즘으로 가비지 컬렉션을 수행했기 때문에 위와 같은 경우에서 메모리 누수가 있었다.

## Mark and Sweep (표시하고-쓸기)

공식 문서라서 그대로 따라 써보기는 하는데 정말 표시하고 쓸기라는 표현을 쓰는지는 모르겠다

이 알고리즘에서는 `닿을 수 없는 오브젝트`를 `쓸모없는 오브젝트`로 간주한다. 자바스크립트의 모든 오브젝트는 `Object`의 하위 객체이기 때문에, 최상위 객체인 이 친구를 통해서 다른 모든 객체에 접근할 수 있게 된다.

가비지 컬렉터는 `root`를 시작으로 각 객체가 참조하고 있는 객체들을 방문하며, 해당 객체들을 `mark(기억)`한다. 한 번 mark한 객체는 다시 방문하지 않도록 하며, 더 이상 `mark`할 객체가 없을 때 알고리즘은 탐색을 멈춘다. 마지막으로 `mark`되지 않은 객체들을 메모리에서 제거하면 끝

위 Reference Counting 알고리즘의 첫 번째 예시에서도, `obj1`과 `obj2`는 서로를 참조하여 참조 횟수가 0이 아니기 때문에 삭제되지 않았으나, Mark and Sweep 알고리즘에서는 `닿을 수 없는 오브젝트`이기 때문에 삭제 대상이 된다.

최근의 가비지 콜렉션 알고리즘은 대부분 이 Mark and Sweep 알고리즘을 적용하고 있다.

--

[링크](https://stackoverflow.com/questions/58100536/when-and-how-javascript-garbage-collector-works)

자바스크립트는 메모리 관리를 가비지 콜렉션에 일임하고 있기 때문에, 개발자 입장에서는 언제 가비지 콜렉션이 작동할지 알 수가 없다.  
이런 특성을 "비결정적(non-determinism)"이라고 하는데, 결과적으로는 개발자가 상상하는 시점에 가비지 콜렉션이 작동하지 않아 실질적으로 필요한 메모리보다 더 많은 메모리를 사용하는 시점이 발생하는 경우도 있다. 가비지 콜렉션은 자바스크립트의 메인 스레드에서도(?) 작동하기 때문에, 애니메이션이 있는 페이지의 경우에는 여기서 끊김 현상(Jank)이 발생할 수도 있다.

```
자바스크립트 엔진 안에, 가비지 콜렉션을 위한 다른 멀티 스레드들이 존재하지만
그럼에도 코드를 실행하는 메인 스레드("자바스크립트는 싱글 스레드" 라고 할 때의 그 스레드)에서도 사용되기도 한다.
가비지 콜렉션의 판단에 따라 어느 스레드를 이용할 지가 변하는 모양임. 메인 스레드의 실행에 따라 메모리 참조가 새로 발생할 수도 있고, 없어질 수도 있고 해서 메인 스레드가 실행되지 않을때 가비지 콜렉션을 작동시키는 의미가 있는 모양임
```

따라서 메인 스레드의 실행에 영향을 미치지 않기 위해 다음의 최적화 기법들이 적용되어 있다.

`1. Generational Collection (세대별 수집)`

- 객체를 새로운 객체와 `오래된 객체`로 나누어 분류한다. 대부분의 객체들은 생성된 후에 비교적 빠르게 사용되며 금방 쓸모없어져 `가비지`가 되는 시간이 빠른데, 이러한 객체들은 `새로운 객체`로 분류된다. 가비지 콜렉터는 이러한 `새로운 객체`들 위주로 작동하여 메모리를 제거한다. 여기서 일정 시간 이상 살아남은 객체들은 `오래된 객체`로 분류되어, 비교적 콜렉터의 감시를 덜 받게 된다.

`2. Incremental Collection (점진적 수집)`

- Mark and Sweep 방식으로 가비지를 탐색할 때, 모든 객체를 한 번에 다 방문하려고 하면 시간도 오래 걸리고, 그만큼 리소스를 많이 사용하기 때문에 메인 스레드의 실행을 방해할 수 있다. 따라서 자바스크립트 엔진은 이 Mark 작업을 여러 부분으로 분리하여 별도로 수행한다. 분리한 작업 결과를 종합하는 작업이 또 필요하겠지만 하나의 긴 지연시간을 여러 개의 짧은 지연시간으로 나누어 다른 실행에 영향을 덜 준다는 장점이 있다.

`3. Idle-time Collection (유휴 시간 수집)`

- 실행에 영향을 주지 않도록, CPU가 idle-time일 때 가비지 컬렉션을 실행하는 방법.

## 추가로 [링크](https://it-zam.tistory.com/entry/V8-Engine-garbage-collector-%EC%9D%B4%EC%95%BC%EA%B8%B0)를 통해 다른 기법들을 알 수 있음

가비지 콜렉션는 "비결정적"이긴 한데, 대부분의 경우에는 메모리 할당이 이루어지는 경우에만 콜렉터가 작동한다. 할당이 이뤄지지 않는다면 가비지 콜렉터는 유휴 상태에 있게 된다.

```
1. 사이즈가 큰 데이터 할당을 여러 번 수행
2. 할당된 결과를 가비지 컬렉터가 살펴보니, 대부분의 객체가 `접근되지 않는` 객체로 표시됨
3. 더 이상의 데이터 할당을 수행하지 않음
```

위의 시나리오에서 가비지 컬렉터들은 더 이상 데이터 수집을 진행하지 않아, `접근되지 않는` 객체들이 남아있어도 수집되지 않는다.  
이럴 경우는 엄밀히 따져 메모리 누수라고는 보지 않는다는데, 일반적인 사용량 보다 더 많은 메모리를 쓰고 있는 상태이긴 하다.

```
근데 잘 이해가 안 되는데 가비지 컬렉터에서 쓸모없다고 표시하면 바로 수집되어야 하는 거 아닌가?
아닌가보다 콜렉터가 가득 차야만 메모리 수집을 한다고 하는 게 바로 수집되지 않는 것을 의미하나보다
```

마지막으로, 자바스크립트에서 메모리 누수가 발생할 수 있는 예시들  
[링크](https://itstory.tk/entry/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%88%84%EC%88%98%EC%9D%98-4%EA%B0%80%EC%A7%80-%ED%98%95%ED%83%9C)

---

## 디자인 패턴

---

```
MVC, MVP, MVVM
```

## MVC

- Model + View + Controller
  - Model: 데이터를 처리하는 곳
  - View: UI
  - Controller: 사용자 입력(Action)을 받아 처리하는 곳
- 동작 순서
  - 사용자 Action -> Controller 작업, Model 업데이트 -> Controller가 View 선택 -> View에서 Model을 이용하여 화면 표시
  - Controller가 여러 개의 View를 선택할 수 있는 `1:N` 구조
- View와 Model간의 의존도가 크기 떄문에, 애플리케이션의 크기에 따라 복잡도가 과해질 수 있음

## MVP

- Model + View + Presenter
  - Controller 대신 Presenter가 존재함
  - Presenter: View의 요청으로 Model을 가공하여, View에 전달하는 역할 (Controller는 가공만 했었음)
- 동작 순서
  - 사용자 Action -> View가 Presenter에 데이터 요청 -> Presenter가 Model에 데이터 요청 -> Model이 데이터 반환 -> Presenter가 데이터 반환 -> View는 데이터를 받아 화면에 표시
  - Presenter와 View가 `1:1` 구조
- View와 Model의 의존성을 낮췄으나, 대신 Presenter와 View의 의존도가 커짐

## MVVM

- Model + View + View Model
  - View Model이 추가됨
  - View Model: View를 표현하기 위해 만든, View를 위한 Model. View를 나타내기 위한 Model이자, 필요한 데이터 처리를 수행하는 부분
- 동작 순서
  - 사용자 Action -> View에서 `Command 패턴`으로 View Model에 Action 전달 -> View Model이 Model로 데이터 요청 -> Model이 데이터 반환 -> View Model에서 데이터를 받아 가공하여 저장 -> View가 View Model과 `Data Binding`하여 화면 표시
  - View Model과 View가 `1:N` 구조
- View와 Model 사이의 의존성이 없으며, `Command 패턴`과 `Data Binding`을 통해 View와 View Model 사이의 의존성도 없앴음

Command 패턴?

Data Binding?

## Flux

React의 디자인 패턴, MVC 패턴의 단점을 보완하기 위한 패턴  
[링크](https://beomy.tistory.com/44)

![answers1](/assets/answers1.png)  
데이터가 항상 **Dispatcher** -> **Store** -> **View** -> **Action** -> **Dispatcher** 순으로 전달되는 단방향 데이터 흐름  
단방향 Flow가 데이터 변화를 훨씬 예측하기 쉽다.

Action

- 데이터의 상태를 변경하는 역할
- Action의 타입과, Data payload를 묶어 Dispatcher로 전달함

Dispatcher

- Action 메시지를 감지하는 순간, 콜백 함수를 통해 Store로 전달함

Store (Model)

- 앱의 상태(State)와, 상태를 변경할 수 있는 메소드를 가지고 있음
- 어떤 타입의 Action이 전달되었는지에 따라 다른 메소드를 적용하여, 상태를 변경함
- Store는 **싱글턴 패턴**을 따름 (생성자가 여러 개여도, 최초 생성되는 하나의 객체를 공통적으로 사용하게 하는 방식)

View

- React에 해당되는 부분
- Controller View가 Store에서 변경된 데이터를 가져와 자식 View에게 데이터를 전달함
- 데이터를 전달받은 View는 화면을 새로 렌더링

Redux 에는 Dispatcher 개념이 없고, `Reducer`가 Dispatcher + Store 역할을 한다.

---

## 테스트 코드 작성하기

---

테스트로 작성했던 코드 기반으로 실행할 것, 그러므로 리액트에 대한 테스트가 된다.

아니 왤케어려워

React Redux는 testing-library 쓰려는데 자료를 뭐 못 찾겠네  
store에 등록한 액션들은 어케 실행하는데? 아오

---

## 프레임워크와 라이브러리

---

`프레임워크(Framework)`는 전체적으로 틀, Frame 자체를 해당 프레임워크가 가지고 있어서, 개발자가 그 환경에 맞추어 개발을 한다.  
메뉴얼이나 룰이 존재하여 그 규칙을 따라야 한다.

`라이브러리(Library)`는 기능을 제공하는 '도구' 라고 생각하면 된다고 함

"앱의 흐름" 이 어느 쪽에서 제어되느냐로 가른다고도 한다. 프레임워크는 그것이 가진 자체적인 틀에 개발자가 맞추어 개발을 하기 때문에, Flow가 프레임워크 쪽에 있다. 반대로 라이브러리는 개발자가 마음대로 개발하다가, 필요한 경우에 라이브러리의 코드를 가져다 쓰는 형태이기 때문에 Flow가 개발자 쪽에 있다. 는 개념으로 생각하면 될 듯함

---

## JSON Web Token

---

[링크](https://bcho.tistory.com/999), [링크2](https://velopert.com/2389)

웹에서 "로그인" 기능을 구현한다고 생각하면 가장 먼저 떠오르는 개념 쿠키, 세션

쿠키의 경우에는 데이터를 로컬에 저장하기 떄문에 보안 문제로 세션을 사용하는 추세였음, 하지만 세션에도 문제점은 존재했으니

1. 만일 서버가 여러 개로 세분화된다면, 각 서버가 다른 세션을 저장하기 때문에 데이터에 일관성이 부족해진다.
2. 1의 문제를 해결하기 위해, 세션을 전용 서버로 몰아서 저장한다고 치면, 모든 요청을 해당 서버 하나에서 감당해야 하기 때문에 부하가 크다.
3. 그리고 기존의 웹 이용 환경이 데스크탑의 웹 브라우저만 존재했지만 요즘에는 모바일로 사용하는 비중이 상당히 크기 때문에 웹/앱의 쿠키-세션 처리 방법에 따라 다른 로직을 작성해야 한다.

위의 문제들을 해결하기 위해 `Token`방식이 도입되었으며, 토큰 자체에 정보를 담고 있어서 별도의 인증 로직이 필요없어서 2번의 전용 서버가 필요가 없어진다. 또한 JSON방식의 통신이기 때문에 어떤 Client든 상관없이 Token을 이용할 수 있다.

OAuth 방식의 경우에는, 서버에 토큰을 요청하면 서버가 토큰을 생성하며 사용자에게 반환하고, 서버에 저장하기까지 했는데  
JWT와 같은 Claim 기반은 서버에 토큰을 저장하지 않고, 토큰 데이터를 함께 담아 사용자에게 반환한다.

JWT는 토큰 속성 정보(Claim)를 JSON 형태로 갖는데, 개행문자가 표현된 이런 데이터들은 헤더에 담기에 적합하지 않기 때문에 이 JSON 데이터를 base64 인코딩하여 사용한다.

토큰을 누가 가로채서 변조해서 사용할 수가 있기 때문에, "서명" 개념의 추가 데이터가 토큰에 따라붙게 되는데 이 서명의 방식이 다양하기 때문에 어떤 서명 방식을 사용했는지가 토큰의 앞쪽에 붙는다. 서명에는 서버에서 가지고 있는 secret key가 이용되기 때문에 변조를 탐지할 수가 있다. 최종적으로는 다음과 같은 형태를 갖는다.

```
{서명 방식을 정의한 JSON을 BASE64 인코딩}.{JSON Claim을 BASE64 인코딩}.{JSON Claim에 대한 서명}
```

[링크](https://lazyhoneyant.tistory.com/7)

JWT의 경우에도 결국 Claim에 대한 부분은 암호화가 되지 않기 때문에 비밀 데이터들을 전달하기에는 마냥 좋지는 않다.

그리고 이 JWT의 경우에도 클라이언트에서 지속적으로 서버에 전송하기 위해서는 쿠키 or 세션을 선택해야 하는데, 여기서 어떤 것을 선택할지도 고민할 문제임

세션의 경우에는 자바스크립트 코드로 제어하기 때문에 그 부분을 보완해야 하고 (XSS 공격),  
쿠키의 경우에는 CSRF 공격에 취약하기 때문에 그 부분을 보완해야 한다.

XSS 공격이 우회하는 방법이 더 다양해서 보통은 쿠키를 사용한다고 함  
CSRF공격은 단순하게 말하면 다른 도메인에서 쿠키를 도용해서 쓰는건데, 서버에서 비교적 단순한 방법으로 처리가 가능하다고 함

---

## Execution Context

---

[링크](https://velog.io/@tmmoond8/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C-%EA%B0%9C%EB%B0%9C%EC%9E%90-%EC%9D%B8%ED%84%B0%EB%B7%B0-%ED%9B%84%EA%B8%B0-%EB%A9%B4%EC%A0%91-%EC%A7%88%EB%AC%B8-%EC%A0%95%EB%A6%AC-%EC%9E%91%EC%84%B1-%EC%A4%91#5-%EC%8B%A4%ED%96%89%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8)

```
Execution Context(실행 컨텍스트)는 실행 가능한 코드가 실행되기 위해 필요한 환경
```

위에서 말하는 실행 가능한 코드란 ..

- 전역 코드: 전역 영역에 존재하는 코드
- Eval 코드: eval 함수로 실행되는 코드
- 함수 코드: 함수 내에 존재하는 코드

이런 것들이 있다.

기본적으로, 브라우저가 스크립트를 실행한다고 치면 제일 먼저 **전역 컨텍스트** 가 생성된다.

---

## Method Chaining

---

```
자기 자신을 반환하면서 다른 함수를 지속적으로 호출하는 릴레이 방식의 프로그래밍 패턴
```

```javascript
const MySquare = function () {
  this.width = 0;
  this.height = 0;
};

MySquare.prototype.setWidth = function (newWidth) {
  this.width = newWidth;
  return this;
  // 객체 자기 자신을 반환
};

MySquare.prototype.setHeight = function (newHeight) {
  this.height = newHeight;
  return this;
};

MySquare.prototype.getSize = function () {
  return this.height * this.width;
};

const square = new MySquare();
const result = square.setWidth(10).setHeight(5).getSize(); // 메소드를 연속적으로 호출

console.log(result);
```

---

## 적응형 vs 반응형

---

적응형 웹은 실행 환경(모바일, 데스크탑, 태블릿 등) 에 따라 각각의 템플릿을 만들어 적용하는 방식

반응형 웹은 하나의 템플릿이 여러 실행 환경에 적용되는 방식

www.naver.com 이 데스크탑 페이지라면, 모바일은 m.naver.com 로 접속되는데 이 경우에는 다른 템플릿이 적용되는 경우로 `적응형`에 속한다.

하나의 페이지에서 화면 크기를 조절하는 것으로 레이아웃으 변경되는 홈페이지들은 `반응형`에 속한다.

유튜브의 경우에는 데스크탑 페이지에서도 어느정도 반응형을 지원하는데? 모바일 페이지도 따로 존재하기는 한다.

---

## 함수형 프로그래밍과 객체지향 프로그래밍

## Funcional Programming, Object Oriented Programming

[링크](https://kyung-a.tistory.com/3)

[함수형 프로그래밍](https://velog.io/@kyusung/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%9A%94%EC%95%BD)

함수형 프로그래밍은 `사이드 이펙트가 없는 순수 함수와, 동작의 결과를 강조` 하는 프로그래밍 방식

순수함수는 동일한 인자가 들어가면, 항상 동일한 출력값을 반환하는 "외부의 영향을 받지 않는" 함수를 말하며, 안전성이 보장된다고 할 수 있다.

함수형 프로그래밍의 장점

- 사이드이펙트를 걱정하지 않아도 됨
- 객체지향에 비해 코드가 간결함
- 비 절차형(?)이라 평가 시점(연산 시점)이 중요하지 않음
- 테스트가 쉬움 (순수함수이므로 1회 실행만으로 신뢰성이 보장됨)

단점

- 외부 데이터나, 내부 데이터를 조작할 수 없는 방식임

함수형 프로그래밍과 객체지향 프로그래밍의 차이

- 함수형의 경우
  - 값의 연산 및 결과 도출 중심으로 코드 작성이 이루어짐
  - 함수가 인자로 받은 값을 저장하거나 하지 않고, 간결한 과정으로 처리하는 데 주 목적을 둠
- 객체지향의 경우
  - 클래스 디자인과, 객체들의 관계를 중심으로 코드 작성이 이루어짐
  - 상태, 멤버 변수, 메소드 등이 긴밀한 관계를 가짐
  - 멤버 변수의 상태에 따라 프로그램의 결과가 달라짐

객체지향은 `객체 중심`의 프로그래밍 방식  
기존 절차지향이 위에서 아래 순서대로 명령어가 수행되었다면, 객체지향은 객체라 불리는 요소(집합)의 구성으로 명령어를 수행한다.  
각 객체 간의 관계를 통해 코드 호출, 수행 순서를 유기적으로 조정할 수 있어 `코드의 간결함, 신뢰성, 재사용성`에 큰 강점을 가진다.

객체지향의 대표적 특징

- 상속성
  - 상위 부모 객체의 특성을, 하위 객체가 물려받는 것
- 은닉성
  - 객체의 멤버 변수를 외부에서 접근할 수 없도록 캡슐화할 수 있음 (다른 언어의 private, public, protected 키워드 등으로)
  - Javascript에서는 접근 지정자를 공식적으로 지원하지 않기 때문에, 멤벼 변수 앞에 `_` 를 붙이는 것으로 private, protected 을 표시한다.
  - 혹은 클로저를 이용하면 private 멤버변수를 구현할 수 있다.
- 다형성
  - 같은 타입이지만 실행 결과가 다양한 객체를 대입할 수 있는 성질
  - 객체가 지닌 함수를 다른 스코프에서 재정의하여 덮어씌울 수 있다. (오버라이드)

[예제와 같이 보는 함수형 프로그래밍](https://medium.com/@mr.november11/%EB%B2%88%EC%97%AD-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%98%88%EC%A0%9C%EB%A1%9C-%EB%B0%B0%EC%9A%B0%EB%8A%94-%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-ec0a95dc49d1)

---

## React Hook

---

```
Hook이 16.8버전에 새로 추가되었습니다. Hook을 이용하여 Class를 작성할 필요 없이 상태 값과 여러 React의 기능을 사용할 수 있습니다.
```

`Hook은 계층 변화 없이 상태 관련 로직을 재사용할 수 있도록 도와줍니다.`

## # State Hook

React의 컴포넌트는  
React.Component 를 상속받는 Class를 만들어 사용하는 클래스형 컴포넌트  
함수를 선언하여 사용하는 함수형 컴포넌트 두 종류가 존재하는데

클래스형 컴포넌트에서는 Component 클래스를 상속받아 사용하기 떄문에 `state`에 따른 lifecycle을 이용할 수 있었으나 함수형은 그렇지 못했다. 하지만 함수형 컴포넌트에서도 `state`와 그 lifecycle을 이용할 수 있게 한 것이 `Hook`.

직접 코드로 비교하면

```js
// 클래스형
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
}
```

```js
// 함수형
import React, { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);
  // return [state 변수, state 값을 변경하는 함수]
  // 넘겨주는 인자는 state의 초기값
}
```

두 코드가 같은 기능을 한다.

`useState`로 가져온 **state값을 변경하는 함수**(예시의 `setCount`)는 `this.setState`와는 다르게  
state를 변경할 때 "병합" 하는 방식이 아니라 "대체" 하는 방식이기 때문에, state에 객체를 저장하여 그 객체의 각각을 갱신하려고 할 때 주의해야 함

```js
const [state, setState] = useState({
  left: 0,
  top: 0,
  width: 100,
  height: 100,
});
// 위와 같이 state를 선언했다고 치면

setState((state) => ({ ...state, left: e.pageX, top: e.pageY }));
// 위와 같이 스프레드 연산자를 사용하여 width, height 값이 손실되지 않게 해야 한다.
```

말했듯이 대체하는 방식이기 때문에, `...state` 를 넣지 않으면 `state` 에는 `left`와 `top` 만 남게 된다.  
스프레드 연산자로 해결이 가능하긴 하지만, 애당초 state를 선언할 때 값이 같이 변하는 변수끼리만 묶도록 권장한다.

## # Effect Hook

`Effect Hook을 사용하면 함수 컴포넌트에서 side effect를 수행할 수 있습니다.`

데이터 가져오기, 구독 설정하기, 수동으로 컴포넌트 DOM 수정하기 등이 모두 `side effects`

공식 홈페이지에서는 `componentDidMount`, `componentDidUpdate`, `componentWillUnmount` 를 합친 것이 `useEffect` Hook이라고 생각해도 된다고 함, 컴포넌트가 렌더링 될 때마다 특정 작업을 실행하게 하는 Hook이라고 보면 된다.

```js
function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;

    return () => {
      // clean-up, effect 에 대한 뒷정리를 하는 부분
      console.log(`previous count is ${count}`);
    };
  });
}
```

`useEffect`가 인자로 갖는 함수 안의 내용은, React의 lifecycle에 따라 `count` state 값이 변경될 때마다 실행된다. useEffect에서 반환되는 익명 함수는 effect에 대한 clean-up을 수행하는 부분으로, state가 변할 시에 이전 state에 대한 로직을 작성할 수 있다.

clean-up 부분은 컴포넌트가 최초 로드될 때는 실행되지 않고, 로드 이후 state가 처음으로 업데이트될 때 최초로 실행된다.

useEffect는, 함수 말고도 다른 하나의 인자를 더 받을 수 있는데, `deps(dependancyList인듯)` 라고 부르며 사용은

```js
useEffect(() => {
  console.log(`watching number: ${number}`);
}, [number]);
```

이렇게 한다. `deps` 인자에 배열 형태로 값을 넣으면, 해당 값의 변화가 있을 때에만 `effect`를 실행하고  
값의 변화가 없으면 effect를 건너뛴다. 만약 처음의 예시처럼 `deps`를 생략한다면, 아무 state나 변해도 effect가 재실행된다.

Redux에서도 이를 지원하여, `useSelector`, `useDispatch` 로 전역 store를 Hook과 함께 사용할 수 있다.

\*\* Class형 컴포넌트는, props를 재활용하는 특성 때문에 프로그램에서 예상치 못한 결과가 나올 수 있음, 그 예시는 [여기](https://boxfoxs.tistory.com/395) 에 3초 딜레이 거는 걸로 잘 나와 있음

\*\* useRef 함수, DOM ref을 위한 변수로도 만들 수는 있는데, class의 인스턴스 property와 유사하다고 함.

공식 문서에서도, 함수형 컴포넌트와 함께 Hook 을 사용하기를 권하고 있고, 메모리 사용량도 함수형이 더 적다고 한다. (클래스형 컴포넌트는 React의 Component 클래스를 상속받으면서 그런 문제가 생긴다는 듯?)  
Hook을 지원하지 않던 때에는 클래스형과 함수형의 차이가 매우 컸으나, 이제는 함수형 컴포넌트를 주로 사용하는 모양

---

## Context API

---

```
context는 React 컴포넌트 트링 안에서 전역적이라고 볼 수 있는 데이터를 공유할 수 있도록 고안된 방법입니다.
그러한 데이터로는 "현재 로그인한 유저", "테마", "선호 언어" 등이 있습니다.
```

"테마" 의 경우에 props를 사용해서 각 컴포넌트에 전달한다 치면, 상위 컴포넌트에서 맨 마지막 컴포넌트까지 같은 props를 반복해서 전달해야 하기 때문에, 상당히 번거로운 방법이 된다. 여기서 `context`를 사용하면 중간에 있는 엘리먼트들에 따로따로 props를 넘겨주지 않아도 된다.

```js
const ThemeContext = React.createContext('light');

function App() {
  return (
    <div>
      <Toolbar />
    </div>
  );
}

function Toolbar() {
  return (
    <div>
      <ThemeButton />
    </div>
  );
}

function ThemeButton() {
  const themeContext = React.useContext(ThemeContext);

  return (
    <span>{themeContext}</span> // light
  );
}
```

함수형 컴포넌트에서는 위와 같이 `useContext`함수를 통해 Context를 가져와서 사용할 수 있다. 그래서 Context들을 정의해 놓는 디렉토리를 따로 만들어서, 거기서 import해 사용하는 방식도 가능하다.

클래스형 컴포넌트에서는,

```jsx
return (
  <ThemeContext.Provider value={defaultValue}>
    ...
  </ThemeContext.Provder>
);
```

이런 식으로 상위 컴포넌트에서 Provider를 사용하면, 하위 컴포넌트 전체에 값이 전파된다. 물론 사용하는 방식은 다르나 여기선 함수형 컴포넌트에 대해서만 기록함

---

## Context API와 Redux의 비교

---

전역 상태 관리를 위한 도구라는 점에서는 동일함

하지만 Redux가 상태 관리 이외의 기능들을 좀 더 많이 제공한다.

- 로컬 스토리지에 상태를 영속적으로 저장하고, 시작할 때 다시 불러오는 데 뛰어남
- 상태를 서버에서 미리 채워서 HTML에 담아 클라이언트로 보내고, 앱을 시작할 때 다시 불러오는 데 특히 뛰어남
- 사용자의 액션을 직렬화하여 상태와 함꼐 버그 리포트에 첨부할 수 있고, 개발자들이 이를 통해 에러를 재현할 수 있음
- 액션 객체를 네트워크를 통해 보내면 코드를 크게 바꾸지 않아도 협업 환경을 구현할 수 있음
- 실행취소 내역의 관리나, 낙관적인 변경(optimistic mutations)을 코드를 크게 바꾸지 않고도 구현할 수 있음
- 개발할 때 상태 내역 사이를 오가고, 액션 내역에서 현재 상태를 다시 계산하는 일을 TDD 스타일로 할 수 있음
- 개발자 도구에게 완전한 조사와 제어를 가능하게 해서, 개발자들이 자신의 앱을 위한 도구를 직접 만들 수 있게 해줌
- 비즈니스 로직 대부분을 재사용하면서 UI를 변경할 수 있게 해줌

아직 하나도 와닿는 게 없다...  
그리고 Context API를 사용하면 High-Frequency한 어플리케이션에서는 성능 이슈가 발생할 수 있다고 한다. 이것도 지금은 잘 모르겠다...

[코드와 함께하는 비교](https://egg-programmer.tistory.com/281)

---

## Callback 지옥 탈출하기

---

예제로 보기

```js
// 콜백 지옥
setTimeout(
  (name) => {
    let coffeeList = name;
    console.log(coffeeList);

    setTimeout(
      (name) => {
        coffeeList += ', ' + name;
        console.log(coffeeList);

        setTimeout(
          (name) => {
            coffeeList += ', ' + name;
            console.log(coffeeList);

            setTimeout(
              (name) => {
                coffeeList += ', ' + name;
                console.log(coffeeList);
              },
              500,
              'Latte'
            );
          },
          500,
          'Mocha'
        );
      },
      500,
      'Americano'
    );
  },
  500,
  'Espresso'
);
```

```js
// Promise 사용으로 탈출하기
function addCoffee(newCoffee) {
  return (prevCoffee) => {
    return new Promise((resolve) => {
      setTimeout(() => {
        prevCoffee.push(newCoffee);
        console.log(prevCoffee);
        resolve(prevCoffee);
      }, 500);
    });
  };
}

addCoffee('아메리카노')([]).then(addCoffee('라떼')).then(addCoffee('믹스커피'));
```

예시가 극단적이지 않나 싶기도 한데 이렇게 하면 탈출 가능하다

---

## Call By Value, Call By Ref

---

```js
let a = 1;
const fun = function (b) {
  b = b + 1;
};

fun(a);
console.log(a); // 1
```

자바스크립트는 기본적으로 원시값을 넘기면 `Call By Value` 로 작동한다.

```js
let a = { num: 10 };
const fun = function (b) {
  b.num = 5;
};

fun(a);
console.log(a); // { num: 5 }
```

~~원시값이 아닌 객체가 전달되어 `Call By Reference`로 작동했다.~~

자바스크립트는 기본적으로 `call by value`로만 작동한다.  
다만 객체의 경우에는, 값을 직접 저장하는 것이 아니라 값이 저장된 `주소` 를 저장하기 때문에, call by value로 호출되어도, 복사본인 객체가 실질적으로 같은 값(주소)를 가리키기 때문에  
호출된 객체의 내부 값을 변경시키면 실제 객체의 데이터도 변하게 된다.

결과적으로 주소값만 기억하니까, 객체의 경우에도 결국 객체 자체를 다른 값으로 대체해버리려고 하거나  
그런 건 되지 않는다.

```js
const a = { num: 10 };
const fun = function (b) {
  b = { num: 88 };
};

fun(a);
console.log(a); // { num: 10 }
```

---

## Jest와 Enzyme으로 테스트 작성하기

---

React 17버전의 컴포넌트는 Enzyme의 `mount()`함수를 사용할 수가 없어서(버전 문제라는듯)  
부득이하게 React 16.14.0 버전을 사용했다.

필요한 패키지 설치

```
npm install enzyme enzyme-adapter-react-16
```

---

## XSS, CSRF

---

```
XSS (Cross-Site Scripting)
```

공격 대상인 사이트에, 스크립트를 넣어 실행시키는 것

input 입력 태그에 이름을 입력하고 버튼을 누르면, **Hello {이름}** 을 표시하는 페이지가 있다고 가정하자.

input의 입력 태그에 `<script>alert(123)</script>` 같은 값을 입력하고 버튼을 눌었을 때, 저 스크립트 태그가 html에 넣어지면서 태그 안의 스크립트가 실행될 수도 있다.

이런 식으로 홈페이지에서 의도하지 않은 방식으로 자바스크립트 코드를 실행시키는 것을 XSS라고 부른다.

```
1. 허용된 위치가 아닌 곳에 신뢰할 수 없는 데이터가 들어가는 것을 허용하지 않는다.
2. 신뢰할 수 없는 데이터는 검증을 하여라.
3. HTML 속성에 신뢰할 수 없는 데이터가 들어갈 수 없도록 하여라.
4. 자바스크립트에 신뢰할 수 없는 값이 들어갈 수 없도록 하여라.
5. CSS의 모든 신뢰할 수 없는 값에 대해서 검증하여라.
6. URL 파라미터에 신뢰할 수 없는 값이 있는지 검증하여라.
7. HTML 코드를 전체적으로 한번 더 검증하여라.
```

```
CSRF (Cross-Site Request Forgery)
```

XSS와 마찬가지로, 의도치 않은 작업을 실행시킨다는 의미에서는 같지만. XSS는 자바스크립트를 실행시키는 것이고

CSRF는 서버로 특정 요청을 보내는 행위 등을 일컫는다.

대표적으로, `img` 태그로 이미지를 불러올 때 `GET`을 사용하는 것을 이용하여,  
게시글 작성 시에 `img`태그의 이미지 src에 특정 서버의 API 주소를 입력해서  
다른 사용자들이 게시글을 열람할 때 해당 사용자의 권한으로 서버에 요청을 보내게 하는 방식이 있다.

게시글 열람자가 자체적으로 해당 서버 요청을 실행한 것이 되어서, 다른 사용자의 Session등을 탈취당할 수도 있고,  
만약 열람자가 관리자였다면 관리자 계정 권한으로 특정 작업을 하게 될 수도 있다.

`XSS는 공격대상이 클라이언트, CSRF는 공격대상이 서버`

---

## ES7+ 몇 가지 기능들

---

### ES7

- `.includes(value)` 함수
  - string, array에 사용 가능하며 인자로 오는 값(value)이 string, array에 포함되었는지 확인 가능
- 지수표현
  - 3\*\*3 = 27

### ES8

- `.padStart(number)`, `.padEnd(number)`
  - 문자열에 사용할 수 있는 함수
  - 문자열의 길이를 `number`만큼 늘리는데, number가 문자열의 길이보다 크면 해당 공간을 공백으로 채운다.
  - padStart는 앞쪽을 공백으로 채우고, padEnd는 반대로 뒤쪽을 채운다.
  ```javascript
  'ginc'.padStart(6); // '  ginc'
  'ginc'.padEnd(6); // 'ginc  '
  ```
- `Object.values(object), Object.entries(object)`

  - 오브젝트의 원소들을 배열로 접근할 수 있게 함

  ```javascript
  const obj = {
    id: '아이디',
    email: '메일',
  };

  Object.values(obj); // ['아이디', '이메일']
  Object.entries(obj); // [['id', '아이디'], ['email', '이메일']]
  ```

  `.forEach`나, `.map` 함수와 연계해서 사용할 수 있다.

### ES10

- `.flat()`

  - 다차원 배열을 평탄하게(?) 만드는 함수
  - 배열의 빈 공간을 정리할 수도 있음

  ```javascript
  const arr = [1, 2, , [3, 4, , [5, ,]]];

  arr.flat(); // [1, 2, 3, 4, [5, , ]];
  arr.flat(1); // [1, 2, 3, 4, [5, , ]];
  arr.flat(2); // [1, 2, 3, 4, 5];

  // 여기서 [5, , ] 는 [5, empty] 입니다. 길이가 3이 아님!
  ```

- `.trimStart()`, `.trimEnd()`
  - 공백 없애기
- `Object.fromEntries(array)`

  - key-value 쌍의 배열을 object로 변환한다.
  - `Object.entries()` 함수의 반대기능이라 보면 됨

  ```javascript
  const obj = [
    ['a', 1],
    ['b', 23],
  ];

  Object.fromEntries(obj); // { a: 1, b: 23 }
  ```

- `Infinity`
  - 무한대 숫자를 표현함
  - `Infinity - 1 === Infinity`
- `for of` 반복문
  - array와, string 같은 iterable에 사용 가능
  ```javascript
  for (each of 'string') {
    console.log(each); // s t r i n g, 문자 하나씩 출력됨
  }
  ```
- `for in` 반복문

  - object와 같은 enumerable에 사용하여, property를 볼 수 있음

  ```javascript
  const obj = { a: 1, b: 'asd', c: 100 };

  for (each in obj) {
    console.log(each); // a b c, 객체의 key값만 가져옴
  }
  ```

  - array와 string에 `for in`을 사용하면, 인덱스가 반환된다. (0 ~ )
    내부적으로 index를 property로 갖는 object구조이기 때문

## ES2020

- `bigInt`
  - 새로 추가된 숫자 Type
  - 숫자 뒤에 n을 붙여, `Number.MAX_SAFE_INTEGER` 를 초과하는 값을 표현할 수 있다.
- Optional Chaining Operator

  - object에 property가 존재하는지 판별하는 키워드
  - property 뒤에 물음표(?)를 붙여 사용할 수 있음

  ```javascript
  const obj = {
    students: {
      david: {
        id: 1,
        phone: '1234',
      },
    },
  };

  obj.students.jason.id; // TypeError: Cannot read property 'id' of undefined
  obj.students.jason?.id; // undefined
  ```

  특정 상황에서 에러 방지하기 위해 사용할 수 있겠다.

- Nullish Coalescing Operator

  - 값이 `null`이거나, `undefined`인지 판단할 수 있음
  - 값 뒤에 `??` 을 붙여 쓸 수 있다.

  ```javascript
  const nullValue = null;
  const undefinedValue = undefined;
  const normalValue = 123;

  console.log(nullValue ?? 'it is null'); // it is null
  console.log(undefinedValue ?? 'it is undefined'); // it is undefined
  console.log(normalValue ?? 'it is not null/undefined'); // 123[
  ```

---

## 스코프 체인이란

---

스코프, 범위  
변수나 함수에 접근할 수 있는 범위를 말한다.

자바스크립트의 스코프에는 두 종류가 있다. 전역 스코프와 지역 스코프

자바스크립트는 함수를 선언할 때마다 새로운 지역 스코프를 생성하는데, 그래서 기본적으로 이 스코프 안에서 변수나 함수를 우선적으로 찾는다.  
또한 블럭 레벨 스코프가 아니라 기본적으로 함수 레벨 스코프이다. 다만 `let, const`를 이용하면 블록 단위로 변수, 함수의 스코프를 제한할 수 있다.

함수 레벨이건, 블록 레벨이건 스코프가 생성되며 상위/하위 개념을 갖게 되는데, 이렇게 중첩된 스코프들은 연결리스트로 저장된다.  
따라서 하위 스코프에서 연결리스트를 통해 상위 스코프의 변수나 함수에 접근될 수 있다.

```js
function foo() {
  var a = 100;
  function bar() {
    console.log(a); // 100
  }
}

foo();
```

`bar`함수에는 a 변수가 없으나, 스코프 체인을 통해 상위 스코프인 `foo`함수의 스코프에 접근하여, a 변수의 값을 100으로 출력할 수 있다.  
이런 식으로 상위 스코프에 접근할 수 있으며, 최상단 스코프까지 탐색해서 찾으려는 변수가 없으면 undefined가 나온다.  
없는 함수를 실행하려고 한 경우에는 typeError가 발생한다. (not a function)

이렇게 스코프가 연결되어 참조할 수 있는 개념을 스코프 체인이라 한다.

---

## 렉시컬 스코프

---

함수의 스코프가, 함수 "선언"시에 결정되는 개념을 "렉시컬 스코프"라고 하고,

반대 개념으로는 함수가 "실행"될 때 스코프가 정해지는 "다이나믹 스코프"가 있다.

자바스크립트를 포함해서 대부분의 언어가 "렉시컬 스코프"를 따르고 있다.

```js
var x = 1;
function foo() {
  var x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // 1
bar(); // 1
```

렉시컬 스코프를 따르기 때문에, `bar`는 실행될 때의 x값(10)을 사용하지 않고  
전역에 선언될 때의 x값(1)을 출력하게 된다.

---

## JSX의 Key

---

React에서, 반복문을 통해 element를 렌더링하는 경우가 있으면, element에 `key`값을 할당하라는 안내가 있다. (IDE등에서 알려줌)

이 Key는, 렌더링 시의 최적화를 위한 고유값인데

이 Key값이 유지되면, 반복문을 통한 렌더링 결과물이 순서만 바뀌거나, 동일한 데이터를 가지고 있는 경우를 최적화할 수 있다.  
(key값이 같으면, 이전에 렌더링된 결과를 그대로 사용함)

따라서 이 Key값은 element에 담길 데이터와 1:1로 대응하는 고유한 값이여야 하며, 배열의 index같은 값을 Key에 입력하면  
배열 안의 순서만 변하는 경우에도 element의 데이터가 그대로임에도 전체 다 렌더링을 새로 하게 된다.

---

## Virtual DOM의 이점

---

```js
var numbers = [1, 2, 3, 4];

for (var i = 0; i < numbers.length; i++) {
  var $number = document.createElement('div');
  $number.innerText = numbers[i];

  document.body.appendChild($number);
}
```

이런 식으로 반복문을 이용하여 element를 추가한다고 했을 때, virtual DOM을 사용하지 않고 직접 DOM에 element를 추가하게 되면  
각 `$number`가 추가될 때마다 repaint나, reflow가 발생하기 때문에 효율이 좋지 못하다.

Virtual DOM의 경우에는, 변경 사항을 모두 적용한 최종 결과를 DOM과 비교하여 적용하기 때문에, element 추가되는 과정에서 불필요하게 반복되는 reflow나, repaint를 줄일 수 있다.

---

## 마이크로태스크와 매크로태스크

---

이벤트 루프에서 연계되는 개념

자바스크립트 엔진은 작업을 기다리고, 새로운 작업이 도착하면 실행하고, 다시 기다리기를 반복함  
이러한 반복되는 과정이 이벤트 루프(간단하게)

여기서 자바스크립트 엔진을 활성화시키는 작업에는

- 이벤트가 발생하여, 이벤트리스너를 실행하는 경우
- setTimeout에서 설정한 시간이 다 되어, 콜백 함수를 실행하는 경우
- `<script src="..."></script>` 같은 태그로 외부 스크립트를 새로 실행하는 경우
  등을 예시로 들 수 있다.

다만 이런 작업이 한 번에 여러 개가 요청될 수 있는데, 엔진은 요청된 작업들을 "매크로태스크 큐"에 저장하고, 순서대로 작업들을 실행한다.

작업이 수행 중일 때, 다른 작업은 여전히 대기 상태가 되며 이는 렌더링 작업도 해당된다.  
따라서 하나의 작업이 너무 긴 시간이 소요되면, 그만큼 브라우저의 렌더링이 지연되어 "응답 없음" 등의 상태가 되기도 한다.  
그리고 이렇게 "응답 없음" 상태가 되면, 브라우저가 그 동안 발생하는 새로운 이벤트를 마찬가지로 처리할 수 없게 된다.

위에서 말한 경우에 생성되는 "매크로태스크" 외에 "마이크로태스크"가 있는데  
이건 코드를 이용해서만 생성되고, 주로 Promise에 의해 생성된다.

Promise객체에 붙는 `then/catch/finally` 핸들러가 마이크로태스크가 되고  
이 Promise 객체를 제어하는 `await`를 통해서도 마이크로태스크가 생성된다.

**마이크로태스크**는, **매크로태스크**나 렌더링이 실행되기 전에 먼저 실행된다.  
그 이유는, 마이크로태스크의 실행 환경을 동일하게 보장하기 위해서다.

위에 썼듯이 매크로태스크에는 이벤트 발생 등의 작업이 포함되어 있어서, 코드 실행 환경이 변화될 수 있다.  
따라서 마이크로태스크의 실행 환경을 보장하기 위해, 두 작업을 별도로 분리하여 순서대로 실행한다.

```js
setTimeout(() => console.log('setTimeout'), 0);

Promise.resolve().then(() => console.log('Promise'));

console.log('normal code');
```

위 코드의 실행 결과가 어떻게 나올까?  
정답은 `normal code -> Promise -> setTimeout`

스크립트를 실행하면, setTimeout과 Promise는 비동기 함수로서 Web API에 전달된다.

그러면 콜 스택에 남는 `console.log('normal code')`가 가장 먼저 실행되고,  
Web API에 전달된 두 작업 중에서 **마이크로태스크**인 `Promise`의 콜백 then()함수가 먼저 실행,  
모든 마이크로태스크가 실행되었으니 마지막으로 **매크로태스크**인 `setTimeout`의 콜백 함수가 실행된다.

![event loop](./assets/event-loop.gif)
Image from [Here](https://velog.io/@yejineee/%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84%EC%99%80-%ED%83%9C%EC%8A%A4%ED%81%AC-%ED%81%90-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C-%ED%83%9C%EC%8A%A4%ED%81%AC-%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%83%9C%EC%8A%A4%ED%81%AC)

---

## 자바스크립트의 데이터 타입

---

자바스크립트의 변수 타입에는 두 가지가 있다.

**기본형(Primitive Type)**과 **참조형(Reference Type)**

기본적으로, 기본형은 불변성을 가지며, 참조형은 반대로 가변성을 갖는다

기본형에는 `Number`, `String`, `Boolean`, `null`, `undefined`, `Symbol` 6 가지가 있고

참조형은 `Object` (Array, Map, Function, Date 등)가 있다.

## 데이터 할당

변수가 생성될 때, 메모리 공간을 할당하여 그 안에 변수 이름에 해당되는 **식별자**를 입력하고, 그 변수에 저장될 값을 저장하는 과정이 진행된다.

그런데 이 변수의 "값"을 식별자가 할당된 메모리(`$0`)에 직접 입력하지 않고, "값"을 저장하기 위한 메모리(`$1`)를 따로 할당한 다음에 `$1`의 주소값을 `$0` 에 입력한다.

여기서 식별자가 저장되는 메모리를 **변수 영역**,
"값"이 저장되는 메모리를 **데이터 영역**이라고 말하겠음
(개념적인 구분을 위한 것이고 실제로 구분되는 것은 아닌듯함)

## 변수 영역과 데이터 영역 분리의 이점

이런 방식으로 식별자와 데이터를 따로 저장하는 방식에 무슨 이점이 있을까?

String 변수를 초기화하고, 값을 변경하는 경우를 예로 들어보자

8바이트로 크기 제한이 있는 Number와 달리 String은 메모리 할당 제한이 없기 때문에, 긴 문자열을 입력할수록 할당해줄 메모리의 크기가 늘어나게 된다.

변수 영역에 값을 직접 저장하면 나중에 값이 변경되어 추가로 메모리 할당이 필요할 경우가 생길 때 "사용 중인 메모리의 할당을 늘리는" 작업이 필요하다. 단순히 메모리 끝 부분에서 추가 공간을 할당하면 될 수도 있지만, 현재 메모리의 주변 공간이 다른 변수에 의해 사용중일 수도 있다.

그런 경우에는 그 "다른 변수" 들의 공간을 이동시키는 등의 작업을 해서라도 메모리 할당을 해 줘야 하기 때문에 불필요하게 처리해야 할 연산이 많아지게 된다.

변수 영역에 주소값을 저장하게 되면, 다른 여유 공간의 메모리에 새로운 값을 입력하고 그 주소값만 가져와 식별자와 연결시키면 되기 때문에, 메모리 할당이 가변형인 데이터에 대해서 추가 불필요 연산을 막을 수가 있다.

식별자에 값을 직접 입력하지 않는 대신, 주소를 입력하는 것으로 위의 경우에서 추가 연산을 방지할 수 있었는데, 이런 방식은 중복 데이터를 저장하는 경우에도 효율적인 면이 있다.

자바스크립트에서 변수를 선언하고, **변수 영역**에 식별자를 입력하고, 값을 입력하기 위한 **데이터 영역**에 메모리를 할당할 때, 같은 값이 이미 **데이터 영역**에 저장되어있는지 검색을 한다.

같은 값이 데이터 영역에 이미 저장되어 있다면, 새로 메모리를 할당하지 않고 주소값을 가져다 변수 공간에 입력한다.

## 기본형과 참조형

변수 영역과 데이터 영역이 분리되어, 기본형 데이터의 값을 변경할 때는 기존에 입력한 값을 변경시키지 않는다. 그래서 기본형 변수는 **불변성**을 가진다고 말한다. 이전 값을 변경시키는게 아니라 항상 새로운 값을 할당한다.

참조형 데이터도 변수 영역과 데이터 영역이 분리되는 방식은 동일하지만, Object를 저장할 변수 영역을 추가로 만드는 점에서 차이가 있다.

```jsx
var a = 10;

var b = {
  num1: 10,
  num2: 20,
};
```

첫 번째 라인에서 기본형 변수를 초기화하며, 변수 영역에 식별자 a가 저장되고, 데이터 영역에 10을 저장하는 공간(`$0`)이 할당된다.

다음으로 참조형 변수를 선언할 때에는?

마찬가지로 변수 영역에 식별자 b가 저장되고, 그 안에 저장될 값을 데이터 영역에서 할당한다. 근데 여기서, 안에 들어갈 값이 한 개가 아니라 num1, num2 두 개나 된다. 이 경우에는 데이터 영역에 값을 입력하기 위하여 b객체의 프로퍼티를 저장할 **변수 영역**에 새로운 공간을 만들고, 그 안에 프로퍼티의 값들이 입력된다. (프로퍼티가 기본형이면 동일한 방식으로)

`b.num1` 값을 찾아가는 과정이 이렇게 된다.

변수 영역 → (데이터 영역의 주소) → 데이터 영역 → (프로퍼티들이 저장된 주소) → 변수 영역 → (프로퍼티의 값이 저장된 데이터 영역의 주소) → 데이터 영역 → (num1에 저장된 값)

결과적으로 객체의 제일 안쪽에 저장될 데이터들은 기본형 변수들이기 때문에, 값이 저장되는 방식은 똑같지만 참조형 변수는 **객체의 프로퍼티를 위한 변수 영역**이 추가로 할당되어, 주소값 전달이 한 단계 더 많다. 객체가 중첩으로 할당되는 경우에는 그 단계가 더욱 많아진다.

그리고 이 단계가 늘어나는 것으로 참조형 변수는 가변형이 될 수가 있다.

`b.num1`의 값을 변경한다고 해도, 식별자에 입력된 **객체의 프로퍼티가 저장된 변수 영역**의 주소값은 변하지 않기 때문! **프로퍼티 .. 변수 영역** "안에" 입력되는 주소값이 변하지 그 자체의 주소는 변하지 않는다. 그래서 결과적으로 내부 값은 변하지만, `b` 식별자는 여전히 같은 주소값을 가리키게 된다. 그 결과

```jsx
var obj1 = {
  num1: 10,
  num2: 20,
};
var obj2 = obj1;

obj1.num1 = 33;
console.log(obj2.num1);
```

위와 같은 결과가 나오게 된다. obj2의 num1은 의도치 않게 값이 변했는데, obj1과 같은 주소를 가리키고 있으니 당연하다.

그래서 객체를 복사할 경우에는 깊은 복사를 해야 한다. 직접 함수를 만들어서 객체를 하나하나 반복문으로 탐색하며 새로운 객체를 생성할 수도 있고, ES6에서 추가된 Spread Operator를 사용해도 된다.

```jsx
var obj1 = {
  num1: 10,
  num2: 20,
};
var obj2 = {
  ...obj1, // Spread Operator
};

obj1.num1 = 33;
console.log(obj2.num1);
```

## undefined와 null

기본형 변수의 종류에, undefined와 null이 있다.

이 둘은 모두 "값이 없음" 을 의미하지만, 내부적으로는 더 세밀한 차이가 있다.

undefined는 값이 없기도 없다는 뜻이지만, 정확히는 "정의되지 않은" 상태다.

개발자가 변수를 선언했는데, 초기화를 하지 않은 경우에 자바스크립트는 선언한 변수의 값을 undefined라고 간주한다.

null의 경우에는 개발자가 명시적으로 "값이 없음" 그 자체를 의미하기 위한 변수다.

값이 없는 상태는 같기 때문에, 아래와 같은 결과가 나온다

```jsx
undefined == null; // true
undefined === null; // false, Type까지 비교하기 때문에 false
```

위에서 선언한 변수에 값을 할당하지 않으면 변수의 값이 undefined로 추론된다고 했는데, 배열의 경우에는 undefined가 아닌 **empty** 상태가 입력된다.

```jsx
var arr = [];
arr.length = 2;
console.log(arr); // [empty x 2]
```

empty는 배열에서 특수한 값으로 간주되어, 배열을 순회하는 메소드 등이 이 empty를 만나면, 순회하지 않고 무시한다.

---

## 배열에 비동기 작업 수행하기

---

`순차 처리, 병렬 처리`

`요약: forEach로 비동기 처리 하려고 하지 마라`

배열에 든 요소들을 대상으로, 비동기 함수를 실행하고 싶을 수 있다.  
그리고 배열의 요소를 순회하는 방법도 다양하다.

첫째로 index를 이용한 기본적인 `for` 구문  
배열의 `.forEach` 함수를 이용한 `for-each` 구문  
마지막으로 `for-of` 구문

여기서 나는 `for-each`를 애용했는데, 이 함수는 비동기 처리에 적합하지가 못하다.  
forEach를 써서 비동기 처리를 한다 치면 이렇게 함수를 쓰면 작동할 거라 생각했다.

```js
function promiseFunc(number) {
  // 500ms 후에 number를 출력함
  return new Promise((resolve) => setTimeout(() => resolve(number), 500));
}

const arr = [1, 2, 3, 4];

arr.forEach(async (each) => {
  const result = await promiseFunc(each);
  console.log(result);
});
```

각 요소를 대상으로, 500ms씩 기다리는 `promiseFunc` 함수를 `async-await`를 이용했으니  
500ms마다 숫자가 하나씩 출력될 거라고 생각을 할 수 있는데, 실제로 실행하면 숫자가 처음 500ms 이후에 한 번에 출력된다.

여기서 forEach의 polyfill을 보면 왜 위와 같은 일이 발생하는 지 알 수 있다.

```js
Array.prototype.forEach = function (callback) {
  for (let index = 0; index < this.length; index++) {
    callback(this[index], index, this);
  }
};
```

forEach도 내부에서는 이렇게 `for` 반복문을 사용하며, 인자로 전달받은 callback 함수를 하나씩 호출하는걸 볼 수 있다.  
여기서 우리가 `async-await`를 붙이는 부분은? callback 함수의 내부가 된다.

그러니 callback 함수의 내부만 비동기로 작동하고, 그 바깥의 `for` 반복문은 비동기로 처리되지 않는다.  
우리는 반복문이 비동기로 돌기를 바라는데, 그것이 아니게 됨  
풀어서 보면 이해가 되는데 그냥 `forEach`로만 쓰면 착각하게 된다.

그래서 `forEach`를 쓰지 않고, `for-of`나 일반 `for` 반복문을 이용하는 것이 옳다.

```js
function promiseFunc(number) {
  // 500ms 후에 number를 출력함
  return new Promise((resolve) => setTimeout(() => resolve(number), 500));
}

const arr = [1, 2, 3, 4];

(async () => {
  for (const each of arr) {
    const result = await promiseFunc(each);
    console.log(result);
  }
})();
```

이렇게 for문을 실행하는 구문 자체를 통째로 `async-await`처리 해야 올바르게 500ms씩 차례로 숫자가 출력된다.

여기까지는 *순차 처리*를 위한 코드 작성법이고, 비동기 처리를 실시하되 순서가 중요하지 않은 경우에는 *병렬 처리*를 할 수 있다.

병렬 처리? 아까 `forEach`로 한 번에 실행되던거 병렬 처리 아닌가? 라고 생각하면 안 된다

```js
(async () => {
  arr.forEach(async (each) => {
    const result = await promiseFunc(each);
    console.log(result);
  });

  await console.log('done');
})();
```

애당초 `forEach`는 비동기 처리를 제대로 해 준 게 아니기 때문에, 이렇게 코드를 작성해도 `done` 이 가장 먼저 출력된다.

비동기 처리가 `forEach`의 콜백 내부에서만 작동하기 때문에, 바로 `done`을 출력하는 코드가 실행돼버린다. 이럴 때는

`Promise.all()`을 사용하면 올바르게 병렬처리를 대기할 수 있다.

```js
(async () => {
  const promises = arr.map(async (each) => {
    const result = await promiseFunc(each);
    console.log(result);
  });
  await Promise.all(promises);

  console.log('done');
})();
```

`map`함수로 배열의 각 객체의 `Promise` 객체들을 반환받고, 반환받은 객체들을 `Promise.all()` 함수를 이용해서 기다릴 수 있다.  
이렇게 되면 함수 전체가 비동기 처리되어 올바르게 각 `promiseFunc()`의 실행을 기다리고, 마지막으로 `done`이 출력된다.
