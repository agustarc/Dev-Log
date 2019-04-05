# 애노테이션의 배열 리터럴
  * 1.1까지는 애노테이션에서 여러 값을 배열로 넘길 때 ```arrayOf```를 써야 했다.
  * 하지만 1.2에서는 ```[]``` 사이에 원소를 넣어서 표시할 수 있다.
    ```kotlin
    @RequestMapping(value = ["v1", "v2"], path = ["path", "to", "resource"])
    ```
  <br>
  <br>

# 지연 초기화(lateinit) 개선
  * 지연 초기화 프로퍼티가 초기화됐는지 검사할 수 있게 됐다.
  * 지연 초기화를 최상위 프로퍼티와 지역 변수에도 사용할 수 있다.
    ```kotlin
    lateinit var url: String
    ... // url을 외부에서 받아 초기화하는 코드
    if (::url.isInitialized) {
      ...
    }
    ```
    <br>
    <br>

# 인라인 함수와 디폴트 함수 타입 파라미터 지원
  * 인라인 함수가 람다를 인자로 받는 경우(```즉, 함수 타입의 파라미터가 있는 경우```)에도 디폴트 파라미터를 사용할 수 있다.
    ```kotlin
    inline fun <E> Iterable<E>.strings(transform: (E) -> String = 
      { it.toString() }) = map { transform(it) }

    val defaultStrings = listOf(1, 2, 3).strings()
    val customStrings = listOf(1, 2, 3).strings { "$(it)" }
    ```
    <br>
    <br>
    
# 함수/메소드 참조 개선
  * ```this::foo```에서 ```this```를 빼고 ```::foo```라고 써도 현재 맥락의 ```this```에 맞춰 해석해준다.
  * 예전에는 ```::foo```는 최상위에 정의된 ```foo```에 대해서만 사용할 수 있었다.
  <br>
  <br>
  
# 타입 추론 개선
  * 제네릭 메소드를 호출해서 새 값을 가져와 그 값을 특정 타입으로 타입 캐스팅하는 경우를 컴파일러가 감지해서 제네릭 메소드의 타입 인자를 특정 타입으로 지정해준다.
  * 아래 코드를 1.1에서 실행하면 제네릭 타입 인자 관련 오류를 볼 수 있다.
    ```kotlin
    fun <T> any(): T = TODO()
    val foo = any() as String
    ```
  * 안드로이드 API에 있는 ```findViewById```가 대표적인 예이다.
  * 1.2부터는 이를 제대로 처리할 수 있다.
    ```kotlin
    val button = findViewById(R.id.button) as Button
    ```
    <br>
    <br>
    
# 경고를 오류로 처리
  * 커맨드라인 옵션에 ```-Werror```를 지정하면 모든 경고를 오류로 처리한다.
    ```gradle
    compileKotlin {
      kotlinOptions.warningsAsErrors = true
    }
    ```
    <br>
    <br>
    
# 스마트 캐스트 개선
  * 안전한 캐스트를 통해 검사가 이뤄진 경우를 놓치는 경우가 있었지만 개선됐다.
    ```kotlin
    fun foo(x: Foo?) {
      val b = (x as? SubClass)?.subclassMethod()
      if (b != null) {
        x.subclassMethod2() // x의 타입은 SubClass
      }
    }
    ```
  * 람다 안에서 ```var```에 대한 스마트 캐스트가 가능하다.
  * 단, 스마트 캐스트가 이뤄진 이후에는 ```var```를 변경하면 안 된다.
  <br>
  <br>
  
# Enum 원소 안의 클래스는 내부 클래스로
  * ```Enum``` 원소 안에 클래스, 인터페이스 등 다양한 중첩 타입을 정의할 수 있었고 ```inner``` 클래스는 정의할 수 없었지만, 1.2부터는 ```inner```로 표시된 내부 클래스 정의만 가능하다.
    ```kotlin
    enum class Foo {
      BAR {
        inner class Baz // 1.1에서는 불가능 1.2에서는 가능
        class Baz2      // 1.1에서는 가능 1.2에서는 경고('deprecated' 경고)
      }
    }
    ```
  * 1.3부터는 아예 컴파일도 안 될 예정이므로 ```Enum``` 엔트리 안에 클래스를 정의해야 한다면 꼭 내부 클래스를 사용해야 한다.
  <br>
  <br>
  
