# Kotlin의 기본 컬렉션과 시퀀스(Sequence)의 차이
  * 기본 컬렉션의 함수는 결과 컬렉션을 **즉시** 생성한다.
  * 시퀀스는 중간 임시 컬렉션을 사용하지 않고 컬렉션 연산을 연쇄할 수 있다.
  * 기본 컬렉션을 사용한 예제
    ```kotlin
    class Person(name: String, age: Int)
    val people = listOf(Person("Alice", 31), Person("Bob", 29))
    
    people.map(Person::name).filter { it.startsWith("A") }
    ```
    * 위 코드에서 ```map```과 ```filter```의 연쇄 호출은 리스트를 2개를 만들어낸다. 
    * 한 리스트는 ```filter```의 결과를 담고 다른 하나는 ```map```의 결과를 담는다.
    * **대량의 데이터**를 다룰 때 비효율적이다. 
    <br>
  
  * 위 코드를 시퀀스로 작성한 예제
    ```kotlin
    class Person(name: String, age: Int)
    val people = listOf(Person("Alice", 31), Person("Bob", 29))
    
    people.asSequence()
      .map(Person::name)
      .filter { it.startsWith("A") }
      .toList()
    ```
    * 전체 연산 결과는 동일하지만 시퀀스는 중간 결과를 저장하는 컬렉션이 생기지 않기 때문에 원소가 많은 경우 성능이 뛰어나다.
    * 시퀀스의 원소는 필요할 때 비로소 계산되며 중간 처리 결과를 저장하지 않고도 연쇄적으로 적용해서 효율적으로 계산을 수행할 수 있다. 
    <br>
    
  * 어떤 컬렉션이든 ```asSequence``` 확장 함수를 통해 시퀀스로 바꿀 수 있다.
  * 시퀀스로 변환이 아닌 시퀀스를 생성하고 싶다면 ```generateSequence``` 함수로 생성
  * 항상 시퀀스를 사용할 수는 없는데 시퀀스의 원소를 인덱스를 사용해 접근하는 등의 다른 API메소드가 필요할 경우 리스트로 변환해야 한다.
  <br>
  
# 언제 시퀀스(Sequence)를 사용해야 할까?  
  * 인덱스를 통한 원소 접근이 필요 없고
  * **대량의 데이터**를 다룰 때
  * 시퀀스는 **람다를 인라인 하지 않기 때문에** 크기가 작은 컬렉션은 오히려 일반 컬렉션이 더 성능이 나을 수도 있다.
  <br>
  
# 시퀀스의 중간(intermediate) 연산과 최종(terminal) 연산  
  * 중간 연산은 다른 시퀀스를 반환하고 최종 연산은 결과를 반환한다.
  * 중간 연산은 항상 지연 계산된다. 
  * 최종 연산이 없는 예제 (아무 내용도 출력되지 않는다)
    ```kotlin
    listOf(1, 2, 3, 4).asSequence()
      .map { print("map($it) "); it * it }
      .filter { print("filter($it) "); it % 2 == 0 }
    ```
  * 최종 연산이 적용된 예제 (예상대로 출력된다)
    ```kotlin
    listOf(1, 2, 3, 4).asSequence()
      .map { print("map($it) "); it * it }
      .filter { print("filter($it) "); it % 2 == 0 }
      .toList()
      
    // map(1) filter(1) map(2) filter(4) map(3) filter(9) map(4) filter(16)  
    ```
  * 최종 연산을 호출하면 미뤄뒀던 모든 계산이 수행된다.
  <br>
  
# 기본 컬렉션과 시퀀스의 연산 수행
  * ```map```과 ```filter```를 사용한 위 예제 코드에서 기본 컬렉션과 시퀀스의 연산 수행 순서의 차이가 있다.
  * **기본 컬렉션**
    * ```map``` 함수를 각 원소에 대해 먼저 수행해서 변환된 리스트를 가지고 그 리스트에 대해 다시 ```filter```를 수행한다. 
    * ```map``` 함수를 모든 원소에 적용하고 난 뒤 ```filter``` 함수를 수행하는 방식
    <br>
  * **시퀀스**
    * 각 원소에 대해 순차적으로 적용된다.
    * 각 원소별로 ```map``` 함수 -> ```filter``` 함수를 수행하는 방식
    <br>
  * 컬렉션 함수의 경우 호출 순서에 따라서 결과는 같아도 수행하는 변환의 전체 횟수는 달라질 수 있다.
  * ```map``` 함수를 먼저 호출하는 경우 모든 원소에 대해서 ```map``` 함수를 적용하게 되지만 ```filter``` 함수를 먼저 적용하면 한 번 걸러진 원소들에 대해서만 ```map``` 함수를 적용하면 된다. 
    ```kotlin
    class Person(name: String, age: Int)
    val people = listOf(Person("Alice", 31), Person("Bob", 29), Person("Leopold", 33))

    // map 함수를 먼저 호출 (모든 원소에 대해 map 함수가 적용된다)
    people.asSequence()
      .map(Person::name)
      .filter { it.age > 30 }
      .toList()

    // filter 함수를 먼저 호출 (걸러진 원소들에 대해서만 map 함수가 적용된다)
    people.asSequence()
      .filter { it.age > 30 }
      .map(Person::name)
      .toList()    
    ```
    <br>
    
 # 시퀀스를 Java8 스트림과 비교하면
  * 시퀀스는 병렬 실행 기능을 지원하지 않는다.
  
