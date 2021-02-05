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

`같이 볼 내용 -> Reflow, Repaint`

---
## 호이스팅이란
---
`함수 안에 있는 선언들을 모두 끌어올려, 해당 함수 유효 범위의 최상단에 선언하는 것`

Javascript가 실행될 때, 내부적으로 이루어지는 과정이며 실제로 코드가 위쪽에 실행되거나 하는 것이 아님, 따라서 실행 과정이나 메모리에 영향을 주지는 않는다.  

`var` 변수와 *함수 선언문* 에서만 호이스팅이 발생하며, `let`, `const` 변수와 *함수표현식* 에서는 호이스팅이 발생하지 않는다. 또한 `class` 선언도 마찬가지로 호이스팅이 발생하지 않는다.
```javascript
// 함수 선언문
function foo() {
  console.log('bar');
}

// 함수 표현식
var foo = function () {
  console.log('bar');
}
```
호이스팅 덕에 기본적으로 함수를 코드 위쪽에서 사용하고, 선언은 아래쪽에 해도 오류 없이 작동한다. 다만 예외가 되는 *함수 표현식* 의 경우에는 오류가 발생할 여지가 있다.
```javascript
function foo() {
  var result = bar();
  var bar = function() { return 123; }
}
// 호이스팅 후
function foo() {
  var bar; // 변수는 호이스팅 발생
  var result = bar(); // 여기서 bar가 선언되지 않았기에 오류 발생
  // 'bar' is not a function
  bar = function() { return 123; } // 함수 표현식은 호이스팅 발생 안 함
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
console.log(typeof(foo)); // 'string'
```

---
## 클로저란
---
[링크](https://hyunseob.github.io/2016/08/30/javascript-closure/)

`클로저란 독립적인 (자유)변수를 가리키는 함수이다. 또한 클로저 안에 정의된 함수는 만들어진 환경을 '기억'한다.`

```javascript
function foo(number) {
  var text = `input number is: ${number}`;
  return function() {
    return text;
  }
}
var closure = foo(77);
console.log(closure()); // 'input number is 77'
```
`foo()`함수 안에서 정의되어 반환되는 함수를 `클로저`라고 부른다. 또한 `closure` 함수가 출력될 때 `77`이라는 입력값을 기억하는 것으로 **만들어진 환경을 기억**한다고 할 수 있다.

```javascript
function hello(name) {
  this._name = name;
}
hello.prototype.say = function() {
  console.log(`hello ${this._name}`);
}

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
  return function() {
    console.log(`hello ${_name}`);
  }
}
var hello1 = new hello('gincheong');
hello1(); // 'hello gincheong'
```
클로저는 환경을 기억하기 위해 `hello`함수의 `_name` 지역변수를 메모리에 저장하게 된다. 클로저가 계속 존재하는 이상, `_name` 변수는 메모리에서 삭제되지 않기 때문에 메모리 누수가 발생할 수도 있다. 따라서 클로저를 더 사용하지 않게 되면. `hello1 = null;` 과 같이 클로저의 참조를 제거해주는 것이 좋다.

---
## 프로토타입이란
---
[링크](https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67)

`Javascript의 객체는 모두 'Object' 의 하위 객체이며, Prototype Obejct을 통해 연결되어 있다. 이를 Prototype Chain이라 한다.`

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
__proto__를 통해 상위 속성에 접근한다는 개념은, 모든 객체들에서 `.toString()` 함수를 호출할 수 있다는 점을 생각하면 이해하기 쉽다.

___
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
- 컴포넌트 기반의 SPA 라이브러리
  - 컴포넌트 단위로 데이터 변화를 감지하고, 렌더링이 필요한 부분만 부분적으로 변화시켜줌
- `prop`, `state`, 그리고 라이프사이클
  - 컴포넌트 단위 개발이라는 공통점 탓에, 사용법은 달라도 기본적인 구조 자체는 비슷함
- Virtual DOM 사용
  - Virtual DOM을 사용해서, 실제 DOM에 변화가 일어나는 부분만 변화시키기 때문에 성능 이점을 가짐

1. 컴포넌트의 데이터를 바꿀 때
- Vue는, `this.{변수} = {값}` 의 형태로 바로 값을 변경할 수 있다. 
- React는, `this.setState()` 함수를 통해 데이터를 바꾼다, setState함수를 써야만 React의 라이프사이클이 정상적으로 진행된다.
- Vue에도 물론 비슷한 형태의 라이프사이클이 존재하지만, 값을 단순히 대입하는 것으로도 라이프사이클이 진행되도록 되어 있음

2. HTML을 표현하는 방식
- Vue는, 기본 HTML 문법을 그대로 따르기 때문에, 특정 환경이 없이도 Vue 메인 스크립트를 불러오기만 하면 어디에서나 작동한다.
- React는, JSX라는 개별적인 표현 문법을 사용하는데, 그렇기 때문에 Vue와 달리 단순 실행으로는 작동하지 않음. `react-scripts start` 명령어 같은 걸로 따로 JSX를 해석할 환경? 도구가 필요하다.

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
    const {todos, onClick} = this.props;
    return (
      <ul>
        {todos.map(todo =>
          <Todo 
            key={todo.id}
            onClick={onClick}
            {...todo}
          />
        )}
      </ul>
    );
  }
}

// 컨테이너 컴포넌트에서 프레젠테이션 컴포넌트로 전달하는 state
const todolistStateToProps = (state) => {  
  return {
    todos: state.todos
  }
}

// 컨테이너 컴포넌트에서 프레젠테이션 컴포넌트로 액션을 보내는 함수
const todolistDispatchToProps = (dispatch) => {  
  return {
    onClick(data){
      dispatch(complete(data)) // 액션 메서드
    }
  }
}

// 연결
export default connect(todolistStateToProps,todolistDispatchToProps)(TODOList);  
```

### reducer
`React 저장소에 유일하게 접근할 수 있는 객체로, 들어오는 action에 따라 행동한다.`  
리듀서들로 이루어진 폴더, 리듀서를 정의한 것을 보자
```javascript
import todoAction from '../action/index';  
const {ADD_TODO} = todoAction.todo;

const todo = (state, action) => {  
  switch (action.type) {
    case ADD_TODO:
      return {
        text: action.text,
        completed: false
      };
    default:
      return state;
  }
}


const todos = (state = [], action) => {  
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state, todo(undefined, action)
      ];
    default:
      return state;
  }
}
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

2. Backtick( ` )을 이용한 템플릿 리터럴
- 문자열 덧셈을 + 없이 간편하게 할 수 있다.
- 또한 문자열 내에 변수 값 삽입도 간편함
```javascript
const innerHTML = `<input type="number" value=${inputValue} />`;
```

3. 함수 기본 매개변수 값 지정
```javascript
function myFunc(a=10, b=20) {
  ...
}
```

4. 화살표 함수
```javascript
const numbers = [1, 2, 3];

const newNumbers = numbers.map(val => val + 1);
// 중괄호를 생략해서 바로 return할 수 있음
```

5. 비구조화 할당
```javascript
const album = {
  price: 50000,
  name: 'One Reeler',
  titleTrack: 'panorama'
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

  if (response.ok) { // 작업이 성공하면
    resolve(response.data);
  } else { // 작업이 실패하면
    reject("Error Occured");
  }
});

connect.then(success => {
  // resolve함수에서 전달된 response.data 값이 전달됨
  console.log(success);
}).catch(error => {
  console.error(error); // 출력: Error Occured
});
```

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
const introduce = function(name) {
  console.log(`my name is ${name} and I'm ${this.age}`);
}

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