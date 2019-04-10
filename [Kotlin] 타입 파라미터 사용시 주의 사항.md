# null에 엄격한 Kotlin의 타입 시스템에서 NPE를 발생시킬 수 있는 타입 파라미터
  * Kotlin에서 타입의 상한 없이 타입 파라미터만 선언할 경우 ```Nullable``` 한 타입이 될 수 있다.
  * 타입의 상한 없이 단순히 타입만 정의한 예제
    ```kotlin
    val processor = Processor<String?>()
    processor.process(null)

    class Processor<T> {
      fun process(value: T) {
          value.hashCode() 
          // value가 null이지만 hashCode()가 null에 안전한 확장 함수로 정의되어 있어 이 코드에서 NPE가 발생하지 않지만 실제 코드에선 얼마든지 발생할 수 있다.
      }
    }
    ```
  * ```Processor```의 제네릭 타입 파라미터로 ```T```는 ```Any``` 또는 ```Any?``` 가 될 수 있다.
  <br>
  
  * 만약 아래와 같이 작성한다면 ```null```을 인자로 넘길 수 없을 것이다.
    ```kotlin
    val processor = Processor<String>()
    processor.process(null) // compile error

    class Processor<T> {
      fun process(value: T) {
          value.hashCode()
      }
    }
    ```  
  * 파라미터로 ```null```을 전달하는 코드 작성은 지양해야 하기 때문에 애초에 ```null```을 전달할 수 없게 만들어야 한다. 
  <br>
  
  * 위와 같은 위험을 피하기 위해 아래와 같이 타입의 상한을 지정해야 한다. 
    ```kotlin
    class Processor<T : Any> {
      fun process(value: T) {
          value.hashCode() 
      }
    }
    ```
    <br>
    <br>
    
# 설계된 동작일까 아니면 의도치 않은 결과일까?
  * 만약 타입 파라미터 ```T```를 ```Nullable``` 하게 선언할 수 있게 한다면?
    * 안 그래도 복잡한 제네릭에 복잡성만 가중시킬 수 있다.
    * ```<T? : Any?>``` 이런 이상한 선언이 조합될 수 있다.
    <br>
    
  * 만약 타입 상한 없이 타입 파라미터를 선언하면 자동으로 ```Any```를 상한으로 지정한다면?
    * 타입 파라미터를 무조건 ```Non-nullable```로만 선언할 수 있게 된다.
    * 타입 상한으로 ```Any``` 또는 ```Any?```를 선택할 수 없다.
    <br>
    
  * 타입 상한으로 ```Nullable```을 지정할 수 있게 설계한 이유가 뭘까?
    * 아래와 같이 컬렉션에 ```null```이 담길 수 있는 상황에서 ```null```에 안전한 처리를 위해서였을 수도..
    ```kotlin
    class Forest<T : Tree?>(private val list: List<T>) {
        val last: T?
            get() = list.lastOrNull()
    }    
    ```
    
    
    
