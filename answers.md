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
`foo()`함수 안에서 정의되어 반환되는 함수를 `클로저`라고 부른다. 또한 아래에서 `foo`함수를 호출할 때 `77`이라는 입력값을 기억하는 것으로 **만들어진 환경을 기억**한다고 할 수 있다.

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