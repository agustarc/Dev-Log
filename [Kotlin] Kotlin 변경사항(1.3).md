# 코틀린 네이티브 개선
  * ```LLVM``` 기반 백엔드를 사용한 네이티브 컴파일 기능을 계속 개선 중이다.
  * 지원하는 플랫폼
    * iOS(arm32, arm64, emulator x86_64)
    * 맥OS(x86_64)
    * 안드로이드(arm32, arm64)
    * 윈도우(mingw x86_64)
    * 리눅스(x86_64, arm32, MIPS, MIPS 리틀엔디언)
    * 웹 어셈블리(wasm32)
  * 상호 운용을 위해 코틀린 네이티브 컴파일러는 각 플랫폼에서 사용할 수 있는 실행 파일과 ```C++```나 ```C```에서 링크할 수 있는 헤더와 동적(또는 정적) 라이브러리를 생성해낼 수 있다.
  <br>
  <br>

# 다중 플랫폼 프로젝트
  * 1.2에서 사용했던 다중 플랫폼 모델을 완전히 바꿨다. 차이는 다음과 같다.
    * 예전 모델에서는 공통 코드와 플랫폼별 코드가 ```expectedBy``` 의존관계를 통해 서로 다른 모듈에 들어가야 했지만, 1.3부터는 플랫폼별 코드가 같은 모델의 다른 소스 루트 아래 위치하게 됐다.
    * 지원하는 플랫폼에 따라 다양한 플랫폼 사전 설정(preset)을 미리 제공한다.
    * 각 소스 루트별로 의존관계를 별도로 지정한다.
    * 다중 플랫폼 라이브러리를 배포할 수 있다.
    <br>
    <br>
    
# 컨트랙트(Contract, 계약)
  * 스마트 캐스트 기능은 타입 검사를 통해 널 가능한 변수에 저장된 값이 널일 가능성이 없으면 자동으로 널이 될 수 없는 타입으로 캐스팅해준다.
    ```kotlin
    fun foo(s: String?) {
      if (s != null) s.length // 컴파일러가 s를 String 타입으로 자동 캐스팅
    }
    ```
  * 하지만 이 검사를 별도 함수로 분리하면 스마트캐스트가 더 이상 지원되지 않는다.
    ```kotlin
    fun String?.isNotNull(): Boolean = this != null

    fun foo(s: String?) {
      if (s.isNotNull()) s.length // 자동변환 안됨
    }
    ```
  * 1.3에서는 컨트랙트를 사용해 이런 상황을 개선할 수 있다.
  * 컨트랙트는 함수의 동작을 컴파일러가 이해할 수 있게 기술하기 위한 기능이다.
  * 현재 두 가지 종류의 컨트랙트를 지원한다.
    * 함수의 반환 값과 인자 사이의 관계를 명시해서 스마트캐스트 분석을 쉽게 만들어주는 컨트랙트
      ```kotlin
      fun require(condition: Boolean) {
        // "이 함수가 정상적으로 반환되면 condition이 참이다"라는 조건을 표현하는 컨트랙트
        contract { return() implies condition }
        if (!condition) throw IllegalArgumentException(...)
      }

      fun foo(s: String?) {
        require(s is String)
        // s is String이라는 조건이 참이면 예외가 발생하지 않으므로
        // 이하 코드에서는 자동으로 s를 String으로 스마트캐스트할 수 있다.
      }
      ```
    * 고차 함수가 있을 때 컴파일러가 변수 초기화 여부 분석을 더 잘 할 수 있게 돕는 컨트랙트
      ```kotlin
      fun synchronize(lock: Any?, block: () -> Unit) {
          // "이 함수는 block을 여기서 바로 실행하며 오직 한번만 실행한다"라는 뜻의 컨트랙트
          contract { callsInPlace(block, EXACTLY_ONCE) }
      }

      fun foo() {
          val x: Int
          synchronize(lock) {
              x = 42 // 이 블록을 단 한 번만 싫애한다는 사실을 컴파일러가 알고 있으므로
                     // val에 값을 제대입한다는 오류 메시지를 표시하지 않음
          }
          
          println(x) // 바로 위에 있는 람다 블록이 한 번 실행된다는 사실을 컴파일러가 
                     // 알고 있으므로, 여기서 x가 초기화되지 않을 수 있다는 오류를 발생시키지 않음
      }
      ```
    * 사용자 정의 컨트랙트
      * 아직 실험적인 기능이므로 컨트랙트 선언 문법은 얼마든지 바뀔 수 있다.
      * 표준 라이브러리에 있는 ```contract``` 함수를 호출하면 컨트랙트를 만들 수 있다.
      * Contract 함수는 DSL 영역(scope)을 제공한다.
      <br>
      <br>
      
