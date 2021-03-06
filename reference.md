## const

재할당 x 재설정 x (read only)

explorer는 11부터 지원

```javascript
const MY_FAV_NO = 7;
MY_FAV_NO = 20; // 재할당 못한다고 TypeError남

const MY_FAV_NO = 20; // 식별자 MY_FAV_NO는 이미 선언되어있다고 TypeError남

const FOO; // const선언에는 초기화가 필요하다.. 때문에 SyntaxError남

const MY_OBJECT = {
  "key": "value"
}; // const는 object에서도 사용 가능 {key: "value"}

MY_OBJECT = {
  "name": "yj"
}; // 마찬가지로 재할당 못한다고 TypeError남

// 하지만 객체의 key는 보호되지 않는다.
MY_OBJECT.key = "changed";
// {key: "changed"}

// 결론 : 객체를 immutable하게 하려면 object.freeze();를 써라!!
```

## class

class선언은 hoisting되지 않는다.

chrome 42.0 / explorer ?

```javascript
// 아래 두개의 클래스에서 Square클래스가 먼저 선언되면 에러남... hoisting되지 않기 때문에
class Polygon {
  constructor(height, width) {
    this.name = 'Polygon';
    this.height = height;
    this.width = width;
  }
}

var polygon = new Polygon(); // 이렇게 쓸수있다.
typeof polygon; // function
polygon; // Polygon {name: "Polygon", height: undefined, width: undefined} 생성자를 쓰지 않았기 때문에 height, width는 undefined로 나온다.

var polygon = new Polygon(4, 8); // 이건 height와 width를 매개변수로 명시했기 때문에 4와 8이 잘 출력됨.

// Polygon을 상속받은 Square클래스.. super()는 this가 사용되기 전에 호출되어야 한다.
class Square extends Polygon {
  constructor(length) {
    super(length, length); // 부모클래스에 Square클래스 생성할때 받는 매개변수를 넘겨준다.
    this.name = 'Square';
  }
}

var square = new Square(4);
square; // Square {name: "Square", height: 4, width: 4}
```

## super

super키워드는 부모 오브젝트의 함수를 호출할때 사용

super([arguments]);

Chrome 42.0 / explorer ?

## for...in과 for...of차이

for...of는 chrome 38 / Edge 12 / Explorer ?

for...in은 객체의 모든 열거가능한 속성에대해 반복

for...of는 컬렉션(Array, Map, Set...) 전용. value가 나온다고 이해하면된다

```javascript
var iterable = 'boo';
for (var value of iterable) { // value is 속성값
  console.log(value);
}
/*
b
o
o
*/
for (var value in iterable) {
  console.log(value);
}
/*
0
1
2
*/

// Object에 objCustom 빈함수 추가, Array에 arrCustom 빈함수 추가
Object.prototype.objCustom = function () {};
Array.prototype.arrCustom = function () {};

var iterable = [3, 5, 7];
iterable.foo = "hello";

/*
index: 0, values: 3
index: 1, values: 5
index: 2, values: 7
index: foo, values: hello
index: arrCustom, values: function () {}
index: objCustom, values: function () {}
*/
for (var i in iterable) {
  console.log('index: ' + i + ', values: ' + iterable[i]);
}

for (var i of iterable) {
  console.log(i); // 3, 5, 7
}
```

## label

```javascript
var i, j;

loop1: // 첫번째 for문을 loop1이라고함
for (i = 0; i < 3; i++) {
   loop2: // 두번째 for문을 loop2라고함
   for (j = 0; j < 3; j++) {
      if (i === 1 && j === 1) {
         continue loop1; // 두번째 for문을 도는도중 i와 j가 1일경우 loop1로 가게 함
      }
      console.log("i = " + i + ", j = " + j);
   }
}
/*
i = 0, j = 0
i = 0, j = 1
i = 0, j = 2
i = 1, j = 0
i = 2, j = 0
i = 2, j = 1
i = 2, j = 2
*/
```

## 속성접근자

속성에 access하는 방법은 두가지가 있다.

```javascript
// . 점 표기법 property는 숫자로 시작하면 안된다. object.$1은 가능 object.1은 안됨
get = object.property;
object.property = set;

// [ ] 각괄호 표기법 property_name은 문자열. 1foo, !foo 모두 가능
get = object[property_name];
object[property_name] = set;

// 각괄호 표기법 예제
var object = {};
object['1'] = 'value'; // 숫자를 포함하는 모든 비 문자열 객체는 object[이곳]에서 toString()을 통해 형변환됨
console.log(object[1]);
```

## new

생성자의 인스턴스를 만듬... new constructor[([arguments])]

