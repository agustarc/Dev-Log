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
    
  * **backing 프로퍼티 이름**
    * 개념적으로 같은 프로퍼티이지만 하나는 클래스 외부에 공개하고 다른 하나는 내부 구현을 담을 때 private으로 선언하고 언더스코어로 작성한 뒤 공개용 프로퍼티의 getter로 할당한다.
      ```kotlin
      class C {
        private val _elementList = mutableListOf<Element>()

        val elementList: List<Element>
             get() = _elementList
      }
      ```
    <br>

 * **좋은 이름 선택**
   * 클래스의 이름은 명사 또는 명사구로 작성한다. ex) ```List```, ```PersonReader```
   * 메소드의 이름은 동사 또는 동사구로 작성한다. ex) ```close```, ```readPersons```
   * 메소드의 이름은 오브젝트를 변경하거나 새로운 오브젝트를 리턴하는 경우 이를 암시해야 한다.
   * 예를 들어, ```sort```는 해당 컬렉션 자체를 정렬하고 ```sorted```는 정렬된 컬렉션의 복사본을 반환하는 것이다.
   * 이름에 의미 없는 단어(```Manager```, ```Wrapper``` 등)를 사용하는 것은 피하자.
   * 이름에 약어를 사용하는 경우 두 개의 문자(```IOStream```)일 때는 둘 다 대문자로 표기하고 그보다 더 긴 경우(```XmlFormatter```, ```HttpInputStream```) 파스칼 표기법으로 작성하자. 
   <br>
   <br>

# Formatting
 * 이미 Java에서부터 쓰고 있던 컨벤션들이 많지만 놓치고 있던 것들도 있으니 정리하자.
 * 대부분의 경우 Java 코딩 컨벤션을 따른다.
 * 들여 쓰기는 4자리 공백을 사용하고 탭은 사용하지 않는다.
 * Curly braces ```{}```의 경우 구조가 시작되는 선의 끝에 opening curly brace ```{```를 위치시키고, closing curly brace ```}```는 수평으로 정렬된 별도의 라인에 놓는다. 
   ```kotlin
   if (elements != null) {
      for (element in elements) {
          // ...
      }
   }
   ```
 * Kotlin에서 세미콜론은 선택사항이기 때문에 줄 바꿈은 중요하다.
 * Kotlin 언어의 디자인은 Java 스타일의 ```braces```를 가정한 것으로, 다른 포맷 스타일을 사용하려고 하면 예상치 못한 동작에 직면할 수 있다.
   <br>
 
 * **가로 공백**
   * (```a + b```)처럼 이진 연산자 사이에 한 칸의 공백을 둔다.
   * 예외적으로 range to 연산자의 경우 공백을 두지 않는다. (```0..i```)
   * 단항 연산자 주변에는 공백을 두지 않는다. (```a++```)
   * (```if```, ```when```, ```for```, ```while```)과 같은 흐름 제어 키워드들과 여는 소괄호 사이에는 공백을 넣는다.
     ```kotlin
     if (condition) {

     }
     ```
   * 기본 생성자 선언과 메소드 선언의 여는 소괄호 앞에는 공백을 두지 않는다.
     ```kotlin
     class A(val x: Int)

     fun foo(x: Int) { ... }

     fun bar() {
         foo(1)
     }
     ```
   * ```(```, ```[```,의 뒤나 ```]```, ```)```,의 앞에 공백을 두지 않는다.
   * ```.```이나 ```?.``` 주변에 공백을 두지 않는다. ```foo.bar().filter { it > 2 }.joinToString(), foo?.bar()```
   * 주석 문자 뒤에는 공백을 둔다. ```// This is a comment```
   * 타입 파라미터를 선언할 때 사용하는 angle brackets ```<>``` 주변에 공백을 두지 않는다. ```class Map<K, V> { ... }```
   * 메소드 레퍼런스 호출 시 사용하는 ```::```사이에 공백을 두지 않는다. ```Foo::class, String::length```
   * ```Nullable``` 타입을 선언할 때 ```?``` 앞에 공백을 두지 않는다. ```String?```
   <br>
   <br>
 
 
# Documentation comments
<br>

# Avoiding redundant constructs
<br>

# Idomatic use of language features
<br>

# Coding conventions for libraries

