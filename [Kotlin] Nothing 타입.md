# Nothing 타입
* **아무런 값도 포함하지 않는 타입**

  * 실제 ```Nothing```의 선언을 찾아보면 단지 클래스 선언만 존재
    ```kotlin
    public class Nothing private constructor()  
    ```
  * 타입이기 때문에 제네릭에 사용될 수 있다. 
  * 어떠한 타입도 가능하다면 ```Any```, 아무런 값도 필요하지 않는 경우 ```Nothing```
  * 제네릭을 사용하여 ```Nothing```을 활용할 경우 아래와 같은 코드 작성이 가능
  ```kotlin
  sealed class Result<out R> {
      data class Success<out T>(val data: T) : Result<T>()
      data class Error(val exception: Throwable) : Result<Nothing>()
      object Loading : Result<Nothing>()

      override fun toString(): String {
          return when(this) {
              is Success<*> -> "Success[data=$data]"
              is Error -> "Error[exception=$exception]"
              Loading -> "Loading"
          }
      }
  }  
  ```
  * ```Success```가 아닌 경우 에러만 있거나 값이 없기 때문에 이럴 때 ```Nothing```으로 활용할 수 있다.
  <br>
  
* **```null```로 초기화할 때 컴파일러가 타입을 추론할 수 없다면 ```Nothing?``` 타입으로 정의된다.**  
  * Kotlin에서는 변수 선언 시 타입을 생략할 수 있다.
  * 만약 타입을 생략한 상태에서 선언과 동시에 ```null```을 대입하면 컴파일러는 타입을 추론할 수 없기에 ```Nothing?``` 타입으로 유추한다. 
  ```kotlin
  val x = null          // `x` has type `Nothing?`
  val l = listOf(null)  // `l` has type 'List<Nothing?>`
  ```
  ```kotlin
  internal object ThrowingCaller : Caller<Nothing?> {
      override val member: Nothing?
          get() = null

      override val parameterTypes: List<Type>
          get() = emptyList()

      override val returnType: Type
          get() = Void.TYPE

      override fun call(args: Array<*>): Any? {
          throw UnsupportedOperationException("call/callBy are not supported for this declaration.")
      }
  }  
  ```
  <br>
  
* **함수가 예외를 던진다는 것을 명시하기 위한 반환 타입**
  * Kotlin에는 Java처럼 함수 시그니처에 ```throws 예외```를 정의하는 것이 없다. 
  * Kotlin은 예외 처리를 강제하지 않는다.
  * 함수가 아무런 값도 반환하지 않고 단지 예외를 던지는 경우 반환 타입에 ```Nothing```을 정의한다. 
  * 어떤 경우에 사용되는지는 아래 Kotlin 코드를 보면 이해가 쉬울 듯
  ```kotlin
  public inline fun TODO(reason: String): Nothing = throw NotImplementedError("An operation is not implemented: $reason")  
  ```
  ```kotlin
  internal object EmptyIterator : ListIterator<Nothing> {
      override fun hasNext(): Boolean = false
      override fun hasPrevious(): Boolean = false
      override fun nextIndex(): Int = 0
      override fun previousIndex(): Int = -1
      override fun next(): Nothing = throw NoSuchElementException()
      override fun previous(): Nothing = throw NoSuchElementException()
  }  
  ```
  ```kotlin
  private object EnhancedTypeAnnotationDescriptor : AnnotationDescriptor {
      private fun throwError(): Nothing = error("No methods should be called on this descriptor. Only its presence matters")
      override val type: KotlinType get() = throwError()
      override val allValueArguments: Map<Name, ConstantValue<*>> get() = throwError()
      override val source: SourceElement get() = throwError()
      override fun toString() = "[EnhancedType]"
  }  
  ```
  ```kotlin
  internal object EmptyList : List<Nothing>, Serializable, RandomAccess {
      private const val serialVersionUID: Long = -7390468764508069838L

      override fun equals(other: Any?): Boolean = other is List<*> && other.isEmpty()
      override fun hashCode(): Int = 1
      override fun toString(): String = "[]"

      override val size: Int get() = 0
      override fun isEmpty(): Boolean = true
      override fun contains(element: Nothing): Boolean = false
      override fun containsAll(elements: Collection<Nothing>): Boolean = elements.isEmpty()

      override fun get(index: Int): Nothing = throw IndexOutOfBoundsException("Empty list doesn't contain element at index $index.")
      override fun indexOf(element: Nothing): Int = -1
      override fun lastIndexOf(element: Nothing): Int = -1

      override fun iterator(): Iterator<Nothing> = EmptyIterator
      override fun listIterator(): ListIterator<Nothing> = EmptyIterator
      override fun listIterator(index: Int): ListIterator<Nothing> {
          if (index != 0) throw IndexOutOfBoundsException("Index: $index")
          return EmptyIterator
      }

      override fun subList(fromIndex: Int, toIndex: Int): List<Nothing> {
          if (fromIndex == 0 && toIndex == 0) return this
          throw IndexOutOfBoundsException("fromIndex: $fromIndex, toIndex: $toIndex")
      }

      private fun readResolve(): Any = EmptyList
  }  
  ```
  
