* Kotlin의 ```invoke operator```를 사용하면 객체를 함수처럼 호출할 수 있다.
* 남용하면 안 되고 DSL에서 유용하게 사용될 수 있음.
* ```invoke```도 ```plus```, ```get```, ```set```, ```rangeTo``` 등과 같은 ```operator```의 하나이다.
* ```invoke```가 동작하는 간단한 예제
  ```kotlin
  class Greeter(val greeting: String) {
    operator fun invoke(name: String) {
      println("$greeting, $name!")
    }
  }
  
  val bavarianGreeter = Greeter("Servus")
  bavarianGreeter("Dmitry") // Greeter의 인스턴스를 마치 함수처럼 호출
  // Servus, Dmitry!
  ```

* ```invoke operator```의 파라미터 개수에 따라 호출하는 함수는 아래와 같이 대응된다.
  ```kotlin
  a.invoke() -> a()
  a.invoke(i) -> a(i)
  a.invoke(i, j) -> a(i, j)
  a.invoke(i_1, ..., i_n) -> a(i_1, ..., i_n)
  ```
  
* 인라인 하는 람다를 제외한 모든 람다는 함수형 인터페이스를 구현하는 클래스로 컴파일된다. 
* 기존 ```Function1```, ```Function2``` 등의 인터페이스에 각각 대응하는 ```invoke operator```가 정의되어 있었는데 Kotlin 1.3부터 변경된 부분이 있다.
* Kotlin 1.2까지는 ```Function``` 인터페이스의 파라미터로 22개까지 받을 수 있었고 ```invoke operator```도 22개까지 받도록 되어 있었으나 1.3부터 255개의 파라미터까지 처리할 수 있도록 변경됨
* 아래 코드는 1.3 버전의 ```FunctionN``` 인터페이스
  ```kotlin
  interface FunctionN<out R> : Function<R>, FunctionBase<R> {
      /**
       * Invokes the function with the specified arguments.
       *
       * Must **throw exception** if the length of passed [args] is not equal to the parameter count returned by [arity].
       *
       * @param args arguments to the function
       */
      operator fun invoke(vararg args: Any?): R

      /**
       * Returns the number of arguments that must be passed to this function.
       */
      override val arity: Int
  }
  ```

* 함수 타입을 확장하면서 ```invoke```를 오버라이딩하는 예제
  ```kotlin
  data class Issue(
          val id: String, val project: String, val type: String,
          val priority: String, val description: String
  )

  class ImportantIssuePredicate(private val project: String) : (Issue) -> Boolean {

      override fun invoke(issue: Issue): Boolean {
          return issue.project == project && issue.isImportant()
      }

      private fun Issue.isImportant(): Boolean {
          return type == "Bug" && (priority == "Major" || priority == "Critical")
      }
  }

  val issue1 = Issue(id = "IDEA-154446", project = "IDEA", type = "Bug", priority = "Major", description = "Save settings failed")
  val issue2 = Issue(id = "KT-12183", project = "Kotlin", type = "Feature", priority = "Normal", description = "Intention: convert several calls on the same receiver to with/apply")
  val predicate = ImportantIssuePredicate("IDEA")

  for (issue in listOf(issue1, issue2).filter(predicate)) {
      println(issue.id)
  }
  
  // IDEA-154446
  ```

* 위 코드의 ```predicate```처럼 로직이 복잡해서 한 람다로 표현하기 힘든 경우 람다를 함수 타입 인터페이스를 구현하는 클래스로 변환하고 그 클래스의 ```invoke``` 메소드를 
오버라이드 하면 간결하고 우아한 코드 작성이 가능하다.
* 만약 ```Issue.isImportant()``` 함수의 로직을 ```for``` 문의 ```filter```에 직접 작성한다면 보기도 힘들고 가독성이 떨어진다. 
* 위와 같은 접근법은 람다 본문에서 따로 분리해 낸 메소드가 영향을 끼치는 영역을 최소화할 수 있다는 장점이 있다.
