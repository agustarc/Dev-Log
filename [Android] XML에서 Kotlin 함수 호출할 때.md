* 보통 ```Java```와 ```Kotlin```을 혼합해서 쓰다 보면 ```@Jvm```류의 어노테이션을 사용하게 된다. 
</br>

* 그런데 ```Kotlin``` 100% 코드에서도 ```@Jvm``` 어노테이션을 사용하게 될 줄은 몰랐다.
</br>

* Data Binding을 사용하면 바인딩 클래스가 ```Java```로 생성되기 때문에 ```XML```내에서 ```Kotlin```에 대한 접근은 ```Java```에서 ```Kotlin```으로 접근하는 것과 같다.
</br>

```xml
<androidx.appcompat.widget.AppCompatImageView
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:visibility=@{PostType.isImage(post.contentType) ? View.VISIBLE : View.GONE }
/>
```

```Kotlin
object PostType {
  @JvmStatic
  fun isImage(type: String) = // return true or false
}
```
</br>

* 위의 예제 코드에서 ```@JvmStatic``` 어노테이션을 제거하게 되면 ```XML```에서는 ```PostType.isImage```를 인식하지 못하기 때문에 ```Companion```을 붙여 쓰거나 ```@JvmStatic``` 어노테이션을 사용해야 한다.