# When의 대상을 변수에 포획
  * 1.3부터 ```when```의 대상을 변수에 대입할 수 있다.
    ```kotlin
    fun Request.getBody() = 
        when (val response = excuteRequest()) {
      is Success -> response.body
      is HttpError -> throw HttpException(response.status)
    }
    ```
  * 물론 ```when``` 바로 앞에서 변수에 식의 결과 값을 대입하고 ```when```을 사용할 수도 있다.
  * 하지만 이 예제처럼 ```when```의 괄호 안에서 변수를 선언하고 대입할 수 있으면 ```when```식 안에서만 사용할 수 있는 변수가 생기므로, ```when```문 밖의 네임스페이스가 더럽혀지는 일을 줄일 수 있다.
  <br>
  <br>
  
# 인터페이스의 동반 객체에 있는 멤버를 @JvmStatic이나 @JvmField로 애노테이션
  * 1.3부터는 인터페이스의 동반 객체에 들어있는 멤버에게 ```@JvmStatic```이나 ```@JvmField```를 붙일 수 있다.
  * 이런 애노테이션이 붙은 멤버들은 클래스 파일에서는 인터페이스의 ```static``` 변수가 된다.
    ```kotlin
    interface Foo {
        companion object {
            @JvmField
            val answer: Int = 42

            @JvmStatic
            fun sayHello() {
                println("Hello, world!")
            }
        }
    }  
    ```
  * 위 코드는 다음 자바 코드와 같다.
    ```kotlin
    interface Foo {
        public static int answer = 42;
        public static void sayHello() {
            // ...
        }
    }  
    ```
    <br>
    <br>

# 애노테이션 클래스 안에 선언을 중첩시킬 수 있음
  * 애노테이션 클래스 내부에 중첩 클래스, 인터페이스, 동반 객체 등을 만들 수 있다.
    ```kotlin
    annotation class Foo {
        enum class Direction { UP, DOWN, LEFT, RIGHT }

        annotation class Bar

        companion object {
            fun foo(): Int = 42
            val bar: Int = 42
        }
    }  
    ```
    <br>
    <br>
  
# 파라미터 없는 메인
  * 프로그램 시작접은 원래 ```main(args: Array<String>)```처럼 문자열 배열을 파라미터로 받아야 했지만, ```args```를 사용하지 않는 경우 파라미터를 받지 않는 메인 함수를 선언할 수 있다.
    ```kotlin
    fun main() {
      println("Hello, world!")
    }
    ```
    <br>
    <br>
  
# 함수 파라미터 수 제한 완화
  * 1.2까지는 함수가 파라미터를 22개까지 받을 수 있었다.
  * 1.3부터는 255개까지 처리할 수 있다. 
  * https://github.com/Kotlin/KEEP/blob/master/proposals/functional-types-with-big-arity-on-jvm.md
  <br>
  <br>
  
# 프로그래시브 모드
  * 코틀린에서는 주요 릴리스(1.1, 1.2, 1.3) 시에만 기존 코드를 바꿔 써야만 하는 변화를 도입한다.
    * 이렇게 기존 코드를 재작성해야만 하게 만드는 언어의 변화를 일컬어 '```브레이킹 체인지(breaking change)```'라고 부른다.
  * 하지만 컴파일러 버그 수정이나 언어 명세 변경을 자신의 프로젝트에 더 빠르게 적용하고 싶은 개발자들을 위해 프로그래시브 컴파일러 모드를 추가했다.  
  * 커맨드 라인에서 ```-progressive```라는 옵션을 컴파일러에게 넘기면 컴파일러가 프로그래시브 모드로 작동한다.
  <br>
  <br>
  
# 인라인 클래스(실험적 기능)
  * 프로퍼티가 단 하나뿐인 클래스를 ```inline```이라는 키워드를 사용해 인라인 클래스로 정의할 수 있다.
    ```kotlin
    inline class name(val s: String)
    ```
  * 코틀린 컴파일러는 인라인 클래스를 사용하는 코드를 번역할 때 내부 프로퍼티(이 예에서는 ```String``` 타입의 값 ```s```)의 값을 사용해 공격적으로 최적화할 수 있다.
  * 예를 들어 별도로 생성자 등을 만들지 않고 인라인 클래스의 인스턴스 객체 대신, 내부 프로퍼티 객체를 사용하게 코드를 생성하는 등의 최적화가 가능하다.
    ```kotlin
    fun main() {
        // 아래 호출은 Name 클래스에 속한 인스턴스를 만들지 않고
        // "Kotlin"이라는 문자열만을 만든다.
        val name = Name("Kotlin")
        // 다음 println문을 처리할 때 Name 타입의 객체에 있는 필드에 접근해 문자열을 가져오는 대신
        // 문자열을 바로 접근하는 코드를 만들어낼 수 있다.
        println(name.s) 
    }  
    ```
    <br>
    <br>

