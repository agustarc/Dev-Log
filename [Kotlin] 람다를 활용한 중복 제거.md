* 함수 타입과 람다 식은 재활용하기 좋은 코드를 만들 때 쓸 수 있는 훌륭한 도구다.
* 예제 코드를 통해 어떻게 중복을 제거하는지 살펴보자.
* 웹 사이트 방문 기록을 분석하는 예
  ```kotlin
  data class SiteVisit {
    val path: String, 
    val duration: Double, 
    val os: OS
  }

  enum class OS { WINDOWS, LINUX, MAC, IOS, ANDROID }

  val log = listOf(
    SiteVisit("/", 34.0, OS.WINDOWS), 
    SiteVisit("/", 22.0, OS.MAC), 
    SiteVisit("/login", 12.0, OS.WINDOWS), 
    SiteVisit("/signup", 8.0, OS.IOS), 
    SiteVisit("/", 16.3, OS.ANDROID)
  )
  ```
  <br>

* 윈도우 사용자의 평균 방문 시간을 출력
  ```kotlin
  val averageWindowsDuration = log
    .filter { it.os == OS.WINDOWS }
    .map(SiteVisit::duration)
    .average()

  println(averageWindowsDuration)
  // 23.0
  ```
  <br>

* 이제 맥 사용자에 대해 같은 통계를 구하고 싶은데 중복을 피하기 위해 OS를 파라미터로 뽑아낼 수 있다.
  ```kotlin
  fun List<SiteVisit>.averageDurationFor(os: OS) = 
    filter { it.os = os }.map(SiteVisit::duration).average()

  println(log.averageDurationFor(OS.WINDOWS))
  // 23.0

  println(log.averageDurationFor(OS.MAC))
  // 22.0
  ```
  <br>

* 위 함수에서 만약 모바일 디바이스 사용자(iOS, Android)의 평균 방문 시간을 구하고 싶다면?
  ```kotlin
  val averageMobileDuration = log
    .filter { it.os in setOf(OS.IOS, OS.ANDROID) }
    .map(SiteVisit::duration)
    .average()

  println(averageMobileDuration)
  // 12.15
  ```
  <br>

* 만약 더 복잡한 조건을 통해 방문 기록을 분석하고 싶다면 위 함수로는 부족하다.
* 고차 함수를 사용하면 코드의 중복도 줄일 수 있고 코드를 더욱 유연하게 만들 수 있다.
  ```kotlin
  fun List<SiteVisit>.averageDurationFor(predicate: (SiteVisit) -> Boolean) =
    filter(predicate).map(SiteVisit::duration).average()

  println(log.averageDurationFor { it.os in setOf(OS.ANDROID, OS.IOS) })
  // 12.15

  println(log.averageDurationFor { it.os == OS.IOS && it.path == "/signup" })
  // 8.0
  ```
  <br>

* **디자인 패턴 중 하나인 전략 패턴을 생각해보면 일반 함수 타입을 사용해 전략을 표현할 수 있고 경우에 따라 다른 람다 식을 넘김으로써 여러 전략을 전달할 수 있다.** 
