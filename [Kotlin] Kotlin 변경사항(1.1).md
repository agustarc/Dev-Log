# 타입 별명 
  * 기존 타입에 대한 별명(alias)을 만드는 기능
  * 최상위 수준에서만 ```typealias```를 사용할 수 있다.
  * 콜백 함수 타입에 대한 타입 별명
    ```kotlin
    typealias MyHandler = (Int, String, Any) -> Unit
    ```
  * MyHandler를 받는 고차 함수
    ```kotlin
    fun addHandler(h: MyHandler) { ... }
    ```
  * 컬렉션의 인스턴스에 대한 타입 별명
    ```kotlin
    typealias Args = Array<String>
    fun main(args: Args) { ... }
    ```
  * 제네릭 타입 별명
    ```kotlin
    typealias StringKeyMap<V> = Map<String, V>
    val myMap: StringKeyMap<Int> = mapOf("One" to 1, "Two" to 2)
    ```
  * 중첩 클래스
    ```kotlin
    class Foo {
      class Bar {
        inner class Baz
      }      
    }

    typealias FooBarBaz = Foo.Bar.Baz
    ```
    <br>
    <br>
    
# sealed 클래스와 data 클래스   
  * 1.1부터 ```sealed class```의 하위 클래스를 ```sealed class``` 내부에 정의할 필요가 없고 같은 소스 파일 안에만 정의하면 됨
  * ```data class```로 ```sealed class```를 확장하는 것도 가능해졌다.
    ```kotlin
    sealed class Expr
    data calss Num(val value: Int) : Expr()
    data class Sum(val left: Expr, val right: Expr) : Expr()
    ```
    <br>
    <br>
    
# 람다 파라미터에서 구조 분해 사용
  * 구조 분해 선언을 람다의 파라미터 목록에서도 사용할 수 있다.
  ```kotlin
  val nums = listOf(1, 2, 3)
  val names = listOf("One", "Two", "Three")
  
  (nums zip names).forEach { (num, name) -> println("${num} = ${name}") }
  
  // 결과
  1 = One
  2 = Two
  3 = Three
  ```
  <br>
  <br>
  
# 밑줄(_)로 파라미터 무시
  * 람다를 정의할 때 여러 파라미터 중 사용하지 않는 파라미터가 있다면 ```_```을 그 위치에 넣으면 따로 이름을 붙이지 않아도 된다.
  * 구조 분해 시에도 관심이 없는 값을 ```_```로 무시할 수 있다.
  <br>
  <br>
  
# 식이 본문인 게터만 있는 읽기 전용 프로퍼티의 타입 생략
  * 읽기 전용 프로퍼티를 정의할 때 게터의 본문이 식이라면 타입을 생략해도 컴파일러가 타입을 추론해준다.
    ```kotlin
    data class Foo(val value: Int) {
      val double get() = value * 2
    }
    ```
    <br>
    <br>
    
# 프로퍼티 접근자 인라이닝
  * 1.1부터 프로퍼티 접근자도 ```inline```으로 선언할 수 있다.
  * 게터, 세터 모두 인라이닝이 가능
  * 일반 멤버 프로퍼티뿐 아니라 확장 멤버 프로퍼티나 최상위 프로퍼티도 인라이닝이 가능하다.
  * 하지만 ```backing 필드```가 있으면 그 프로퍼티의 게터나 세터를 인라이닝할 수는 없다.
    ```kotlin
    val topLevel: Double
      inline get() = Math.PI // 게터 인라이닝

    class InlinePropExample(var value: Int) {
      var setOnly: Int
      get() = value
      inlint set(v) { value = v} // 세터 인라이닝

      // 컴파일 오류: Inline property cannot have backing field
      val backing: Int = 10
      inline get() = field * 1000

    inline var InlinePropExample.square: Int // 게터 세터 인라이닝
      get() = value * value
      set(v) { value = Math.sqrt(v.toDouble()).toInt() }
    ```
    <br>
    <br>
    
# 제네릭 타입으로 Enum 값 접근
  * 제네릭 파라미터로 쓰이는 ```Enum```의 모든 ```Enum``` 값을 이터레이션하거나 값을 찾아봐야 하는 경우가 있다.
  * 이럴 경우 인라인 제네릭 함수 안에서 ```reified``` 타입을 활용해 ```Enum```에 제네릭하게 접근할 수 있다.
  * ```Enum```의 값을 사용하고 싶으면
    ```kotlin
    public inline fun <reified T : Enum<T>> enumValues(): Array<T>
    ```
  * 역으로 이름에서 값을 가져오고 싶으면
    ```kotlin
    public inline fun <reified T : Enum<T>> enumValueOf(name: String): T
    ```
    <br>
    <br>
    
