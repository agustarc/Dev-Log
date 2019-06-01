# 수신 객체 지정 람다(Lambda with receiver)
  * **수신 객체 지정 람다란?**
    * 람다의 본문 안에서 다른 객체의 메서드를 호출할 수 있게 하는 것
    * 수신 객체 지정 람다 안에서는 ```this```를 통해 해당 객체의 멤버에 접근 가능하다.
    * 가장 흔히 사용하는 ```Standard.kt```에 수신 객체 지정 람다의 예를 볼 수 있다.  
      ```kotlin
      public inline fun <T, R> T.run(block: T.() -> R): R
      public inline fun <T, R> with(receiver: T, block: T.() -> R): R
      public inline fun <T> T.apply(block: T.() -> Unit): T
      ```
    * 위의 함수들은 람다 본문 안에서 ```T```의 멤버에 접근 가능하다. 
    * 쉽게 이해되지 않을 땐 ```T``` 객체에 정의되는 익명 함수를 전달받는다고 생각하자.
    * ```StringBuilder```를 수신 객체로 지정한 예
      ```kotlin
      public inline fun buildString(builderAction: StringBuilder.() -> Unit): String =
          StringBuilder().apply(builderAction).toString()    
      ```
    * 위 함수를 사용하면 람다 본문 안에서 아래와 같이 작성할 수 있다.
      ```kotlin
      buildString {
          append("Hi")
          append("Leopold")
      }    
      ```
    * 수신 객체 지정 람다의 형식 
      * ```수신 객체 타입.(파라미터 타입) -> 반환``` 
    * Kotlin으로 ```DSL```을 작성할 때 수신 객체 지정 람다는 중요한 개념
    <br>
 
  * **이름 충돌**
    * 수신 객체 람다 본문에서 ```this```로 접근하는 변수 또는 함수와 외부에 정의된 변수 및 함수 이름이 동일할 경우 ```this@레이블```을 통해 명시해야 한다. 
    * 애초에 ```@레이블```을 사용하기 전에 이름 충돌이 발생하지 않도록 하는 게 옳다고 생각한다.
    * ```@레이블```이 많아지면 가독성이 떨어진다. 
    ```kotlin
    fun alphabet() = with(StringBuilder()) {
      for (letter in 'A'..'Z') {
        append(letter)
      }
      
      append("\nNow I know the alphabet!")
      toString()
    }
    ```
    * 위의 예제 코드에서 ```toString()```을 호출하는 것처럼 외부에도 존재하는 메서드를 호출한다고 가정했을 시 수신 객체 지정 람다 안에서 ```this.```를 생략할 수 있기 때문에 보는 이로 하여금 일시적이나마 헷갈리게 할 수 있다. 
    
    <br>
 
  * **일반 람다와 수신 객체 지정 람다**
    * 일반 람다가 일반 함수라면
    * 수신 객체 지정 람다는 확장 함수의 개념으로 볼 수 있다.
    <br>
