# 불변성과 카피-온-라이트

- 동작은 다음과 같이 분류할 수 있다.
  - 읽기: 데이터를 바꾸지 않는다. 인자에만 의존하여 읽는다면 계산이라 할 수 있다.
  - 쓰기
    - 쓰기는 데이터를 바꾼다.
    - 바뀐 값은 어디에서도 사용할 수 있으므로 불변성 원칙(카피-온-라이트; copy-on-write)에 따라 구현해야한다.
    - 카피-온-라이트로 구현한 쓰기는 계산이라 할 수 있다. 값을 반환하는 것 외에 다른 동작을 하지 않기 때문이다.
    - 쓰기를 읽기로 바꾸면 데이터는 변경되는 일이 없으므로 변경 불가능한 데이터가 된다.
  - 읽는 동시에 쓰기



## 자바스크립트에서 카피-온-라이트 구현하기

- 카피-온-라이트는 세 단계로 이루어진다.
  1. 복사본 만들기
  2. 복사본 변경하기
  3. 복사본 반환하기

```javascript
function addItem(arr, item) {
  const newArr = [...arr];
  newArr.push(item);
  return newArr;
}

function removeItem(arr, item) {
  const newArr = [...arr];
  const idx = newArr.indexOf(item);
  if (idx !== -1) newArr.splice(idx, 1);
  return newArr;
}
```

### 연습문제 p.120

```javascript
let mailing_list = [];
function add_contact(emailList, email) {
  return [...emailList, email];
}
function submit_form_handler(event) {
  const form = event.target;
  const email = form.elements['email'].value;
  mailing_list = add_contact(mailing_list, email);
}
```

## 읽는 동시에 쓰는 동작은 어떻게 카피-온-라이트로 구현하는가?

```javascript
// Array.prototype.shift 는 쓰는 동시에 읽는 동작이다.
const arr = [1, 2, 3, 4];
console.log(arr.shift());	// 1; 값을 반환하였으므로 읽기
console.log(arr);	// [2, 3, 4]; 값을 변경하였으므로 쓰기
```

읽는 동시에 쓰는 동작을 카피-온-라이트로 구현하는 방법은 두 가지이다.

1. 읽기와 쓰기 함수로 각각 분리한다. => 관심사와 책임이 분리되어있으므로 더 좋다.
2. 함수에서 값을 두 개 반환한다.

### 읽기와 쓰기 함수로 각각 분리하기

- 읽기와 쓰기 함수로 각각 분리하기는 두 단계로 이루어진다.
  1. 쓰기에서 읽기를 분리한다.
  2. 쓰기에 카피-온-라이트를 적용해 읽기로 바꾼다.

`shift`를 카피-온-라이트로 구현해보자.

```javascript
// 1. 쓰기에서 읽기 분리하기
function getFirstElement(arr) {
  return arr[0];
}
function shift(arr) {
  arr.shift();
}

// 2. 쓰기에 카피-온-라이트를 적용해 읽기로 바꾸기
function shift(arr) {
  const [removed, ...newArr] = arr;
  return newArr;
}
```

### 값을 두 개 반환하는 함수로 만들기

- 값을 두 개 반환하는 함수로 만들기는 두 단계로 이루어진다.
  1. 기존 동작을 새로운 함수로 감싼다.
  2. 새로운 함수를 읽기만 하는 함수로 바꾼다.

```javascript
// 1. 기존 동작을 새로운 함수로 감싸기
function shift(arr) {
  arr.shift();
}

// 2. 새로운 함수를 읽기만 하는 함수로 바꾸기
function shift(arr) {
  return [removed, ...newArr];
}
```



### 연습문제 p.125

- 읽기 함수와 쓰기 함수로 분리하기

```javascript
function getLastElement(arr) {
  return arr.at(-1);
}

function pop(arr) {
  const newArr = [...arr];
  newArr.pop();
  return newArr;
}
```

- 값을 두 개 반환하는 함수로 만들기

```javascript
function pop(arr) {
	const newArr = [...arr];
  return [newArr.pop(), newArr];
}
```



### 연습문제 p.128

```javascript
function push(arr, item) {
	return [...arr, item];
}
```

### 연습문제 p.129

```javascript
function add_contact(mailing_list, email) {
  return push(mailing_list, email);
}
```

### 연습문제 p.130

```javascript
function arraySet(arr, idx, value) {
  const newArr = [...arr];
  newArr[idx] = value;
  return newArr;
}
```



## 불변 데이터 구조는 빠르다

- 얕은 복사(shallow copy)는 데이터 구조의 최상위 단계만 복사하는 것이다. 이때 데이터 구조가 중첩되어도 최상위만 복사한다.
- 구조적 공유(structural sharing)은 두 개의 중첩된 데이터 구조가 어떤 참조를 공유하는 것이다. 모든 것을 복사하지 않으며, 메모리를 적게 사용하기 때문에 빠르다.
- 불변 데이터 구조는 바뀌지 않기 때문에 구조적 공유가 안전하다.

### 연습문제 p.136

```typescript
function objectSet(object, key, value) {
  return { ...object, [key]: value };
}
```

### 연습문제 p.137

```typescript
function setPrice(item, new_price) {
  return objectSet(item, 'price', new_price);
}
```

### 연습문제 p.138

```typescript
function setQuantity(item, new_quantity) {
  return objectSet(item, 'quantity', new_quantity);
}
```

### 연습문제 p.139

```typescript
function objectDelete(object, key) {
  const newObject = { ...object };
  delete newObject[key];
  return newObject;
}
```



## 중첩된 쓰기를 읽기로 바꾸기

- 중첩된 데이터 구조의 최상위부터 최하위까지 불변해야 불변 데이터 구조다.

### 연습문제 p.144

- p.140 최하위부터 최상위까지 중첩된 데이터 구조의 모든 부분이 불변형이어야한다 면 중첩된 데이터 구조는 deep copy해야되고 구조적 공유 하면 안되는 거 아님?
- 아래 코드는 deep copy인가?

```typescript
function setQuantityByName(cart, name, quantity) {
  return cart.map(({ _name, _quantity }) => ({ [_name]: _name === name ? quantity : _quantity }));
}
```

