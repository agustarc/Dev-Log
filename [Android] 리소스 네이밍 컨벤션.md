* 리소스 네이밍을 잘해두면 확실히 추후 유지 보수나 생산성에 있어서 좋은 점이 많다.
</br>

* 리소스 네이밍에 대해서 잘 정리해둔 링크
  * [A successful XML naming convention](https://jeroenmols.com/blog/2016/03/07/resourcenaming/)
  * [Android resource naming convention](https://medium.com/mindorks/android-resource-naming-convention-42e4e8026614)
</br>

![Alt text](/images/resource_naming.png)

* **Layouts**
```xml
<WHAT>_<WHERE>
```
</br>

* **Strings**
```xml
<WHERE>_<DESCRIPTION> or <HOW>_<DESCRIPTION>
String의 경우 WHERE와 HOW를 섞어 쓰는 편
```
</br>

* **Drawables**
```xml
<WHERE>_<DESCRIPTION>_<SIZE> or <WHAT>_<DESCRIPTION>_<SIZE>
Drawable의 경우 WHERE보다 WHAT으로 시작하는 네이밍 룰이 괜찮은 것 같은데 프로젝트 상황에 따라 다를 듯
```
</br>

* **Dimensions**
```xml
<WHAT>_<WHERE>_<DESCRIPTION>_<SIZE>
```
</br>

* **Ids**
```xml
<WHAT>_<WHERE>_<DESCRIPTION>
```
</br>
