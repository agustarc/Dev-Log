# object 키워드 사용 방식에 따른 차이
* object declaration : Singleton
* object expression : Non-Singleton
</br>

# object declaration
* ```class``` 선언을 ```object```로 하면 Singleton이 된다.
```kotlin
object Singleton
```
* Java로 디컴파일된 소스를 보면 원리 이해가 쉽다.
```java
public final class Singleton {
   public static final Singleton INSTANCE;

   static {
      Singleton var0 = new Singleton();
      INSTANCE = var0;
   }
}
```
</br>

* ```object``` 선언은 생성자를 가질 수 없다.
* ```object``` 안에 ```class```처럼 멤버를 가질 수 있다.
* ```class``` 내부에는 정의할 수 없는 ```const```를 정의할 수 있다.
* ```object``` 내부에 메소드를 정의하면 인스턴스 메소드로 정의된다.
```kotlin
object Singleton {
    fun doSomething() = Unit
}
```
```java
public final class Singleton {
   public static final Singleton INSTANCE;

   public final void doSomething() {
   }

   static {
      Singleton var0 = new Singleton();
      INSTANCE = var0;
   }
}
```
* ```class``` or ```object``` 내부에 중첩 ```object``` 선언이 가능하다.
* ```class``` or ```object``` 내부에 중첩으로 ```object``` 선언하면 Java의 ```static inner class``` 형태가 된다.
* ```object``` 확장 가능한 ```class``` 및 ```interface```를 상속받을 수 있다.
```kotlin
object Singleton : Comparable<Int> {
    private const val x: Int = 100

    override fun compareTo(other: Int) = x.compareTo(other)
}
```

# companion object
* ```companion object```는 ```class``` 당 한 개만 선언이 가능하다. ```object```는 여러 개 선언 가능
* ```companion object```도 이름을 가질 수 있다.
```kotlin
class Person {
    companion object Factory {

    }
}
```
* 이름은 생략 가능하고 생략된다면 ```Companion```이라는 이름으로 생성된다.
* 착각할 수 있는 사실은 ```object```나 ```companion object``` 모두 내부에 정의된 메소드는 인스턴스 메소드다. 
* 아래와 같이 ```companion object``` 내부에서 Outer class의 멤버 접근은 불가능하다. 
```kotlin
class Person(val name: String) {
    companion object : Factory<Person> {
        override fun create(): Person = Person(name) // compile error 
    }
}
```
* Outer class에 대한 접근을 하기 위해서는 ```inner``` 키워드를 붙여야 하는데 ```object```는 ```inner``` 키워드를 허용하지 않는다.
* 당연한 것이 ```object```로 선언하면 ```static class```가 되기 때문
* Java에서 ```object```에 선언된 메소드를 호출하려면 ```@JvmStatic``` 어노테이션을 붙여야 한다.
```kotlin
class Person {
    companion object : Factory<Person> {
        @JvmStatic
        override fun create(): Person = Person()
    }
}
```
```java
Person person = Person.create();
```
* ```@JvmStatic``` 어노테이션은 Java를 위한 static factory 메소드를 생성해준다.
```java
@JvmStatic
@NotNull
public static Person create() {
  return Companion.create();
}
```

# object expression
* 추상 클래스 인스턴스화 또는 익명 클래스를 선언할 때 사용한다. 
* 익명 클래스를 선언할 때 Kotlin ```class```의 기본값은 ```final```이기 때문에 접근 제어자를 변경해줘야 한다.
```kotlin
abstract class Animal

val animal = object : Animal() {

}
```
* 인터페이스 인스턴스화
```kotlin
interface Person {
    fun say()
}

val person = object : Person {
    override fun say() {

    }
}
```
* 단순 오브젝트 생성
```kotlin
val point = object {
    val x: Int = 10
    val y: Int = 10
}
```
* 위와 같이 이름이나 타입 없이 ```object```를 선언했을 경우 Java로 디컴파일된 소스를 보면 아래와 같이 ```Object``` 타입의 인스턴스를 생성하고 멤버로 ```x```, ```y```를 가지게 된다.
```java
Object var1 = new Object() {
   private final int x = 10;
   private final int y = 10;

   public final int getX() {
      return this.x;
   }

   public final int getY() {
      return this.y;
   }
};
```