# DSL의 수신 객체 제한
  * 수신 객체 지정 람다를 사용한 빌더 DSL에서 내부 람다는 외부 람다의 묵시적 수신 객체에 정의된 메소드를 자유롭게 사용할 수 있다.
  * 어휘 범위 규칙에 따라 내부 람다의 ```this```에 정의되지 않은 메소드를 외부 람다의 ```this```에서 찾기 때문이다.
  * 1.1부터는 ```@DslMarker``` 애노테이션을 통해 수신 객체 지정 람다 안에서는 맨 안쪽의 묵시적 ```this```만 사용할 수 있고 나머지는 ```this@레이블``` 형태로 명시적으로 사용해야 된다.
  ```kotlin
  @DslMarker
  annotation class HtmlTagMarker
  
  @HtmlTagMarker
  abstract class Tag(val name: String) { ... }
  
  class Table() : Tag("table") { ... }
  class Tr() : Tag("tr") { ... }
  
  table {
    tr {
      // 이 위치에서는 'Tr'에 정의된 메소드만 묵시적 사용이 가능
      tr { ... } // 컴파일 안 됨 'Table' 클래스에 정의된 메소드는 이 위치에서 사용 불가
      this@table.tr { ... } // 명시적으로는 사용 가능
    }
  }
  ```
  <br>
  <br>
  
# 로컬 변수 등을 위임
  * 클래스나 객체의 프로퍼티 외에 로컬 변수나 최상위 수준의 변수에도 위임을 사용할 수 있다.
  ```kotlin
  import kotlin.reflect.KProperty
  
  class BarDelegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): Int {
      println("thisRef = ${thisRef}")
      println("property.name = ${property.name}")
      return 100
    }
  }
  
  val y:Int by BarDelegate()
  
  fun main(args: Array<String>) {
    val x:Int by BarDelegate()
    println("x = ${x} y = ${y}")
  }
  ```
  * 로컬 변수를 위임한 경우 위임 객체의 ```getValue```나 ```setValue```로 전달되는 ```KProperty```타입의 값에 있는 여러 메소드 중에 ```name```을 제외한 나머지는 실행 시 UnsupportedOperationException을 발생시킴
  <br>
  <br>
  
# 위임 객체 프로바이더
  * ```provideDelegate()``` 연산자를 제공하는 객체를 ```by``` 다음에 쓰면 해당 프로퍼티가 맨 처음 객체와 연결될 때마다 매번 새로운 위임 객체를 만들어내서 사용할 수 있다.
  * ```provideDelegate()``` 연산자 안에서는 프로퍼티가 위임 객체에 자신을 위임하는 시점에 프로퍼티 이름과 객체에 따라 적절한 검증을 진행하거나, 상황에 맞는 적절한 프로퍼티 위임 객체를 제공하는 등의 작업을 수행할 수 있다.
  * 예를 들어 디버그 모드일 때는 Mock 서버를 연결하고 프로덕션 모드일 때는 실제 서버를 연결해서 JSON 문자열을 가져오는 경우를 생각해볼 수 있다.
  <br>
  <br>
  
# mod와 rem
  * ```mod``` 대신 ```rem```이 ```%``` 연산자로 해석된다.
  * ```mod``` 대신 ```rem```을 사용하는 이유는 기존 자바 ```BigInteger``` 구현과 다른 정수형 타입(```Int``` 등)의 ```%``` 연산 결과를 맞추기 위함이다.
  <br>
  <br>
  