```javascript
function Car() {}
car1 = new Car();
// 지금 car1에는 color property가 없다. 따라서 undefined
console.log(car1.color);    // undefined
// Car prototype에 color property를 null로 설정한다.
Car.prototype.color = null;
// wow....
console.log(car1.color);    // null

car1.color = "black";
console.log(car1.color);   // black

Car.prototype.color = 'red'; // prototype color를 red로...
console.log(car1.color);   // black... red로 바뀌진 않는다.
```

## 전개연산자

여러개를 통째로 넘겨준다...?

```javascript
// 배열을 함수의 인수로 사용하고 싶을때 function.protytype.apply를 쓴다.
function myFunction(a, b, c) {
  return a + b + c;
}
var myArr = [1, 2, 3];
myFunction.apply(this, myArr);

// 같은결과이다 es6에 새로 추가된 전개연산자 문법임.
myFunction(...myArr);

// 여러번 쓸수도있다
function myFunction(v, w, x, y, z) {
  console.log(v, w, x, y, z);
}
var args = [0, 1];
myFunction(-1, ...args, ...[3, 4]); // -1 0 1 3 4

// 배열안에 끼워넣음...
var parts = ['shoulders', 'knees'];
var lyrics = ['head', ...parts, 'and', 'toes'];

// arr1에 arr2를 push할수도있다...
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1.push(...arr2);
arr1; // [0, 1, 2, 3, 4, 5]
```

## 단항 연산자 +, -

\+ : 단항 더하기 연산자. 피연산자를 숫자형으로 변환함

\- : 단항 빼기 연산자. 피연산자를 숫자형으로 변환하고 부정함

```javascript
+3         // 3
+'3'       // 3
+'3이야'   // NaN
+'-3'      // -3
+true      // 1
+false     // 0
+null      // 0
+undefined // NaN
+0         // 0

-3          // -3
-'3'        // -3
-'-3'       // 3
-'3이야'    // NaN
-true       // -1
-false      // -0
-null       // -0
-undefined  // NaN
-0          // -0
```

## 논리 NOT 연산자

헷깔리면 console에서 Boolean(헷갈리는거); 하면 확실함

```javascript
!true       // false
!false      // true
!1          // false
!0          // true
!'hi'       // false
!''         // true
!undefined  // true
```

## 선택 연산자 ||

```javascript
var a = 0,
    b = false,
    c = 1;

// 위의 조건이 모두 boolean형으로 변환해서 false일 경우 0이 반환된다.
console.log( a || b || c); // 1
```

## instanceof

어떠한 객체가 어떠한 생성자에서 만들어진 인스턴스인지 boolean값으로 리턴해준다.

```javascript
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
var myObj = new Car("Volkswagen", "Golf", 2014);
myObj instanceof Car; // true
```

## 화살표함수 =>

익명함수를 편한방법으로 쓸수있다.

```javascript
// ES5
var selected = allJobs.filter(function (job) {
  return job.isSelected();
});

// ES6 job을 parameter값으로 주고 => job.isSelected()를 return한다
var selected = allJobs.filter(job => job.isSelected());

// ES5
var total = values.reduce(function (a, b) {
  return a + b;
}, 0);

// ES6 parameter값이 여러개면 괄호로 묶어서 한다...
var total = values.reduce((a, b) => a + b, 0);

// 빈객체{}와 비어있는블록{}을 주의하자. 객체리터럴은 무조건 괄호로 묶는다!!!
var chewToys = puppies.map(puppy => {});   // BUG!
var chewToys = puppies.map(puppy => ({})); // ok
```

## 비구조화 할당

배열 또는 객체에서 데이터를 '별개'변수로 추출할 수 있게하는 표현식

```javascript
var a, b, rest;
[a, b] = [1, 2]
// rest는 현재 undefined상태
console.log(a) // 1
console.log(b) // 2

[a, b] = [ [3, 4], [5, 6] ]; // a변수에 배열 [3, 4], b변수에 배열 [5, 6]

({a, b} = {a:1, b:2}); // Object {a: 1, b: 2} 와 같다

var z = ({a, b} = {a:1, b:2}); // 이런식으로 z에 할당해서 z.a으로 접근해도 된다.

var x = [1, 2, 3, 4, 5]
var [y, z] = x // 첫번째 인덱스가 y 두번째 인덱스가 z
console.log(y); // 1
console.log(z); // 2

var a = 1;
var b = 3;
// 변수의 교환...
[a, b] = [b, a];
console.log(a); // 3
console.log(b); // 1

// f()함수는 배열 [1, 2]를 리턴한다
function f() {
  return [1, 2];
}

var a, b;
[a, b] = f();   // [a, b] = [1, 2];
[a] = f();      // 1
[...a] = f();   // a = [1, 2];
console.log(a); // 1
console.log(b); // 2

// 일부 반환값을 무시하려면 ,로 구분해라
function f() {
  return [1, 2, 3];
}

var [a, , b] = f(); // 2를 무시함
console.log(a); // 1
console.log(b); // 3
```
