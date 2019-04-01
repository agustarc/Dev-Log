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
    * 일반적으로 클래스의 내용은 다음의 순서로 정렬된다. 
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

 * **콜론**
   * 아래 경우에 콜론 ```:``` 앞에 공백을 넣어라.
     * 타입과 슈퍼 타입을 분리할 때 
     * 동일한 클래스의 다른 생성자 또는 슈퍼 클래스의 생성자에게 위임할 때 
     * ```object``` 키워드 다음에
     <br>
   * 변수 선언과 그 타입을 분리할 때 콜론 ```:``` 앞에 공백을 두지 않는다. 
   * 콜론 ```:``` 뒤에는 항상 공백을 넣는다.
     ```kotlin
     abstract class Foo<out T : Any> : IFoo {
         abstract fun foo(a: Int): T
     }

     class FooImpl : Foo() {
         constructor(x: String) : this(x) { ... }

         val x = object : IFoo { ... } 
     }   
     ```
     <br>
     
 * **클래스 헤더 형식**
   * 파라미터가 몇 개 없는 기본 생성자의 경우 한 줄로 작성할 수 있다.
     ```kotlin
     class Person(id: Int, name: String)
     ```
   * 헤더가 긴 기본 생성자의 경우 들여 쓰기와 함께 라인으로 구분한다. 또한 닫는 소괄호 ```)```는 새로운 라인에 있어야 하고, 만약 상속을 사용하는 경우 슈퍼 클래스의 생성자 호출 및 인터페이스 구현은 닫는 소괄호 ```)```와 동일한 라인에 있어야 한다.
     ```kotlin
     class Person(
         id: Int,
         name: String,
         surname: String
     ) : Human(id, name) { ... }     
     ```
   * 다중 인터페이스를 구현하는 경우 슈퍼 클래스의 생성자를 먼저 닫는 소괄호 )에 같은 라인으로 위치시키고 각 인터페이스는 각각 다른 라인으로 위치해야 한다.  
     ```kotlin
     class Person(
         id: Int,
         name: String,
         surname: String
     ) : Human(id, name),
         KotlinMaker { ... }     
     ```
   * 긴 슈퍼 타입 목록을 가지는 클래스의 경우 콜론 : 다음에 각 타입별로 줄 바꿈 처리하고 가로 정렬을 맞춘다.
     ```kotlin
     class MyFavouriteVeryLongClassHolder :
         MyLongHolder<MyFavouriteVeryLongClass>(),
         SomeOtherInterface,
         AndAnotherOne {

         fun foo() { ... }
     }   
     ```
   * 클래스 헤더가 길 경우 클래스 헤더와 몸체를 명확히 구분하려면 위의 예와 같이 클래스 헤더 다음에 빈 라인을 두거나 아래와 같이 opening curly brace ```{```를 별도의 라인으로 둔다.
     ```kotlin
     class MyFavouriteVeryLongClassHolder :
         MyLongHolder<MyFavouriteVeryLongClass>(),
         SomeOtherInterface,
         AndAnotherOne 
     {
         fun foo() { ... }
     }     
     ```
     <br>

  * **제어자**
    * 여러 개의 제어자를 가질 경우 아래의 순서를 따른다.
      ```kotlin
      public / protected / private / internal
      expect / actual
      final / open / abstract / sealed / const
      external
      override
      lateinit
      tailrec
      vararg
      suspend
      inner
      enum / annotation
      companion
      inline
      infix
      operator
      data      
      ```
      <br>
      
    * 모든 어노테이션은 제어자 앞에 둔다.
      ```kotlin
      @Named("Foo")
      private val foo: Foo      
      ```
      <br>
             
  * **어노테이션 형식**
    * 어노테이션은 일반적으로 어노테이션이 부착되는 곳 이전 위치에 동일한 들여 쓰기로 라인을 구분하여 둔다.
      ```kotlin
      @Target(AnnotationTarget.PROPERTY)
      annotation class JsonExclude
      ```
    * 인자 없는 어노테이션의 경우 같은 라인에 위치할 수 있다.
      ```kotlin
      @JsonExclude @JvmField
      var x: String      
      ```
    * 인자 없는 한 개의 어노테이션만 선언할 경우 해당 어노테이션과 상응하는 선언과 동일한 라인에 위치할 수 있다.
      ```kotlin
      @Test fun foo() { ... }
      ```
      <br>
      
  * **파일 어노테이션**
    * 파일 어노테이션은 파일의 주석과, 패키지 선언 사이에 둔다.
    * 패키지가 아니라 파일을 대상으로 한다는 것을 강조하기 위해 패키지 선언과 공백 라인으로 분리한다.
      ```kotlin
      /** License, copyright and whatever */
      @file:JvmName("FooBar")

      package foo.bar      
      ```
      <br>
      
  * **함수 형식**
    * 함수의 시그니처를 한 줄로 표현하기 알맞지 않다면 아래와 같은 구문을 따른다.
      ```kotlin
      fun longMethodName(
          argument: ArgumentType = defaultValue,
          argument2: AnotherArgumentType
      ): ReturnType {
          // body
      }    
      ```
    * 파라미터는 4칸 공백으로 들여 쓰기 한다. 
    * 함수가 단일 표현식으로 구성되어있을 경우 ```{}```과 ```return```을 제거하고 ```=```로 표현하는 방식이 좋다.
      ```kotlin
      fun foo(): Int {     // bad
          return 1 
      }

      fun foo() = 1        // good    
      ```
    * 타입 추론에 의해 타입을 생략하는 경우도 있지만 리턴 타입의 경우 타입을 명시하는 것이 좋다고 생각한다. 리턴 값의 구조가 복잡해질수록 코드를 읽는 사람이 직접 추론해나가는 과정이 필요하다. 
    <br>
    
  * **표현식 본체 형식**
    * 만약 표현식 본체가 한 줄로 표현하기 알맞지 않다면 첫 줄에 ```=``` 와 다음 줄에 표현식 본체를 4칸 공백 들여 쓰기로 작성한다.
      ```kotlin
      fun f(x: String) =
      x.length
      ```
      <br>
      
  * **프로퍼티 형식**
    * 매우 간단한 ```Read-only``` 프로퍼티의 경우 한 줄로 작성하는 것을 고려하자.
      ```kotlin
      val isEmpty: Boolean get() = size == 0
      ```
    * 좀 더 복잡한 프로퍼티의 경우 ```get``` 과 ```set``` 을 분리된 라인에 두 자.
      ```kotlin
      val foo: String
          get() { ... }      
      ```
    * 초기화 코드가 있는 프로퍼티의 경우, 초기화 코드가 길다면 줄바꿈 처리하여 4칸 공백 들여 쓰기 한다.
      ```kotlin
      private val defaultCharset: Charset? =
          EncodingRegistry.getInstance().getDefaultCharsetForPropertiesFiles(file)      
      ```
      <br>
      
  * **제어문 형식**
    * ```if```나 ```when```과 같은 조건문이 멀티라인일 경우 항상 opening curly brace ```{```를 실행 구문에 가깝게 둔다.
    * 각 조건문 라인은 4칸 공백으로 들여쓰기 한다.
    * 조건문의 닫는 소괄호와 함께 여는 opening curly brace ```{```사이에 공백을 둔다.
      ```kotlin
      if (!component.isSyncing &&
          !hasAnyKotlinRuntimeInScope(module)
      ) {
          return createKotlinNotConfiguredPanel(module)
      }    
      ```
    * ```else```, ```catch```, ```finally``` 키워드뿐 아니라 ```do/while``` 루프에서의 ```while``` 키워드까지 이전 curly brace와 동일한 라인에 둔다.
      ```kotlin
      if (condition) {
          // body
      } else {
          // else part
      }

      try {
          // body
      } finally {
          // cleanup
      }      
      ```
    * ```when``` 제어문에서 분기문이 한 줄 이상일 경우 인접한 case 블록과 빈 라인으로 구분하는 것을 고려하라. 
      ```kotlin
      private fun parsePropertyValue(propName: String, token: Token) {
          when (token) {
              is Token.ValueToken ->
                  callback.visitValue(propName, token.value)

              Token.LBRACE -> { // ...
              }
          }
      }    
      ```
    * 짧은 분기문의 경우 braces 없이 조건과 동일한 라인에 둔다.
      ```kotlin
      when (foo) {
          true -> bar() // good
          false -> { baz() } // bad
      }      
      ```
      <br>
  * **메소드 호출 형식**
    * 긴 인자 목록을 가지는 메소드를 호출할 때, 여는 소괄호 다음 줄바꿈을 한 뒤 인자들을 4칸 공백으로 들여 쓰기 한다.
    * 밀접하게 관련된 인자끼리 그룹핑한다.
      ```kotlin
      drawSquare(
          x = 10, y = 10,
          width = 100, height = 100,
          fill = true
      )    
      ```
    * 인자의 이름과 값을 구분하는 ```=``` 주변에 공백을 둔다. 
    <br>
  * **체이닝 호출 형식**
    * 체이닝 형태로 호출할 때는 ```.``` 또는 ```?.```는 들여 쓰기와 함께 개행처리한다. 
      ```kotlin
      val anchor = owner
          ?.firstChild!!
          .siblings(forward = true)
          .dropWhile { it is PsiComment || it is PsiWhiteSpace }    
      ```
    * 체이닝의 첫 번째 호출은 보통 그 이전에 개행을 포함하지만, 코드가 그런 식으로 말이 된다면 생략해도 된다. 
    <br>    
  * **람다 형식**
    * 람다 표현식에서 curly braces ```{}``` 주변에는 공백을 두어야 한다.
    * 파라미터를 나타내는 화살표 ```->``` 주변에도 공백을 두어야 한다.
    * 호출에 사용된 람다가 한 개인 경우 가능한 한 소괄호```()```를 제거해야 한다.
      ```kotlin
      list.filter { it > 10 }
      ```
    * 만약 람다에 ```label```을 할당한다면 ```label```과 opening curly brace ```{``` 사이에 공백을 두지 않는다.
      ```kotlin
      fun foo() {
          ints.forEach lit@{
              // ...
          }
      }    
      ```
    * 다중 라인 람다에서 파라미터의 이름을 선언할 때, 첫 번째 줄에 파라미터 이름과 화살표 ```->``` 를 선언하고 개행처리한다.
      ```kotlin
      appendCommaSeparated(properties) { prop ->
          val propertyValue = prop.get(obj)  // ...
      }    
      ```
    * 만약 파라미터 목록이 한 줄로 표현하기 적합하지 않다면 파라미터 목록과 화살표 ```->``` 를 들여 쓰기와 함께 개행처리한다.
      ```kotlin
      foo {
         context: Context,
         environment: Env
         ->
         context.configureEnv(environment)
      }    
      ```
      <br>
      <br>

