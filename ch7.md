# 사이드 이펙트가 있는 코드를 사용하면서 불변성 유지하기

- '카피-온-라이트 방식은 무엇을 바꾸어야하는지 알고 있어 해당 데이터를 복사해서 변경하는 방식이다.
- 하지만 무엇을 바꾸는지 정확히 알 수 없는, 사이드 이펙트가 있는 레거시 코드가 있다고 하자. 이 경우 카피-온-라이브 방식을 사용할 수 없다.
- 사이드 이펙트가 있는 레거시 코드를 사용하면서 불변성을 유지하려면 어떻게 해야하는가? => 방어적 복사를 사용한다.
- 방어적 복사란? 방어적 복사(defensive copy)는 신뢰할 수 없는 영역에서 바뀔 수도 있는 데이터가 불변성을 유지하는 영역으로 들어갈 때 깊은 복사하고 나갈 때 깊은 복사하는 방식이다.

## 방어적 복사 규칙

1. 불변성 유지하는 영역에서 나갈 때 깊은 복사한 데이터를 전달한다.
2. 불변성 유지하는 영역으로 들어올 때 깊은 복사한 데이터를 전달한다.

### TODO; 왜 굳이 불변성 유지하는 영역에서 나갈 때도 깊은 복사해야하는지 모르겠다

- 깊은 복사 안 해도 불변성 잘 지켜지지 않나?
- 예시가 없을까?

### 안전하게 방어적 복사 코드 분리하기

```javascript
const copiedCart = deepCopy(shppingCart);
black_friday_promotion(copiedCart);
shoppingCart = deepCopy(copiedCart);
```

이런 함수가 있으면 이해하기 어려울 수도 있다. 왜 깊은 복사하는지는 `black_friday_promotion` 함수가 사이드 이펙트가 있는 함수이기 때문이라는 것을 알아야하기 때문이다.

```javascript
function black_friday_promotion_safe(cart) {
  const copiedCart = deepCopy(shppingCart);
	black_friday_promotion(copiedCart);
	shoppingCart = deepCopy(copiedCart);;
  
  return copiedCart;
}

shppingCart = black_friday_promotion_safe(shoppingCart);
```

### 연습문제 p.154

```javascript
function payrollCalc(employees) {
	// payRollChecks를 반환하긴 하는데 employees를 변경할 수도 있음
  // 신뢰할 수 없는 코드임
  return payrollChecks;
}

function payrollCalcSafe(employees) {
  const copiedEmployees = deppCopy(employees);
  const payRollChecks = payrollCalc(copiedEmployees);
  return deepCopy(copiedEmployees);
}
```

### TODO; 연습문제 p.155

못 풀었다 무슨 소린지 못 알아들었다.

### 연습문제 p.161

1. DC
2. SC
3. SC
4. DC
5. DC

### 연습문제 p.163

1. DC
2. CW
3. DC, CW
4. **DC, CW => CW임** DC도 복사하니까 맞지 않을까?
5. CW
6. DC
7. **CW => DC임**. 카피-온-라이트가 없으면 안전지대가 없는거니까 CW 아님?
8. CW
9. DC
10. DC
