# 계층형 설계 - 패턴 1: 직접 구현

- 계층형 설계(stratified design)는 바로 아래 계층의 함수로 현재 계층의 함수를 만드는 것이다.

## 패턴 1: 직접 구현

- 함수 시그니처가 나타내고 있는 문제를 함수 본문에서 적절한 구체화 수준에서 해결해야한다. 너무 구체적이면 code smell이다.
- 또한 **비슷한 수준으로 추상화되어야 한다. 모두 동일한 추상화 단계를 사용하는 것이 좋으므로, 추상화거나 풀어버리자**. [참고 > 토스ㅣSLASH 21 - 실무에서 바로 쓰는 Frontend Clean Code](https://youtu.be/edWbHp_k_9Y?feature=shared)
  - 다이어그램에서 화살표의 길이가 추상화 수준(구체화 수준)을 나타낸다. 즉 화살표는 모두 동일한 길이를 가져야한다.

- **같은 계층의 함수들이 서로를 호출하지 않아야한다. 반드시 상위 계층은 하위 계층의 함수를 호출하도록 한다.**
- 상위 계층은 하위 계층의 구체적인 구현을 몰라야한다.
- 이 패턴에서 상위 계층이 하위 계층을 가리키는 화살표는 동일한 길이를 가져야한다. 화살표의 길이가 동일하다는 것은 동일한 구체화 수준을 가지는 것이다.

```javascript
functio freeTieClip(cart) {
  // 넥타이 하나를 사면 무료로 넥타이 클립을 하나 더 주는 함수
  // 생략
  for (let i = 0; i < cart.length; i++) {
    // 이 함수가 cart가 배열이라는 걸 알아야하는가?
  }
}
```

- 계층의 목적은 계층에 있는 함수의 목적이다.

### 연습문제 p.190

 ```typescript
 function isInCart(cart, name) {
   return indexOfItem(cart, name) !== null;
   // isInCart는 cart가 어떻게 생겼는지 몰라도 된다
 }
 ```

```
isInCart
	ㄴindexOfItem()
		ㄴfor loop
		ㄴarray index
```

### 연습문제 p.192

```typescript
function setPriceByName(cart, name, price) {
  const cartCopy = cart.slice();
  const targetIdx = indexOfItem(cart, name);
  
  if (targetIdx !== null) {
    cartCopy = setPrice(cartCopy[targetIdx], price);
  }
  
  return cartCopy;
}
```

### 연습문제 p.194

```typescript
function setPriceByName(cart, name, price) {
  const idx = indexOfItem(cart, name);
  if (idx !== null) return arraySet(cart, idx, price);
  return cart;
}
```

### p.197

- 인덱스로 참조하는 게 더 읽기 쉬워보인다.
- 배열인 걸 왜 감춰야하는지 모르겠다.