# Documentation comments
 * 주석의 내용이 길 경우 ```/**``` 을 시작으로 각 라인은 ```*```으로 시작한다.
   ```kotlin
   /**
    * This is a documentation comment
    * on multiple lines.
    */ 
   ```
 * 짧은 주석의 경우 한 줄로 표현될 수 있다.
   ```kotlin
   /** This is a short documentation comment. */
   ```
 * 일반적으로 ```@param```과 ```@return``` 태그는 사용하지 마라.
 * 대신, 파라미터 및 반환 값에 대한 설명을 주석 내용에 직접 포함시키고, 언급된 모든 파라미터에 링크를 추가한다.
 * 주석 내용의 흐름에 맞지 않는 장황한 설명이 필요한 경우에만 ```@param``` 및 ```@return```을 사용해라.
   ```kotlin
   // Avoid doing this:

   /**
    * Returns the absolute value of the given number.
    * @param number The number to return the absolute value for.
    * @return The absolute value.
    */
   fun abs(number: Int) = ...

   // Do this instead:

   /**
    * Returns the absolute value of the given [number].
    */
   fun abs(number: Int) = ... 
   ```
   <br>
   <br>

# Avoiding redundant constructs
 * Kotlin에서 특정 문법 구조가 선택사항이고 IDE에 의해 불필요하다고 강조된 경우 생략할 수 있다.
 * 명확성을 위해 불필요한 문법적 요소를 남겨두지 마라.
 * **Unit**
   * 함수가 ```Unit```을 반환한다면 반환 타입은 생략되어야 한다.
   ```kotlin
   fun foo() { // ": Unit" is omitted here

   }   
   ```
   <br>
 * **세미콜론**  
   * 세미콜론은 가능한 생략해야 한다.
   <br>
 * **문자열 템플릿**
   * 간단한 변수 하나를 문자열에 템플릿에서 참조할 땐 curly braces ```{}```를 사용하지 마라.
   * curly braces ```{}```는 긴 표현식에서만 사용해라.
     ```kotlin
     println("$name has ${children.size} children")   
     ```
     <br>
     <br>
     