# 부호 없는 정수(실험적 기능)
  * ```kotlin.UByte``` : 0부터 255까지 범위에 속하는 부호 없는 8비트 정수
  * ```kotlin.UShort``` : 0부터 65535까지 범위에 속하는 부호 없는 16비트 정수
  * ```kotlin.UInt``` : 0부터 2^32 - 1까지 범위에 속하는 부호 없는 32비트 정수
  * ```kotlin.ULong``` : 0부터 2^64 - 1가지 범위에 속하는 부호 없는 32비트 정수
  * 부호 없는 정수들은 부호 있는 정수들이 제공하는 대부분의 기능을 제공한다.
    ```kotlin
    // 부호 없는 정수 리터럴은 u를 접미사로 사용한다.
    val uint = 42u 
    val ulong = 42uL
    val ubyte: UByte = 255u

    // 표준 라이브러리 확장을 통해 부호 있는 정수와 부호 없는 정수를 변환할 수 있다.
    val int = uint.toInt()
    val byte = ubyte.toByte()
    val ulong2 = byte.toULong()

    // 부호 없는 정수도 부호 있는 정수와 비슷한 연산을 제공한다.
    val x = 20u + 22u
    val y = 1u shl 8
    val z = "128".toUByte()
    val range = 1u..5u  
    ```
    <br>
    <br>

# @JvmDefault(실험적 기능)
  * 자바 인터페이스에 디폴트 메소드가 추가됨에 따라 코틀린도 인터페이스의 디폴트 메소드를 선언할 수 있게 지원하는 ```@JvmDefault``` 애노테이션을 추가했다.
    ```kotlin
    interface Foo {
        // foo를 인터페이스의 디폴트 메소드로 만든다.
        @JvmDefault
        fun foo(): Int = 42
    }  
    ```
  * 다만 디폴트 메소드를 사용하면 이진 호환성이 깨진다.
  * 프로덕션 환경에서 사용할 때는 이진 호환성이 깨진다는 사실에 특히 유의해야 한다.
  <br>
  <br>
  
# 표준 라이브러리 변화
  * **Random 다중 플랫폼 지원**
    * 1.3부터는 여러 플랫폼에서 사용할 수 있는 난수 함수가 추가됐다. 
    * ```Kotlin.random.Random```을 모든 플랫폼에서 쓸 수 있다.
    ```kotlin
    val number = Random.nextInt(42)  // [0, 42) 범위의 정수 난수 발생
    println(number)
    ```
    <br>
  * **isNullOrEmpty와 orEmpty 확장을 여러 클래스에 추가**
    * ```isNullOrEmpty```와 ```orEmpty``` 확장 함수를 컬렉션, 맵, 배열 등의 객체에도 추가했다.
    <br>
  * **배열 원소 복사 확장 함수 copyInto() 추가**  
    * ```array.copyInto(targetArray, targetOffset, startIndex, endIndex)``` 함수를 사용해 어떤 배열의 내용을 다른 배열(targetArray)에 복사할 수 있다.
      ```kotlin
      val sourceArr = arrayOf("k", "o", "t", "l", "i", "n")
      val targetArr = sourceArr.copyInto(arrayOfNulls<String>(6), 3, startIndex = 3, endIndex = 6)
      println(targetArr.contentToString())

      sourceArr.copyInto(targetArr, startIndex = 0, endIndex = 3)
      println(targetArr.contentToString())    
      ```
      <br>
  * **맵 연관 쌍 추가 함수 associateWith()**
    * 키 컬렉션과 값 컬렉션을 서로 1:1로 연관시킬 때 ```associate { it to getValue(it) }```를 사용할 수 있었다.
    * 하지만 ```keys.associateWith { getValue(it) }```를 사용해 더 쉽게 키와 값을 연관시킬 수 있다.
      ```kotlin
      val keys = 'a'..'f'
      val map = keys.associateWith { it.toString().repeat(5).capitalize() }
      map.forEach { println(it) }    

      a=Aaaaa
      b=Bbbb
      c=Cccc
      d=Dddd
      e=Eeee
      f=Ffff
      ```
      <br>
  * **isEmpty와 ifBlank 함수**
    * 컬렉션, 맵, 배열, 시퀀스 등에 추가된 ```isEmpty```를 쓰면 해당 컬렉션이 비어있는 경우에 대한 대안을 제시할 수 있다.
    ```kotlin
    fun printAllUppercase(data: List<String>) {
        val result = data
        .filter { it.all { c -> c.isUpperCase() } }
            .ifEmpty { listOf("<no uppercase>") }
        result.forEach { println(it) }
    }

    printAllUppercase(listOf("foo", "Bar")) // <no uppercase>
    printAllUppercase(listOf("FOO", "BAR")) // FOO, BAR
    ```
    <br>
  * **sealed 클래스에 접근할 수 있는 리플렉션 추가**
    * 리플렉션에서 ```KClass.sealedSubclasses```를 사용하면 ```sealed``` 클래스를 직접 상속한 하위 클래스들에 접근할 수 있다.
    <br>
  * **기타 소소한 변경**
    * Boolean에 동반 객체가 생겼다.
    * Any?.hashCode() 확장이 null에 대해 0을 반환한다.
    * Char도 MIN_VALUE와 MAX_VALUE를 제공한다.
    * 기본 타입들의 동반 객체에 SIZE_BYTES와 SIZE_BITS를 추가했다.
  
