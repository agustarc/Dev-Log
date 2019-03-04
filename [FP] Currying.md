# Currying 이란?
* 수학과 컴퓨터 과학에서 커링이란 다중 인수 (혹은 여러 인수의 튜플)을 갖는 함수를 단일 인수를 갖는 함수들의 함수열로 바꾸는 것을 말한다. 모지즈 쇤핑클에 의해 도입되었고, 이후 해스켈 커리에 의해 발전하였다.
* 이상 위키의 내용인데 참 간단하게도 얘기해놨다.
</br>

# 좀 더 이해하기 쉽게
* **여러 개의 인자를 갖는 함수 형태를 -> 단일 인자를 갖는 함수 여러 개의 형태로 표현하는 것**
```
f(n1, n2, n3, n4) -> f(n1)f(n2)f(n3)f(n4)
```
* 이해하기 쉽게 저렇게 표현해봤다.
* Currying을 적용하기 위한 **전제조건**으로 함수가 반환 타입 또는 변수에 할당할 수 있어야 된다.
* 기존에 인자가 여러 개인 함수를 호출할 때 인자 개수를 맞춰줘야 했던 것과는 달리
* Currying을 이용하면 인자를 누락시켜 호출할 수 있다.
```
f(n1, n2, n3, n4)를 호출할 때
f(1, 2, 3, 4) 이렇게 인자 개수를 맞춰줘야 했다면

f(n1)f(n2)f(n3)f(n4)를 호출할 때는
f(1) or f(1)(2) or f(1)(2)(3) or f(1)(2)(3)(4) 형태가 가능하다.

```
* 위와 같은 형태가 쉽게 와닿지 않을 수 있는데 f(1)(2) 이런 형태를 기존 Java 개발할 때 
메소드 체이닝 형태로 생각하면 이해가 쉽다. 
* f(1).f(2).f(3).f(4) 이렇게 동작한다고 하면 이해가 쉬우려나
</br>

# 실제 코드
* 위와 같은 구조로 여러 인자를 합치는 Currying 함수를 만들어 본다면?
* Kotlin 버전
```kotlin
fun sum(n1: Int): (Int) -> (Int) -> Int = fun(n2: Int): (Int) -> Int = fun(n3: Int): Int = n1 + n2 + n3

val addOne: (Int) -> (Int) -> Int = sum(1)
val addTwo: (Int) -> Int = addOne(2)
val addThree: Int = addTwo(3)

println(addOne) // (kotlin.Int) -> (kotlin.Int) -> kotlin.Int
println(addTwo) // (kotlin.Int) -> kotlin.Int
println(addThree) // 6
```

* Scala 버전
```scala
def sum(n1:Int): Int => Int => Int = (n2:Int) => (n3: Int) =>  n1 + n2 + n3

val addOne: Int => Int => Int = sum(1)
val addTwo: Int => Int = addOne(2)
val addThree: Int = addTwo(3)

println(addOne)
println(addTwo)
println(addThree)
```
* Scala 배우는 김에 이럴 때 써먹자..
</br>

# 어디에 쓰일까?
* 일단 함수 재사용에 장점이 있을 것 같다.
* 아직 어디에 유용하게 쓰일지 감이 안 온다. 
* 함수형 공부하면서 내용 업데이트하자.