# Idomatic use of language features
 * **불변성**
   * Immutable 컬렉션을 선언할 때는 항상 ```Collection```, ```List```, ```Set```, ```Map```과 같은 인터페이스를 사용해라.
   * 컬렉션 인스턴스를 생성할 때 가능한 Immutable 컬렉션 타입으로 생성해라.
     ```kotlin
     // Bad: use of mutable collection type for value which will not be mutated
     fun validateValue(actualValue: String, allowedValues: HashSet<String>) { ... }

     // Good: immutable collection type used instead
     fun validateValue(actualValue: String, allowedValues: Set<String>) { ... }

     // Bad: arrayListOf() returns ArrayList<T>, which is a mutable collection type
     val allowedValues = arrayListOf("a", "b", "c")

     // Good: listOf() returns List<T>
     val allowedValues = listOf("a", "b", "c") 
     ```
   * 기본적으로 Kotlin에서 ```List```는 Read-only
   <br>
 * **기본 인자 값**
   * 오버 로딩을 선언하기 위해 기본 인자 값을 가지는 함수를 선언하는 것이 좋다.
     ```kotlin
     // Bad
     fun foo() = foo("a")
     fun foo(a: String) { ... }

     // Good
     fun foo(a: String = "a") { ... }   
     ```
     <br>
 * **타입 별칭**
   * 코드베이스에서 여러 번 사용되는 함수 타입이나 타입 파라미터의 경우 ```type alias```를 사용하는 것이 좋다.
   ```kotlin
   typealias MouseClickHandler = (Any, MouseEvent) -> Unit
   typealias PersonIndex = Map<String, Person>   
   ```
   <br>
 * **람다 파라미터**
   * 양이 짧고 중첩되지 않는 람다 구조에서는 명시적인 파라미터 선언보다 ```it```을 사용하는 것을 추천한다.
   * 파라미터가 있는 중첩된 람다 구조에서는 파라미터를 명시적으로 선언해야 한다.
   <br>
 * **람다에서의 return**
   * 람다 안에서 ```label```이 붙은 ```return```을 여러 개 사용하는 것을 피해라.
   * 하나의 탈출 포인트를 갖도록 구조를 바꾸는 것을 고려해라.
   * 만약 그것이 불가능하거나 충분히 명확하지 않다면, 람다를 익명 함수로 변환하는 것을 고려해라.
   * 람다의 마지막 문장에서 ```label```이 붙은 ```return```을 사용하지 마라.
   <br>
 * **이름있는 인자**
   * 동일한 ```primitive``` 타입의 파라미터가 여러 개이거나 또는 ```Boolean``` 타입일 때 이름있는 인자를 사용해라.
   ```kotlin
   drawSquare(x = 10, y = 10, width = 100, height = 100, fill = true)   
   ```
   <br>
 * **조건문 사용**
   * ```try```, ```if```, ```when```은 아래와 같은 표현식을 사용하는 것이 좋다.
     ```kotlin
     return if (x) foo() else bar()

     return when(x) {
         0 -> "zero"
         else -> "nonzero"
     }   
     ```
   * 아래 코드보다 위의 코드가 더 낫다.
     ```kotlin
     if (x)
         return foo()
     else
         return bar()

     when(x) {
         0 -> return "zero"
         else -> return "nonzero"
     }   
     ```
     <br>
  * **if vs when**
    * 조건이 2개라면 ```when```보다 ```if```를 사용해라.
      ```kotlin
      when (x) {
          null -> ...
          else -> ...
      }    
      ```
    * 위의 코드보다 ```if (x == null) ... else ...```를 사용해라.
    * ```when```은 조건이 3개 이상일 때나 더 많은 선택지가 있을 때 사용해라. 
    <br>
  * **조건문에서 Nullable Boolean 값 사용**
    * 만약 조건문에서 ```Nullable Boolean``` 값을 사용해야 한다면 ```if (value == true)``` 또는 ```if (value == false)```로 체크해라.
    <br>
  * **루프 사용**
    * 루프에는 ```filter```, ```map```과 같은 고차 함수를 사용하는 것이 좋다.
    * 예외적으로 ```forEach```의 경우 ```forEach```의 리시버가 ```nullable```이거나 ```forEach```가 긴 호출 체인의 일부로 사용되는 경우가 아니라면 일반적인 ```for``` 루프가 더 좋다.
    * 복수의 고차 함수를 이용한 복잡한 표현과 루프를 선택할 때에는 각 상황에서 실행되는 operation들의 비용을 이해해야 하고 성능 고려 사항들을 유념해라.
    <br>
  * **범위 루프**
    * 루프에서 범위를 지정할 때 ```until```을 사용해라.
      ```kotlin
      for (i in 0..n - 1) { ... }  // bad
      for (i in 0 until n) { ... }  // good    
      ```
      <br>
  * **함수 vs 프로퍼티**
    * 몇몇 경우에 인자 없는 함수는 Read-only 프로퍼티로 교체될 수 있다.
    * 의미론적으로는 비슷할지라도 언제 어느 것을 더 선호할 것인가에 대한 몇몇 양식적인 관습들이 있다.
    * 아래 알고리즘의 경우 함수보다 프로퍼티가 좋다.
      * ```throw```를 하지 않음
      * 계산이 저렴하거나 한 번 실행 후 캐시 됨
      * 객체의 상태가 변경되지 않은 경우 호출에 대해 동일한 결과를 반환
      <br>
  * **확장 함수 사용**
    * 확장 함수를 자유롭게 사용해라.
    * 주로 객체에 대해 동작하는 함수가 있을 때마다 해당 객체를 리시버로 받는 확장 함수를 만드는 것을 고려해라.
    * API 공해를 최소화하기 위해 확장 함수의 가시성을 최대한 제한하라.
    * 필요에 따라 ```private``` 가시성을 가지는 로컬 확장 함수, 멤버 확장 함수, Top-level 확장 함수를 사용해라.
    <br>
  * **infix 함수 사용**
    * 유사한 역할을 수행하는 두 객체에서 동작할 때만 ```infix```로 선언해라.
    * 좋은 예제 : ```and```, ```to```, ```zip```
    * 나쁜 예제 : ```add```
    * 리시버 객체를 변형시키는 경우 메소드를 ```infix```로 선언하지 마라.
    <br>
  * **팩토리 함수**
    * 클래스의 팩토리 함수를 선언한다면 클래스와 동일한 이름을 피해라.
    * 팩토리 함수의 동작이 왜 특별한지 명확하게 하기 위해 고유 이름을 사용해라.
    * 정말 특별한 의미가 없는 경우에만 클래스와 동일한 이름을 사용할 수 있다.
      ```kotlin
      class Point(val x: Double, val y: Double) {
          companion object {
              fun fromPolar(angle: Double, radius: Double) = Point(...)
          }
      }    
      ```
    * 만약 여러 개의 오버 로딩된 생성자를 가진 객체가 서로 다른 슈퍼 클래스 생성자를 호출하지 않고 기본 인자 값을 가진 단일 생성자로 축소할 수 없는 경우 오버 로딩된 생성자를 팩토리 함수로 교체하는 것이 좋다.
    <br>
    <br>

# Coding conventions for libraries
  * 라이브러리를 작성할 때 API의 안전성을 보장하기 위해 아래의 추가적인 규칙들을 따르는 것이 좋다.
    * 항상 멤버의 가시성을 명시적으로 지정해라(의도치 않게 ```public``` API로 선언되어 노출되지 않도록) 
    * 항상 함수의 반환 타입과 속성 타입을 명시적으로 지정해라(구현 변경 시 의도치 않게 반환 타입이 변경되지 않도록)
    * 새로운 주석이 필요 없는 ```override``` 된 것들을 제외하고 모든 ```public``` 멤버에 대한 주석을 제공해라. (라이브러리 문서 생성을 지원하기 위해)
