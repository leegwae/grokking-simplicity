# ch4

## 테스트/재사용하기 쉬운 코드란?

- 테스트하기 쉽게 하려면
  - DOM 업데이트와 비즈니스 규칙은 분리되어야한다
  - 전역 변수가 없어야한다.
- 재사용하려면
  - 전역변수에 의존하지 않아야한다.
  - 항상 DOM을 사용할 수 있는 곳에서만 실행된다고 가정하면 안 된다.
  - 함수가 결괏값을 리턴해야 한다.

함수의 암묵적 입력과 암묵적 출력을 없애 테스트/재사용하기 쉬운 코드를 만들 수 있다.

## 함수의 입력과 출력

- 함수에 암묵적 입력과 암묵적 출력이 있으면 함수는 액션이 된다.
- 함수의 인자는 명시적 입력이다.
- 함수의 인자 외 다른 입력은 암묵적 입력이다. 예) 전역변수를 읽는 것
- 함수의 리턴값은 명시적 출력이다.
- 함수의 리턴값 외 다른 출력(리턴값을 반환하는 것 외의 모든 일)은 암묵적 출력이다. 예) 전역변수를 바꾸는 것, 콘솔 출력하는 것, DOM을 업데이트하는 것
- 명시적 입력과 명시적 출력은 불변값이다. 인자나 리턴값이 이후에 바뀐다면 암묵적 입력과 암묵적 출력이다.
- 암묵적 입력과 암묵적 출력이 부수 효과다.

**=> 최대한 암묵적 입력을 명시적 입력(인자)으로, 암묵적 출력을 명시적 출력(반환값)으로 바꾸자.**

## 암묵적 입력과 암묵적 출력을 제거하기

1. 계산으로 뺄 코드를 찾아 함수로 분리한다.
2. 분리한 함수에서 암묵적 입력과 출력을 찾는다.
3. 암묵적 입력은 명시적 입력(인자)으로, 암묵적 출력은 명시적 출력(리턴값)으로 바꾼다.

```javascript
/* 암묵적 입력, 암묵적 출력이 있는 함수 */
const todos = [];
function addTodo(title) {
  todos.push(title);
}
add_todo('집 사기');

/* 암묵적 입력은 없고, 암묵적 출력이 있는 함수 */
let myTodos = [];
function addTodo(todos, title) {
  todos.push(title);
}
add_todo(myTodos, '집 사기');

/* 암묵적 입력, 암묵적 출력이 없는 함수 */
let myTodos = [];
function addTodo(todos, title) {
  const newTodos = todos.slice();
  newTodos.push(title);
  return newTodos;
}
myTodos = addTodo(myTodos, '집 사기');
```

- 카피-온-라이트(copy-on-write): 어떤 값을 바꿀 때, 그 값을 복사하여 바꾸는 방법. 불변성을 구현하기 위해 사용한다.

```javascript
/* 암묵적 입력은 없고, 암묵적 출력이 있는 함수 */
let myTodos = [];
function addTodo(todos, title) {
  todos.push(title);
}
add_todo(myTodos, '집 사기');
```

- 전역변수를 바꾸는 것처럼 보이지 않는데, 왜 이 함수는 계산이 아닐까? 계산은 순수함수로서 실행 시점이나 횟수에 의존하지 않아야한다. 그러나 이 `addTodo`는 실행될 때마다 전역변수 `myTodos`를 수정하게 된다.

```javascript
/* 암묵적 입력, 암묵적 출력이 없는 함수 */
let myTodos = [];
function addTodo(todos, title) {
  const newTodos = todos.slice();
  newTodos.push(title);
  return newTodos;
}
myTodos = addTodo(myTodos, '집 사기');
```

- 내부에서 `newTodos`를 바꾸고 있는데, 왜 이 함수는 액션이 아닐까? 불변값은 생성된 후 바뀌면 안되는 값이나, 생성할 때 초기값이 필요한 경우도 있다.



## 스터디

### 연습문제 p.79

```javascript
function update_tax_dom() {
  set_tax_dom(shopping_cart_total * 0.10);
}
```

위 코드에서 세금을 계산하는 부분을 추출해보자.

```javascript
// 1. 계산 코드를 찾아 함수로 분리하기
function calc() {
  return shopping_cart_total * 0.10
}
function update_tax_dom() {
  set_tax_dom(calc());
}

// 2. 암묵적 입력 찾아 인자로 바꾸기: DOM 업데이트와 비지니스 규칙을 분리한다.
function calc(cart_total) { // 비지니스 규칙
  return cart_total * 0.10;
}
function update_tax_dom() { // DOM 업데이트
  set_tax_dom(calc(shopping_cart_total));
}
```



### 연습문제 p.82

```javascript
// 1. 계산 코드를 찾아 함수로 분리하기
function isFreeIconVisible() {
  return item.price + shopping_cart_total >= 20;
}
function update_icons() {
  const buy_buttons = get_buy_buttons_dom();
  buy_buttons.forEach(button => {
    const { item } = button;
    if (isFreeIconVisible()) button.show_free_shipping_icon();
    else button.hide_free_shopping_icon();
  });
}

// 2. 암묵적 입력 찾아 인자로 바꾸기: DOM 업데이트와 비지니스 규칙을 분리한다.
function isFreeIconVisible(item, cart_total) {
  return item.price + cart_total >= 20;
}
function update_icons() {
  const buy_buttons = get_buy_buttons_dom();
  buy_buttons.forEach(button => {
    const { item } = button;
    if (isFreeIconVisible(item, shopping_cart_total)) button.show_free_shipping_icon();
    else button.hide_free_shopping_icon();
  });
}
```

- (TODO) 책에는 `itemPrice`를 인자로 받고 있다.`item`을 인자로 받아 `item.price`로 읽으면 이것도 암묵적 입력일까?