# (기존 코드를 깨는 변경) ```try``` 블록의 스마트 캐스트 안전성 향상
  * 기존에는 try 블록 안에서 스마트 캐스트로 대입한 값을 ```try``` 블록 뒤에서 사용할 수 있었고, 그에 따라 널 안전성이나 타입 안전성이 깨지는 경우가 있었다.
  * 하지만 1.2부터는 스마트 캐스트 안전성을 더 강화하기 위해 이를 사용 금지 예고(```deprecated```) 처리한다.
  * 꼭 이 기능이 필요하다면 ```-Xlegacy-smart-cast-after-try``` 컴파일러 확장 옵션을 지정할 수 있지만, 1.3부터는 아예 사용 금지될 예정이므로 기존 코드를 변경하는 편이 낫다.
  <br>
  <br>
  
# 1.2부터 사용 금지 예고된 기능
  * **다른 클래스를 상속한 하위 데이터 클래스의 자동 생성 ```copy```로 인한 문제**
    * 데이터 클래스가 이미 ```copy```가 정의된 다른 클래스를 상속하는 데 자동 생성되는 ```copy```메소드의 시그니처가 상위 클래스의 ```copy```와 같은 경우 데이터 클래스의 자동 생성 ```copy```가 상위 클래스의 ```copy```를 디폴트로 사용해서 문제가 생기는 경우가 있었다. 
    * 1.2부터는 이런 문제가 생기는 데이터 클래스를 정의하면 경고가 나오고 1.3부터는 컴파일 오류가 발생할 것이다.
    <br>
  * **Enum 원소 안에서 중첩 타입 정의**
    * ```Enum``` 원소 내부에 내부 클래스가 아닌 중첩 타입을 정의하면 초기화 시 문제가 생길 수 있어서 1.2부터는 ```Enum``` 원소 안에 내부 클래스가 아닌 중첩 타입을 사용하면 경고가 나오고 1.3부터는 컴파일 오류 처리할 예정이다.
    <br>
  * **가변 인자에게 이름 붙은 인자로 원소 하나만 넘기기**
    * 예전에는 ```foo(items = i)```처럼 가변 인자 파라미터에 원소를 단 하나만 넘겨도 정상처리됐다.
    * 하지만 애노테이션의 배열 리터럴과의 일관성을 위해 1.3부터는 이런 경우 번거롭지만 스프레드 연산자를 사용해야 한다.
      ```kotlin
      foo(items = *intArrayOf(1))
      ```
      <br>
  * **Throwable을 확장하는 제네릭 클래스의 내부 클래스**
    * ```Throwable```을 상속하는 제네릭 클래스 안에 내부 클래스를 사용하면 ```throw-catch``` 시나리오에 따라서는 타입 안전성이 깨질 수 있다.
    * 1.2에서는 경고, 1.3부터는 컴파일 오류 처리된다.
    <br>
  * **읽기 전용 프로퍼티를 뒷받침하는 필드 덮어쓰기**
    * 읽기 전용 프로퍼티의 커스텀 게터를 정의하면서 뒷받침하는 필드를 ```field = ...```처럼 덮어쓸 수 있었다.
    * 1.2에서는 경고, 1.3부터는 컴파일 오류 처리된다.
    <br>
    <br>
    
