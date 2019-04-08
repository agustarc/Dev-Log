* 다음과 같이 함수형 인터페이스를 인자로 받는 Java 메소드가 있다고 가정하자.
  ```java
  void postponeComputation(int delay, Runnable computation);
  ```
  <br>
  
* Kotlin에서는 람다를 이 함수에 넘길 수 있고 컴파일러가 자동으로 람다를 Runnable 인스턴스로 변환해준다.
  ```kotlin
  postponeComputation(1000) { println(42) }
  ```
  <br>
  
* 여기서 'Runnable 인스턴스'라는 말은 실제로는 'Runnable을 구현한 무명 클래스의 인스턴스'라는 뜻.
<br>

* 컴파일러는 자동으로 그런 무명 클래스와 인스턴스를 만들어주고 무명 클래스에 있는 유일한 추상 메소드를 구현할 때 람다 본문을 메소드 본문으로 사용한다.
<br>

* Runnable을 구현하는 무명 객체를 명시적으로 만들어서 사용할 수도 있다.
  ```kotlin
  postponeComputation(1000, object : Runnable {
    override fun run() {
      println(42)
    }
  })
  ```
  <br>
  
* **람다와 무명 객체의 차이**
  * 객체를 명시적으로 선언하는 경우 메소드를 호출할 때마다 ```새로운 객체가 생성된다.```
  * 람다는 정의가 들어있는 함수의 변수에 접근하지 않는 람다에 대응하는 무명 객체를 메소드를 호출할 때마다 ```반복 사용한다.```
    ```kotlin
    val runnable = Runnable { println(42) } // 전역 변수로 컴파일되므로 프로그램안에 단 하나의 인스턴스만 존재한다.
    fun handleComputation() {
      postponeComputation(1000, runnable) // 모든 handleComputation 호출에 같은 객체를 사용한다.
    }
    ```
    <br>
    
* 람다가 ```주변 영역의 변수를 포획한다면``` 매 호출마다 같은 인스턴스를 사용할 수 없다. 
<br>

* 그런 경우 컴파일러는 매번 주변 영역의 변수를 포획한 새로운 인스턴스를 생성해준다.
  ```kotlin
  fun handleComputation(id: String) { // 람다 안에서 "id" 변수를 포획
    postponeComputation(1000) { println(id) } // handleComputation을 호출할 때마다 새로 Runnable 인스턴스를 만든다.
  }
  ```
  <br>
  
* 람다가 변수를 포획하면 무명 클래스 안에 포획한 변수를 저장하는 필드가 생기며, 매 호출마다 그 무명 클래스의 인스턴스를 새로 만든다.
<br>

* 하지만 포획하는 변수가 없는 람다에 대해서는 인스턴스가 단 하나만 생긴다.
<br>

* 위의 변수를 포획하는 람다 식의 바이트코드를 디컴파일한 결과
  ```kotlin
  class HandleComputation$1(val id: String) : Runnable {
    override fun run() {
      println(id)
    }
  }

  fun handleComputation(id: String) {
    postponeComputation(1000, HandleComputation$1(id)) // 내부적으로는 람다 대신 특별한 클래스의 인스턴스가 만들어진다.
  }
  ```
  <br>

* Java에서는 ```final``` 변수만 포획할 수 있지만 간단한 트릭을 통해 변경 가능한 변수를 포획할 수 있다. 
* 변경 가능한 변수를 저장하는 원소가 단 하나뿐인 배열을 선언하거나, 변경 가능한 변수를 필드로 하는 클래스로 선언하는 것
* 이런 트릭을 Kotlin으로 작성하면 다음과 같다.
  ```kotlin
  class Ref<T>(var value: T) 
  val counter = Ref(0)
  val inc = { counter.value++ } // 변경 불가능한 변수를 포획했지만 그 변수가 가리키는 객체의 필드 값을 바꿀 수 있다.
  ```
  <br>

* 실제 코드에서는 위와 같은 래퍼를 만들지 않고 직접 변수를 바꾼다.
  ```kotlin
  var counter = 0
  val inc = { counter++ }
  ```
  <br>
  
* 위 코드의 바이트코드를 디컴파일 해보면 아래와 같이 ```IntRef```라는 클래스를 만드는 것을 확인할 수 있다.
  ```java
  final IntRef counter = new IntRef();
  counter.element = 0;
  Function0 inc = (Function0)(new Function0() {
     // $FF: synthetic method
     // $FF: bridge method
     public Object invoke() {
        return this.invoke();
     }

     public final int invoke() {
        int var1 = counter.element++;
        return var1;
     }
  });
  ```
  
* Kotlin에서는 람다가 ```val```을 포획하면 Java와 마찬가지로 그 변수의 값이 복사된다. 하지만 람다가 ```var```변수를 포획하면 변수를 ```Ref```클래스 인스턴스에 넣는다. 
* 그 Ref 인스턴스에 대한 참조를 ```final```로 만들면 쉽게 람다로 포획할 수 있고, 람다 안에서는 ```Ref``` 인스턴스의 필드를 변경할 수 있다.

