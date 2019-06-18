# debounce
  * **흐름 제어 연산자**
    * Observable의 시간 흐름이 지속되는 상태에서 다른 항목들은 배출하지 않고 특정 시간마다 그 시점에 존재하는 항목 하나를 Observable로부터 배출한다.
    * 빠르게 연속 이벤트를 처리하는 흐름 제어 연산자
    <br>
  * **마블 다이어그램**
    * ![Alt text](/images/rx_debounce.png)
    <br>
  * **어떤 경우에 사용할까?**
    * 안드로이드 EditText의 텍스트 변경 이벤트를 전달받아 자동 완성 API 요청하는 등의 케이스에 유용.
    * 불필요한 IO 요청을 방지하고 서버에 부하를 주지 않도록 설계할 수 있다.
      ```kotlin
      edit.textChangeEvents()
          .debounce(500, TimeUnit.MILLISECONDS)
          .map { it.text }
          .filter { it.isNotEmpty() }
          .observeOn(AndroidSchedulers.mainThread())
          .subscribe({

          }, {

          })    
      ```
    
