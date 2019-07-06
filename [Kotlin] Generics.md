# Kotlin의 제네릭 정리
  * Java에선 1.5부터 제네릭이 지원되었고 하위 호환성 유지를 위해 타입 인자가 없는 ```raw``` 타입을 허용했다.
  * 하지만 Kotlin에서는 처음부터 제네릭이 도입되었기 때문에 타입 추론에 의해 자동으로 정의되든 개발자가 직접 정의하든 타입을 항상 정의해야 한다.
  * Java가 ```PECS(Producer - Extends, Consumer - Super)```면 Kotlin은 ```POCI(Producer - Out, Consumer - In)```
  
  * **프로퍼티에 제네릭 선언**
    * 확장 프로퍼티에 제네릭을 선언할 수 있다. (확장 프로퍼티도 결국 함수기 때문에)
      ```kotlin
      val <T> List<T>.penultimate: T
        get() = this[size - 2]
      ```
    * 일반 프로퍼티에는 제네릭을 선언할 수 없다.
    <br>
  
  * **타입 상한 지정**
    * 타입 상한이 한 개라면? 
      ```kotlin
      <T : Number>
      ```
    * 타입 상한이 여러 개라면?
      ```kotlin
      where T : CharSequence, T : Appendable
      ```
    <br>  
    
  * **타입 소거**
    * Kotlin도 Java와 마찬가지로 런타임에 타입 정보가 지워진다.
    * 런타임에 타입 정보를 알고 싶다면 ```inline```과 함께 ```reified``` 구체화 타입 적용
      ```kotlin
      fun <T> isA(value: Any) = value is T
      // Cannot check for instance of erased type: T

      inline fun <reified T> isA(value: Any) = value is T
      ```
      <br>
    
    * ```reified```는 ```inline``` 함수에서만 사용 가능하다.
    * 타입 파라미터가 아니라 구체적인 타입을 사용하므로 만들어진 바이트코드는 실행 시점에 벌어지는 타입 소거의 영향을 받지 않는다.
    * Java 코드에서는 ```reified``` 타입 파라미터를 사용하는 ```inline``` 함수를 호출할 수 없다.
    * Java에서는 Kotlin ```inline``` 함수를 일반 함수처럼 호출한다.
    * 성능을 좋게 하려면 ```inline``` 함수의 크기를 계속 관찰해야 한다. 함수가 커지면 구체화 타입에 의존하지 않는 부분을 별도의 일반 함수로 뽑아내는 편이 낫다.  낫다.
    <br>
    
  * **구체화한 타입 파라미터로 클래스 참조 대신**
    * ```reified```를 이용한 클래스 참조
        ```kotlin
        inline fun <reified T> loadService() {
          return ServiceLoader.load(T::class.java)
        }
        ```
        <br>
    * 안드로이드용 예제 코드
        ```kotlin
        inline fun <reified T: Activity> Context.startActivity() {
          val intent = Intent(this, T::class.java)
          startActivity(intent)
        }
        ```
        <br>

  * **구체화한 타입 파라미터의 제약**
    * 사용 가능한 경우
      * 타입 검사와 캐스팅(```is, !is, as, as?```)
      * Kotlin 리플렉션 API(```::class```)
      * Kotlin 타입에 대응하는 java.lang.Class를 얻기(```::class.java```)
      * 다른 함수를 호출할 때 타입 인자로 사용
    * 사용 불가능한 경우
      * 타입 파라미터 클래스의 인스턴스 생성하기
      * 타입 파라미터 클래스의 동반 객체 메소드 호출하기
      * 구체화한 타입 파라미터를 요구하는 함수를 호출하면서 구체화하지 않은 타입 파라미터로 받은 타입을 타입 인자로 넘기기
      * 클래스, 프로퍼티, 인라인 함수가 아닌 함수의 타입 파라미터를 ```reified```로 지정하기
      <br>

  * **공변성**
    
    |  공변성 |  반공변성 | 무공변성 |
    |:----|:----|:----|
    | ```Producer<out T>``` | ```Consumer<in T>``` | ```MutableList<T>``` |
    | 타입 인자의 하위 타입 관계가 제네릭 타입에서도 유지된다. | 타입 인자의 하위 타입 관계가 제네릭 타입에서 뒤집힌다. | 하위 타입 관계가 성립하지 않는다.
    | ```Producer<Cat>```은 ```Producer<Animal>```의 하위 타입이다. | ```Consumer<Animal>```은 ```Consumer<Cat>```의 하위 타입이다. | |
    | ```T```를 아웃 위치에서만 사용할 수 있다. | ```T```를 인 위치에서만 사용할 수 있다. | ```T```를 아무 위치에서나 사용할 수 있다. |
    <br>   
 
  * **사용 지점 변성(Used-site variance)**
    * 타입이 언급되는 지점에서 변성 지정
    * 클래스에 선언하면서 변성을 지정하는 방식은 ```선언 지점 변성(Declaration site variance)```
    * Java와 비교했을 때 ```MutableList<out T>```는 ```MutableList<? extends T>```와 같고 ```MutableList<in T>```는 ```MutableList<? super T>```와 같다.
    * ```MutableList```와 같은 상당수의 인터페이스는 타입 파라미터로 지정된 타입을 소비하는 동시에 생산할 수 있기 때문에 일반적으로 공변적이지도 반공변적이지도 않다.
    * 하지만 그런 인터페이스 타입의 변수가 한 함수 안에서 생산자나 소비자 중 단 한 가지 역할만을 담당하는 경우가 자주 있다.
      ```kotlin
      fun <T> copyData(source: MutableList<T>, destination: MutableList<T>) {
        for (item in source) {
          destination.add(item)
        }
      }
      ```
    * 위와 같은 함수를 제대로 구현하는 방법은 ```source```의 타입을 ```List<T>```로 정하는 것이다.
    * ```List<out T>```처럼 ```out``` 변경자가 지정된 타입 파라미터를 ```out``` 프로젝션 하는 것은 의미가 없다.
    * ```List```의 정의는 이미 ```class List<out T>```이므로 ```List<out T>```는 그냥 ```List<T>```와 같다. 
    * ```copyData``` 함수에 동작에 맞게 변성을 지정하면 다음과 같다.
      ```kotlin
      fun <T> copyData(source: MutableList<out T>, destination: MutableList<in T>) {
          for (item in source) {
              destination.add(item)
          }
      }
      ```
      <br>
    
  * **스타 프로젝션**
    * 타입 인자 대신 ```*``` 사용
    * 제네릭 타입 인자 정보가 없음을 표현하기 위해 스타 프로젝션을 사용
    * Java의 와일드 카드(```?```)와 대응한다.
    * ```MutableList<*>```는 ```MutableList<Any?>```와 같지 않다.
      * ```MutableList<Any?>```는 모든 타입의 원소를 담을 수 있다는 사실을 알 수 있는 리스트
      * ```MutableList<*>```는 어떤 정해진 구체적인 타입의 원소만을 담는 리스트지만 그 원소의 타입을 정확히 모른다는 사실을 표현
    * ```Consumer<in T>```와 같은 반공변 타입 파라미터에 대한 스타 프로젝션은 ```<in Nothing>```과 동등하다.
    * 결과적으로 그런 스타 프로젝션에서는 ```T```가 시그니처에 들어가 있는 메소드를 호출할 수 없다.
    * 타입 파라미터가 반공변이라면 제네릭 클래스는 소비자 역할을 하는데, 우리는 그 클래스가 정확히 ```T```의 어떤 부분을 사용할지 알 수 없다.
    * 따라서 반공변 클래스에 무언가를 소비하게 넘겨서는 안 된다. 
    * 타입 파라미터를 시그니처에서 전혀 언급하지 않거나 데이터를 읽기는 하지만 그 타입에는 관심이 없는 경우와 같이 타입 인자 정보가 중요하지 않을 때도 스타 프로젝션 구문을 사용할 수 있다. 
      ```kotlin
      fun printFirst(list: List<*>) {
        if (list.isNotEmpty()) {
          println(list.first())
        }
      }
      ```
    * 스타 프로젝션을 사용할 때는 값을 만들어내는 메소드만 호출할 수 있고 ```그 값의 타입에는 신경을 쓰지 말아야 한다.``` 
    <br>
    
  * **스타 프로젝션 사용 시 빠지기 쉬운 함정**
    * 사용자 입력을 검증해야 해서 FieldValdiator라는 인터페이스는 정의했다고 가정하자. 
      ```kotlin
      interface FieldValidator<in T> {
          fun validate(input: T): Boolean
      }

      object DefaultStringValidator : FieldValidator<String> {
          override fun validate(input: String) = input.isNotEmpty()
      }

      object DefaultIntValidator : FieldValidator<Int> {
          override fun validate(input: Int) = input >= 0
      }    
      
      val validators = mutableMapOf<KClass<*>, FieldValidator<*>>()
      validators[String::class] = DefaultStringValidator
      validators[Int::class] = DefaultIntValidator     
      ```
    * 위와 같이 정의를 하고 나면 검증기를 쓸 때 문제가 생긴다. 
    * ```String``` 타입의 필드를 ```FieldValidator<*>```타입의 검증기로 검증할 수 없다. 
    * 컴파일러는 ```FieldValdiator<*>```가 어떤 타입을 검증하는 검증기인지 모르기 때문에 ```String```을 검증하기 위해 그 검증기가 사용하면 안전하지 않다고 판단한다. 
      ```kotlin
      validators[String::class]!!.validate("") // 컴파일 오류
      ```
    * 일단 컴파일을 해결하기 위해 우회한다면 타입 캐스팅을 할 수 있지만 컴파일러는 안전하지 못하다고 경고한다.
      ```kotlin
      val validator = validators[String::class] as FieldValidator<String> // Unchecked cast
      validator.validate("")    
      ```
    * 만약 검증기를 잘못 가져왔을 경우 문제가 발생한다. 
      ```kotlin
      val validator = validators[Int::class] as FieldValidator<String> // Unchecked cast
      validator.validate("")    
      ```    
    * ```String``` 검증기를 가져오려고 했으나 실수로 ```Int``` 검증기를 가져오더라도 타입 캐스팅에는 문제가 없다.
    * 실제 가져온 검증기를 사용하려는 시점, 즉 런타임에 오류를 발생시킨다. 
    * 위와 같은 실수를 방지하기 위해 아래와 같이 타입 안정성을 보장하도록 변경하자.
      ```kotlin
      object Validators {
          private val validators = mutableMapOf<KClass<*>, FieldValidator<*>>()

          fun <T: Any> registerValidator(kClass: KClass<T>, fieldValidator: FieldValidator<T>) {
              validators[kClass] = fieldValidator // 어떤 클래스와 검증기가 타입이 맞아 떨어지는 경우에만 그 클래스와 검증기 정보를 맵에 키/값 쌍으로 넣는다. 
          }

          @Suppress("UNCHECKED_CAST")
          operator fun <T: Any> get(kClass: KClass<T>): FieldValidator<T> =
              validators[kClass] as? FieldValidator<T> ?: throw IllegalArgumentException("No validator for ${kClass.simpleName}")
      }

      Validators.registerValidator(String::class, DefaultStringValidator)
      Validators.registerValidator(Int::class, DefaultIntValidator)

      Validators[String::class].validate("")
      Validators[Int::class].validate(10)    
      ```
    * 기존 코드처럼 똑같이 ```validators``` 맵을 사용하지만 검증기를 등록하거나 가져오는 작업을 수행할 때 타입을 제대로 검사하게 캡슐화한다. 
    * 이 코드도 마찬가지로 안전하지 않은 캐스팅 오류를 컴파일 시 발생시키지만 ```Validators``` 객체가 맵에 대한 접근을 통제하기 대문에 맵에 잘못된 값이 들어가지 못하게 막을 수 있다.     
    
    