# 표준 라이브러리 변화
  * **문자열-숫자 변환**
    * 문자열을 수로 변환할 때 예외를 던지는 대신 ```Null```을 돌려주는 메소드가 추가됐다.
    * ```val port = System.getenv("PORT")?.toIntOrNull() ?: 80```처럼 예외 처리 없이 널 처리로 디폴트 값을 제공할 수 있다.
    * 또한 ```Int.toString()``` 등의 숫자-문자열 변환 함수에 인자로 정수를 넘기면 10진수가 아닌 2진수~36진수 변환이 가능하다.
    <br>
  * **onEach()**
    * 컬렉션과 시퀀스에 ```onEach``` 확장 함수가 생겼다.
    * ```onEach```는 ```forEach```와 비슷하지만 컬렉션이나 시퀀스를 다시 반환하기 때문에 메소드 연쇄 호출이 가능하다.
      ```kotlin
      listOf(1, 2, 3, 4, 5).onEach { println("${it}") }.map { it * it }.joinToString(",")
      ```
      <br>
  * **also(), takeIf(), takeUnless()**
    * ```also```는 ```apply```와 비슷하지만 람다 안에서 ```this```가 바뀌지 않기 때문에 수신 객체를 ```it```으로 활용해야 한다는 점이 다르다.
    * ```this```가 가려지면 불편한 경우 ```also```가 유용하다.
      ```kotlin
      class MyClass {
        fun foo(v: OtherClass) {
          ...
          val result = v.also {
            // MyClass의 멤버는 this를 통해 사용 가능하고, v의 멤버는 it를 통해 사용 가능하다.
          }.transform() // transform은 어떤 다른 값을 만들어냄
        }
      }
      ```
      <br>
    * ```also``` 대신 ```apply```를 사용한다면 ```this@foo``` 같은 식으로 ```MyClass```에 접근해야 한다.
    * ```takeIf```는 수신 객체가 술어를 만족하는지 검사해서 만족할 때 수신 객체를 반환하고, 불만족할 때 ```null```을 반환한다.
    * ```takeUnless```는 ```takeIf```의 반대이다.
      ```kotlin
      val srcOrKotlin: Any = File("src").takeIf { it.exists() } ?: File("kotlin")
      ```
      <br>
  * **groupingBy()**  
    * ```groupingBy```는 컬렉션을 키에 따라 분류한다.
      ```kotlin
      (1..100).groupingBy { it % 3 }.eachCount()
      ```
      <br>
  * **Map.toMap()과 Map.toMutableMap()**
    * 맵을 복사할 때 사용한다.
      ```kotlin
      val m1 = mapOf(1 to 2)
      val m2 = m1.toMutableMap()
      m2[10] = 100
      println(m2) // {1=2, 10=100}
      ```
      <br>
  * **minOf(), maxOf()**
    * 둘 또는 세 값 중 최소(```minOf```)나 최대(```maxOf```)인 값을 구할 때 사용한다. 
      ```kotlin
      val longest = maxOf(listOf(), listOf(10), compareBy { it.size })
      ```
      <br>
  * **람다를 사용한 리스트 초기화**
    * ```Array``` 생성자처럼 리스트 생성자 중에도 람다를 파라미터로 받는 생성자가 생겼다.
      ```kotlin
      fun initListWithConst(v: Int, size: Int) = MutableList(size) { v }
      val evens = List(10) { 2 * it }
      val thirtyZeros = initListWithConst(0, 30)
      ```
      <br>
  * **Map.getValue()**
    * ```getValue```는 인자로 받은 키를 가지고 맵의 원소를 찾아 반환하고 원소가 없으면 ```NoSuchElementException```을 던진다.
    <br>
  * **추상 컬렉션 클래스**
    * JDK의 추상 컬렉션 클래스 기능을 대부분 이어받는 추상 컬렉션 클래스가 추가됐다.
    * 변경 불가능 컬렉션
      * ```AbstractColltion```, ```AbstractList```, ```AbstractSet```, ```AbstractMap```
    * 변경 가능 컬렉션
      * ```AbstractMutableCollection```, ```AbstractMutableList```, ```AbstractMutableSet```, ```AbstractMutableMap```
      <br>
  * **배열 연산 추가**
    * 배열 원소를 모두 다루는 여러 메소드가 추가됐다.
    * 비교 연산
      * ```contentEquals```, ```contentDeepEquals```
    * 해시 코드 계산
      * ```contentHashCode```, ```contentDeepHashCode```
    * 문자열 변환
      * ```contentToString```, ```contentDeepToString```
      <br>
      <br>

# 기타
  * ```-jvm-target 1.8``` 옵션이 추가됐지만 아직은 실제 생성되는 바이트코드상의 변화는 크지 않다.
  * 자바 7과 8에 추가된 JDK API를 지원하는 표준 라이브러리가 분리됐다. 메이븐에서 ```kotlin-stdlib``` 대신 ```kotlin-stdlib-jre7```이나 ```kotlin-stdlib-jre8```을 사용하면 해당 버전 API에 맞춘 코틀린 라이브러리를 사용할 수 있다. 
  * 바이트코드에 파라미터 이름을 넣을 수 있게 됐다. ```-java-parameters``` 커맨드 라인 옵션을 컴파일러에 지정하라.
  * ```const val``` 값을 바이트코드에 인라이닝한다.
  * javax.script API(JSR-223)를 지원한다. 
  * ```kotlin-reflect.jar```에 있던 확장 함수와 프로퍼티를 ```kotlin.reflect.full``` 패키지로 옮겼다. 
    
  
    
    
