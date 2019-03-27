# Source code organization
  * **디렉터리 구조**
    * Java와 Kotlin이 섞인 프로젝트라면 Kotlin과 Java 파일은 동일한 소스 루트에 있어야 한다.
    * 각 파일은 각 패키지 문과 일치하는 디렉터리에 저장되어야 한다.
    * 순수 Kotlin 프로젝트에서 권장하는 디렉터리 구조는 공통 루트 패키지가 생략된 패키지 구조를 따르는 것이다.
    <br>
    
  * **소스 파일 이름**
    * Kotlin 파일에 Top-level로 단일 클래스가 선언된 경우 ```.kt``` 확장자와 함께 클래스의 이름과 동일해야 한다.
    * 파일에 여러 클래스가 포함되어 있거나 Top-level 선언들만 있는 경우 파일의 내용들을 설명하는 이름으로 작성한다.
    * 파일 이름은 파스칼 표기법(```Pascal Case```)을 따를 것 = ```Upper Camel Case```
    * 파일 이름은 파일에 있는 코드가 무엇을 하는지 설명해야 한다.
    * 따라서 파일 이름에 ```"Util"```과 같은 의미 없는 단어를 사용하는 것은 피해야 한다.
    <br>
    
  * **소스 파일 구조**
    * 클래스, 최상위 함수 또는 속성을 동일한 Kotlin 소스 파일에 배치하는 것은 의미적으로 서로 밀접하게 관련되어 있고 파일 사이즈가 합리적인 경우(수백 줄을 초과하지 않는 함) 권장된다.
    * 클래스의 모든 클라이언트와 관련이 있는 확장 함수를 정의할 때 클래스가 정의되어 있는 동일한 파일에 넣어라.
    * 특정 클라이언트에만 이치에 맞는 확장 함수를 정의할 때는 해당 클라이언트의 코드 옆에 위치시킨다.
    * 모든 ```Foo```의 확장만을 보관하기 위해 파일을 만들지 말라.
    <br>
    
  * **클래스 레이아웃**
    * 일반적으로 클래스 내부의 순서는 다음을 따른다. 
      * Property declarations and initializer blocks
      * Secondary constructors
      * Method declarations
      * Companion object
      <br>
      
    * 메소드 선언을 알파벳순 또는 가시성으로 정렬하지 말고 일반 메소드와 확장 메소드의 구분을 하지 마라.
    * 대신, 관련된 것들을 한데 모아 클래스를 읽는 사람이 위에서 아래로 무슨 일이 일어나고 있는지 로직을 따라갈 수 있도록 해라.
    * 중첩 클래스들은 해당 클래스를 사용하는 코드 다음에 위치시킨다.
    * 중첩 클래스들이 외부에서 사용되도록 의도되었고 클래스 내부에서 참조되지 않는다면 ```companion object``` 다음의 마지막에 위치시켜라.
    <br>
    
  * **인터페이스 구현 레이아웃**
    * 인터페이스를 구현할 때 멤버들의 순서는 인터페이스의 선언 순서와 동일하게 유지해라. 
    <br>

# Naming rules
  * Kotlin은 Java의 네이밍 룰을 따른다. 
  * 패키지명은 항상 소문자로 작성하고 언더스코어는 사용하지 않는다.
  * 여러 단어로 된 이름을 사용하는 것은 일반적으로 금지되지만, 필요하다면 단순히 그것들을 연결하거나 카멜 표기법을 사용할 수 있다. (```org.example.myProject```)
  * 클래스나 오브젝트의 이름은 파스칼 표기법을 따른다.
    ```kotlin
    open class DeclarationProcessor { ... }

    object EmptyDeclarationProcessor : DeclarationProcessor() { ... }
    ```
  <br>
  
  * **함수 이름**
    * 함수, 프로퍼티, 로컬 변수의 이름은 소문자로 시작하며 카멜 표기법을 따르고 언더스코어는 사용하지 않는다.
      ```kotlin
      fun processDeclarations() { ... }
      var declarationCount = ...
      ```
    * 예외적으로 클래스의 인스턴스를 생성하는 팩토리 함수의 경우 클래스 이름과 동일할 수 있다.
      ```kotlin
      abstract class Foo { ... }

      class FooImpl : Foo { ... }

      fun Foo(): Foo { return FooImpl(...) }
      ```
      <br>
  
  * **테스트 메소드 이름**
    * ```backtick```으로 감싸서 공백을 포함한 이름을 작성할 수 있다.
    * 유의할 점은 아직 Android 런타임에서는 지원되지 않는다.
    * 메소드 이름에 언더스코어도 허용된다.
      ```kotlin
      class MyTestCase {
        @Test fun `ensure everything works`() { ... }

        @Test fun ensureEverythingWorks_onAndroid() { ... }
      }
      ```
      <br>
      
  * **프로퍼티 이름**
    * ```const```로 선언된 프로퍼티나 Top-level 또는 ```object```에 선언된 ```val```은 언더스코어로 구분하여 대문자로 표기해야 한다.
      ```kotlin
      const val MAX_COUNT = 8
      val USER_NAME_FIELD = "UserName"
      ```
    * Top-level 또는 ```object```에 선언된 프로퍼티 중 동작이나 ```Mutable``` 데이터가 있는 객체를 다룰 경우 카멜 표기법을 따른다.
      ```kotlin
      val mutableCollection: MutableSet<String> = HashSet()
      ```
    * 싱글톤 오브젝트에 대한 레퍼런스를 가지는 프로퍼티의 경우 ```object``` 선언과 동일한 네이밍 스타일을 사용할 수 있다.
      ```kotlin
      val PersonComparator: Comparator<Person> = ...
      ```
    * ```Enum```의 경우 사용법에 따라 언더스코어로 구분된 대문자로 사용하거나 파스칼 표기법을 사용해도 괜찮다.
  
<br>

# Formatting
<br>

# Documentation comments
<br>

# Avoiding redundant constructs
<br>

# Idomatic use of language features
<br>

# Coding conventions for libraries