# 표준 라이브러리
  * **호환 패키지 분리**
    * 코틀린 모듈 시스템을 자바 9 모듈 시스템과 맞춤에 따라 패키지를 여러 jar에 분산시킬 수 없게 됐다.
    * 그에 따라 ```kotlin-stdlib-jdk7```과 ```kotlin-stdlib-jdk8```이라는 아티팩트를 새로 만들었다.
    * 각각 이전(1.1)의 ```kotlin-stdlib-jre7```과 ```kotlin-stdlib-jre8```을 대신한다.
    * 또한 새 자바 모듈 시스템 요구 사항에 맞추기 위해 ```kotlin.reflect``` 패키지에 들어있는 사용 금지 처리된 선언을 ```kotlin-reflect``` 라이브러리에서 없앴다.
    * 혹시 그런 선언이 필요하다면 ```kotlin.reflect.full```에 있는 선언을 사용하라.
    <br>
  * **컬렉션**
    * 컬렉션 원소를 ```n``` 개씩 짝을 지어 처리할 수 있는 슬라이딩 윈도우(Sliding window) 처리 메소드들이 추가됐다.
      * ```chunked/chunkedSequence``` : 컬렉션을 정해진 크기의 덩어리로 나눠준다.
      * ```windowed/windowedSequence``` : 정해진 크기의 슬라이딩 윈도우를 만들어서 컬렉션을 처리할 수 있게 해준다.
      * ```ZipWithNext``` : 컬렉션에 연속한 두 원소에 대해 람다를 적용한 결과를 얻는다.
    * 10개짜리 리스트에 대한 각 메소드를 적용한 예제
      ```kotlin
      fun main(args: Array<String>) {
        val l = listOf(1, 2, 3, 4, 5, 6, 7)
        println("l = ${l}")
        
        // 그룹
        println("l.chunked(size = 3)")
        l.chunked(size = 3).forEach { println(it) }
        
        // 슬라이딩 윈도우
        println("l.windowed(size = 3, step = 1)")
        l.windowed(size = 3, step = 1).forEach { println(it) }
        
        // 연속된 2 원소씩 쌍
        println("l.zipWithNext")
        l.zipWithNext { x, y -> println("($x, $y)") }
        
        // 그룹을 슬라이딩 윈도우로 구현
        fun <T> List<T>.mychunked(size: Int) = windowed(size, size)
        // ZipWithNext를 슬라이딩 윈도우로 구현
        fun <T,R> List<T>.myZipWithNext(f: (T, T) -> R) = 
          windowed(size = 2, step = 1)  // 2개씩 짝짓기
          .filter { it.size == 2 }      // 1개짜리 없애기
          .map { (x, y) -> f(x, y) }    // 함수 적용(구조 분해 람다 사용)
        
        println("l.mychunked(size = 3)")
        l.mychunked(size = 3).forEach { println(it) }
        
        println("l.myZipWithNext")
        l.myZipWithNext { x, y -> println("($x, $y)") }
      }
      ```
    * 위 예제의 실행 결과
      ```kotlin
      l = [1, 2, 3, 4, 5, 6, 7]
      l.chunked(size = 3)
      [1, 2, 3]
      [4, 5, 6]
      [7]
      l.windowed(size = 3, step = 1)
      [1, 2, 3]
      [2, 3, 4]
      [3, 4, 5]
      [4, 5, 6]
      [5, 6, 7]
      l.zipWithNext
      (1, 2)
      (2, 3)
      (3, 4)
      (4, 5)
      (5, 6)
      (6, 7)
      l.mychunked(size = 3)
      [1, 2, 3]
      [4, 5, 6]
      l.myZipWithNext
      (1, 2)
      (2, 3)
      (3, 4)
      (4, 5)
      (5, 6)
      (6, 7)
      ```
    * 리스트의 원소를 처리하는 메소드도 몇 가지 추가됐다.
      * ```fill``` : 변경 가능 리스트의 모든 원소를 지정한 값으로 바꾼다.
      * ```replaceAll``` : 변경 가능 리스트의 모든 원소를 람다를 적용한 결과 값으로 갱신한다. ```map```을 제자리에서 수행한다고 생각하면 이해하기 쉽다.
      * ```shuffle``` : 변경 가능한 리스트의 원소를 임의로 뒤섞는다.
      * ```shuffled``` : 변경 불가능한 리스트의 원소를 임의로 뒤섞은 새 리스트를 반환한다.
      ```kotlin
      val items = (1..5).toMutableList()
      items.shuffle()
      println(items) // [3, 2, 4, 5, 1]

      items.replaceAll { it * 2 }
      println(items) // [6, 4, 8, 10, 2]

      items.fill(5)
      println(items) // [5, 5, 5, 5, 5]
      ```
      * 이 예에서 ```shuffle```의 결과인 ```[3, 2, 4, 5, 1]```은 매번 달라질 수 있고, ```[6, 4, 8, 10, 2]```는 항상 ```shuffle```의 결과로 얻은 ```items``` 리스트의 각 원소를 2배한 값들이 된다.
      <br>
      <br>
    
  * **kotlin.math**
    * 자바스크립트와 JVM 플랫폼 통합을 위해 수학 패키지 ```kotlin.math```에 많은 멤버가 추가됐다.
    * 상수 
      * ```PI```, ```E```
    * 삼각 함수
      * ```cos```, ```sin```, ```tan```, ```acos```, ```asin```, ```atan```, ```atan2```
    * 하이퍼볼릭 함수
      * ```cosh```, ```sinh```, ```tanh```
    * 지수 함수
      * ```pow```, ```sqrt```, ```hypot```, ```exp```, ```expm1```
    * 로그 함수
      * ```log```, ```log2```, ```log10```, ```ln```, ```ln1p```
    * 올림/내림 함수
      * ```ceil```, ```floor```, ```truncate```, ```round```, ```roundToInt```, ```roundToLong```
    * 부호와 절댓값 함수
      * ```abs```, ```sign```, ```absoluteValue```, ```sign```, ```withSign```
    * 2 값의 최댓값/최솟값
      * ```max```, ```min```
    * 부동소수점의 2진 표현
      * ```ulp```, ```nextUp```, ```nextDown```, ```nextTowards```, ```toBits```, ```toRawBits```, ```Double.fromBits```
    <br>
  * **Regex 클래스를 직렬화 가능하게 바꿈**
    * ```kotlin.text.Regex```가 직렬화 가능(Serializable)하게 바뀌었다.
    * 따라서 정규식을 직렬화할 수 있다.
    <br>
    <br>
    
