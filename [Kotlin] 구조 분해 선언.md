# 구조 분해 선언(destructuring declaration)
  * **일반 클래스에 구조 분해 선언**
    * 때때로 객체를 아래와 같이 여러 변수에 분리해서 할당하는 것이 편리할 때가 있다.
      ```kotlin
      val (name, age) = person
      ```
    * Kotlin의 관례 규칙에 따라 ```componentN()```으로 operator를 정의하면 구조 분해 선언을 사용할 수 있다.
    * 위와 같은 구조 분해를 사용하기 위해 ```componentN``` operator 정의
      ```kotlin
      class Person(val name: String, val age: Int) {
        operator fun component1() = name
        operator fun component2() = age
      }
      ```
    * 위와 같이 구조 분해 선언을 정의하고 변수에 대입하면 아래와 같은 자바 코드가 생성된다.
      ```java
      String name = person.component1();
      int age = person.component2();
      ```
      <br>
      
  * **data class는 컴파일러가 자동으로 componentN 함수를 만들어준다**
    * ```data class```는 주 생성자에 들어있는 프로퍼티에 대해서 ```componentN``` 함수를 자동으로 만들어준다.
    * 그러므로 ```data class```의 주 생성자에 들어있는 프로퍼티는 별도의 ```componentN``` 함수 정의 없이 구조 분해 선언 사용 가능
    * 하지만 주 생성자의 프로퍼티가 아닌 경우엔 ```componentN``` 함수를 생성해주지 않는다.
    * ```data class```에 ```componentN```을 직접 구현하는 것을 허용하지 않는다.
    <br>
    
  * **표준 라이브러리의 Pair나 Triple 클래스**
    * 함수의 반환값으로 여러 개의 데이터를 반환해야 할 때 그 개수가 2개나 3개인 경우 ```Pair```나 ```Triple```을 사용할 수도 있다.
    * ```Pair```와 ```Triple```은 그 안에 담겨있는 원소의 의미를 말해주지 않으므로 경우에 따라 가독성이 떨어질 수 있다.
    * 하지만 직접 클래스를 작성할 필요가 없으므로 코드가 단순해진다.
    <br>
    
  * **함수에서 어떤 값을 반환할 때 그 값이 여러 개라면**
    * 반환 값이 2개이고 가독성에 문제가 없다면 ```Pair```
    * 반환 값이 3개이고 가독성에 문제가 없다면 ```Triple```
    * 가독성이 중요하고 오직 데이터를 담는 역할만 한다면 ```data class```
    * 가독성이 중요하고 데이터뿐 아니라 특정 기능을 수행한다면 일반 ```class```에 ```componentN``` operator 정의
    <br>
    
  * **분해 선언과 맵**
    * 맵을 순회할 때 다음과 같이 구조 분해 선언을 사용할 수 있다.
      ```kotlin
      for ((key, value) in map) {

      }    
      ```
    * 위와 같이 맵에 대한 구조 분해가 가능한 이유는 아래와 같이 ```Map.Entry```에 대한 확장 함수가 정의되어 있기 때문이다.
      ```kotlin
      operator fun <K, V> Map<K, V>.iterator(): Iterator<Map.Entry<K, V>> = entrySet().iterator() 
      operator fun <K, V> Map.Entry<K, V>.component1() = getKey()
      operator fun <K, V> Map.Entry<K, V>.component2() = getValue()    
      ```
      <br>
    
  * **사용하지 않는 변수**
    * 구조 분해 선언에서 사용하지 않는 변수는 ```_```로 사용할 수 있다.
    ```kotlin
    val (_, age) = person
    ```
    <br>

  * **람다에서의 구조 분해 선언**
    * 람다 파라미터에도 구조 분해 선언을 사용할 수 있다.
    * 람다의 파라미터가 ```Pair```, ```Triple```, ```Map.Entry```, 또는 적당한 ```componentN``` 함수를 가진 다른 타입을 가질 경우 분해된 파라미터를 사용할 수 있다.
      ```kotlin
      map.mapValues { entry -> "${entry.value}!" } 
      map.mapValues { (key, value) -> "$value!" }    
      ```
    * 두 파라미터를 선언하는 것과 한 개 파라미터 대신에 분리한 쌍을 선언하는 것의 차이
      ```kotlin
      { a -> ... } 
      { a, b -> ... }
      { (a, b) -> ... }
      { (a, b), c -> ... }
      ```
    * 분리한 파라미터 전체 타입이나 일부 컴포넌트의 타입을 개별적으로 지정할 수 있다
      ```kotlin
      map.mapValues { (_, value): Map.Entry<Int, String> -> "$value!" }
      map.mapValues { (_, value: String) -> "$value!" }
      ```