# JVM 백엔드 변경
  * **생성자 호출 정규화**
    * 코틀린 생성자에 복잡한 제어 구조를 인자로 호출하는 코드를 컴파일한 바이트코드는 JVM 명세상으로 적법하지만 일부 바이트코드 처리 도구가 그런 바이트코드를 제대로 처리하지 못하는 경우가 있다.
    * 이를 해결하기 위해 컴파일러에게 ```-Xnormalize-constructor-calls=모드``` 옵션을 제공하면 모드에 따라 자바와 비슷한 바이트코드를 생성할 수 있다.
    * 모드
      * ```disable```
        * 디폴트로서 코틀린 1.0이나 1.1과 같은 바이트코드를 만들어낸다.
      * ```enable```
        * 자바와 비슷한 생성자 호출을 만들어낸다.
        * 그에 따라 클래스 로딩과 초기화 순서가 코틀린 바이트코드와 약간 달라질 수 있다.
      * ```preserve-class-initialization```
        * 자바와 비슷한 생성자 호출 코드를 만들어내되 클래스의 초기화 순서를 보존한다.
        * 그에 따라 애플리케이션의 성능이 저하될 수 있다.
        * 따라서 여러 클래스 사이에 상태를 복잡하게 공유하고 클래스 초기화 시 그 상태를 갱신하는 경우에만 이 옵션을 사용하는 편이 낫다.
        <br>
  * **자바 디폴트 메소드 호출**
    * 자바의 디폴트 메소드를 코틀린 인터페이스 멤버가 오버라이딩하는 경우 코틀린 1.2부터는 무조건 자바 1.8로 컴파일하도록 ```-jvm-target 1.8```을 명시해야 한다.
    * 그렇지 않은 경우 컴파일 오류가 발생한다.
    <br>
  * **(기존 코드를 깨는 변경) 플랫폼 코드의 x.equals(null) 동작 일관성**
    * 자바 원시 타입으로 매핑되는 플랫폼 타입(```Int!, Boolean!, Short!, Long!, Float!, Double!, Char!```)에 대해 ```x.equals(null)```을 호출할 때 ```x```가 ```null```이면 ```true```가 반환됐다.
    * 1.2부터는 ```x```가 원시 타입에 대응하는 플랫폼 타입이라면 NPE가 발생한다. 
    * 단, 같은 타입의 ```x```에 대해 ```x == ...``` 은 ```x```가 ```null```이고 ```...``` 도 ```null```이면 ```true```를 반환한다.
    * 예전처럼 이런 경우 ```true```가 필요하다면 ```-Xno-exception-on-explicit-equals-for-boxed-null``` 컴파일러 옵션을 사용하라.
    <br>
    <br>

# 1.2에 추가된 실험 기능 : 다중 플랫폼 프로젝트
  * 코틀린과 자바스크립트, 향후 네이티브 플랫폼을 모두 지원하는 기능을 개발 중이다.
  * 공통 코드는 공통 모듈에 두고 플랫폼 의존적인 코드는 플랫폼별 모듈에 따로 두는 형태다.
  * 다중 플랫폼을 지원하기 위한 핵심 기능은 ```expect```과 ```actual```을 통해 외부에 제공할 API와 그 API에 대한 실제 구현을 표현하는 것이다.
    ```kotlin
    // 공통 코드
    expect fun hello(world: String)

    expect class URL(spec: String) {
      open fun getHost(): String
      open fun getPath(): String
    }

    // JVM 코드
    actual fun hello(world: String) {
      println("Hello JVM $world")
    }

    actual typealias URL = java.net.URL
    ```
  * 자세한 내용은 https://kotlinlang.org/docs/reference/multiplatform.html 참조
