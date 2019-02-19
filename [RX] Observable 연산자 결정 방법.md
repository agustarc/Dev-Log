# Observable 연산자 결정 방법
RX에는 정말 많은 연산자들이 있는데 주로 자주 사용하는 것들만 쓰는 것 같다. 
</br>
아래 연산자 결정 방법을 보고 상황에 맞게 적절한 연산자를 선택하는 연습을 하자.
</br>
http://reactivex.io/documentation/ko/operators.html#tree
</br>
</br>

## 새로운 Observable을 생성하고 싶은데 그 Observable이 
* 특정 항목을 생성해야 한다면 [Just](http://reactivex.io/documentation/operators/just.html)
  * 구독 시점에 호출된 함수를 통해 생성된 항목을 리턴해야 한다면 [Start](http://reactivex.io/documentation/operators/start.html)
  * 구독 시점에 호출된 ```Action```, ```Callable```, ```Runnable``` 또는 그와 유사한 함수 등을 통해 생성된 항목을 리턴해야 한다면 [From](http://reactivex.io/documentation/operators/from.html)
  * 지정된 시간 이후에 항목을 배출해야 한다면 [Timer](http://reactivex.io/documentation/ko/operators/timer.html)
* 특정 ```Array```, ```Iterable``` 또는 유사한 형태의 소스로부터 항목들을 배출해야 한다면 [From](http://reactivex.io/documentation/operators/from.html)
* 퓨처(Future)에서 항목을 조회해서 배출해야 한다면 [Start](http://reactivex.io/documentation/operators/start.html)
* 퓨처에서 연속된 항목을 가져와야 한다면 [From](http://reactivex.io/documentation/operators/from.html)
* 반복적으로 연속된 항목을 배출해야 한다면 [Repeat](http://reactivex.io/documentation/operators/repeat.html)
* 사용자가 정의한 로직을 통해 생성되어야 한다면 [Create](http://reactivex.io/documentation/operators/create.html)
* 각각의 옵저버가 Observable을 구독한 후에 생성되어야 한다면 [Defer](http://reactivex.io/documentation/operators/defer.html)
* 연속된 정수를 배출해야 한다면 [Range](http://reactivex.io/documentation/operators/range.html)
  * 특정 시간 간격별로 항목을 배출해야 한다면 [Interval](http://reactivex.io/documentation/operators/interval.html)
    * 특정 시간 후에 항목을 배출해야 한다면 [Timer](http://reactivex.io/documentation/operators/timer.html)
* 항목 배출 없이 실행을 완료해야 한다면 [Empty](http://reactivex.io/documentation/operators/empty-never-throw.html)
* 아무일도 하지 않아야 한다면 [Never](http://reactivex.io/documentation/operators/empty-never-throw.html)
</br>

## 다른 Observable을 결합시켜 새로운 Observable을 생성해야 한다
* 그리고 순서와 상관없이 전달된 모든 Observable이 가진 항목 전체를 배출해야 한다면 [Merge](http://reactivex.io/documentation/operators/merge.html)
* 그리고 전달된 Observable의 순서대로 Observable이 가진 모든 항목들을 배출해야 한다면 [Concat](http://reactivex.io/documentation/operators/concat.html)
* 생성하고 싶은 Observable은, 두 개 이상의 Observable이 가진 항목들을 순서대로 결합시켜 새로운 항목을 배출해야 하는데 
  * 각각의 Observable이 항목을 배출할 때마다 그 항목들을 결합시켜 배출해야 한다면 [Zip](http://reactivex.io/documentation/operators/zip.html)
  * Observable 중 하나라도 항목을 배출할 경우 마지막으로 배출된 항목들을 결합시켜 배출해야 한다면 [CombineLatest](http://reactivex.io/documentation/operators/combinelatest.html)
  * 하나의 Observable이 배출한 항목의 타임 윈도우가 열려있는 시간 동안 다른 Observable이 항목을 배출할 때마다 새로운 항목을 배출해야 한다면 [Join](http://reactivex.io/documentation/operators/join.html)
  * ```Pattern```과 ```Plan``` 중계자를 이용해서 항목을 배출해야 한다면 [And/Then/When](http://reactivex.io/documentation/operators/and-then-when.html)
* 그리고 가장 최근에 항목을 배출한 Observable을 통해서만 항목을 배출해야 한다면 [Switch](http://reactivex.io/documentation/operators/switch.html)
</br>

## Observable이 배출한 항목들을 변환한 후에 다시 배출해야 하는데
* 함수와 함께 항목을 한 에 하나씩 변환 후 배출해야 한다면 [Map](http://reactivex.io/documentation/operators/map.html)
* 해당 Observable이 배출한 모든 항목들을 하나의 Observable이 배출하는 형태로 배출해야 한다면 [FlatMap](http://reactivex.io/documentation/operators/flatmap.html)
  * 순서대로 Observable이 배출한 항목들을 연결 지어 배출해야 한다면 [ConcatMap](http://reactivex.io/documentation/operators/flatmap.html)
* 앞에서 실행된 결과를 기반으로 항목을 변환한 후 배출해야 한다면 [Scan](http://reactivex.io/documentation/operators/scan.html)
* 타임스탬프를 추가하여 변환한 후 배출해야 한다면 [Timestamp](http://reactivex.io/documentation/operators/timestamp.html)
* 항목 배출 전까지 경과한 시간을 가리키는 객체로 변환한 후에 배출해야 한다면 [TimeInterval](http://reactivex.io/documentation/operators/timeinterval.html)
</br>

## Observable이 항목을 배출하기 전에 항목의 배출 시간을 지연시켜야 한다면 [Delay](http://reactivex.io/documentation/operators/delay.html)
</br>

## Observable이 배출하는 항목들과 알림 들을 다시 항목들로 변환 후 배출해야 되는데
* 이때 배출하는 항목들을 알림 객체로 감싸서(wrapping) 배출해야 한다면 [Materialize](http://reactivex.io/documentation/operators/materialize-dematerialize.html)
  * 이 알림 객체가 다시 풀릴 수(unwrapping) 있다면 [Dematerialize](http://reactivex.io/documentation/operators/materialize-dematerialize.html)
</br>

## Observable이 배출하는 모든 객체를 무시하고 completed/error 알림만 전달해야 한다면 [IgnoreElements](http://reactivex.io/documentation/operators/ignoreelements.html)
</br>

## Observable이 가진 항목을 그대로 배출하지만 배출 전에 다른 항목들을 먼저 배출될 수 있도록 추가해야 한다면 [StartWith](http://reactivex.io/documentation/operators/startwith.html)
* 만약 소스 Observable이 비어있을 경우 기본 항목을 추가해야 한다면 [DefaultIfEmpty](http://reactivex.io/documentation/operators/defaultifempty.html)
</br>

## Observable이 배출하는 항목들을 모아둔 후 버퍼로 다시 배출해야 한다면 [Buffer](http://reactivex.io/documentation/operators/buffer.html)
* 그중 마지막에 배출된 항목이 추가된 버퍼만 배출해야 한다면 [TakeLastBuffer](http://reactivex.io/documentation/operators/takelast.html)
</br>

## 하나의 Observable을 여러 Observable로 나눠야 한다면 [Window](http://reactivex.io/documentation/operators/window.html)
* 그중 유사한 항목들을 같은 Observable에 모아 두어야 한다면 [GroupBy](http://reactivex.io/documentation/operators/groupby.html)
</br>

## Observable이 배출한 특정 항목을 조회해야 하는데
* Observable이 완료되기 전에 마지막으로 배출한 항목을 조회해야 한다면 [Last](http://reactivex.io/documentation/operators/last.html)
* 배출된 항목이 단지 하나이고 이것을 조회해야 한다면 [Single](http://reactivex.io/documentation/operators/first.html)
* 배출된 첫 번째 항목을 조회해야 한다면 [First](http://reactivex.io/documentation/operators/first.html)
</br>

## Observable의 특정 항목만 재배출 해야 하는데
* 어떤 조건을 만족시키지 않는 항목들을 필터링해서 재배출 해야 한다면 [Filter](http://reactivex.io/documentation/operators/filter.html)
* 첫 번째 항목만 재배출 해야 한다면 [First](http://reactivex.io/documentation/operators/first.html)
* 처음 몇 개의 항목들만 재배출 해야 한다면 [Take](http://reactivex.io/documentation/operators/take.html)
* 마지막 항목만 재배출 해야 한다면 [Last](http://reactivex.io/documentation/operators/last.html)
* 몇 번째 위치한 항목만 재배출 해야 한다면 [ElementAt](http://reactivex.io/documentation/operators/elementat.html)
* 재배출할 항목들이 처음 몇 개 이후의 것들일 경우
  * 처음 몇 개의 항목들 이후의 것들이라면 [Skip](http://reactivex.io/documentation/operators/skip.html)
  * 특정 조건을 만족시킨 이후의 것들이라면 [SkipWhile](http://reactivex.io/documentation/operators/skipwhile.html)
  * 초기 특정 시간 이후에 배출된 항목들이라면 [Skip](http://reactivex.io/documentation/operators/skip.html)
  * 두 번째 Observable이 항목을 배출한 이후의 것들이라면 [SkipUntil](http://reactivex.io/documentation/operators/skipuntil.html)
* 마지막 항목 몇 개를 제외한 경우
  * 마지막 몇 개 항목을 제외한 것들이라면 [SkipLast](http://reactivex.io/documentation/operators/skiplast.html)
  * 특정 조건을 만족할 때까지의 것들이라면 [TakeWhile](http://reactivex.io/documentation/operators/takewhile.html)
  * 소스 Observable이 완료되기 이전 특정 시간 동안 배출한 것들을 제외한 것이라면 [SkipLast](http://reactivex.io/documentation/operators/skiplast.html)
  * 두 번째 Observable이 항목을 배출한 이후에 배출된 것들을 제외한 것이라면 [TakeUntil](http://reactivex.io/documentation/operators/takeuntil.html)
* 주기적으로 Observable을 샘플링해서 재배출해야 한다면 [Sample](http://reactivex.io/documentation/operators/sample.html)
* 특정 시간이 지나고 나서 배출된 항목들만 재배출 해야 한다면 [Debounce](http://reactivex.io/documentation/operators/debounce.html)
* 이미 배출된 항목과 동일한 것들을 제외하고 재배출 해야 한다면 [Distinct](http://reactivex.io/documentation/operators/distinct.html)
  * 만약 중복된 항목이 바로 연이어 배출된다면 [DistinctUntilChanged](http://reactivex.io/documentation/operators/distinct.html)
* 항목 배출이 시작된 이후에 얼마 동안 구독을 지연시켜야 한다면 [DelaySubscription](http://reactivex.io/documentation/operators/delay.html)
</br>

## 항목들을 배출하는 Observable 컬렉션 중에 첫 번째로 항목을 배출하는 Observable의 항목만 배출해야 한다면 [Amb](http://reactivex.io/documentation/operators/amb.html)
</br>

## Observable이 배출한 연속된 항목 전체를 평가해야 한다
* 그리고 항목 전체가 테스트를 통과했는지를 가리키는 boolean 타입 항목 하나를 배출해야 한다면 [All](http://reactivex.io/documentation/operators/all.html)
* 그리고 항목 전체 중 하나라도 테스트를 통과했는지를 가리키는 boolean 타입 항목 하나를 배출해야 한다면 [Contains](http://reactivex.io/documentation/operators/contains.html)
* 그리고 Observable이 항목을 배출하지 못했는지를 가리키는 boolean 타입 항목 하나를 배출해야 한다면 [IsEmpty](http://reactivex.io/documentation/operators/contains.html)
* 그리고 두 Observable이 같은 순서대로 항목들을 배출했는지를 가리키는 boolean 타입 하나를 배출해야 한다면 [SequenceEqual](http://reactivex.io/documentation/operators/sequenceequal.html)
* 그리고 전체 배출된 항목의 평균 값을 배출해야 한다면 [Average](http://reactivex.io/documentation/operators/average.html)
* 그리고 전체 배출된 항목의 합계를 배출해야 한다면 [Sum](http://reactivex.io/documentation/operators/sum.html)
* 그리고 얼마나 많은 항목들이 배출됐는지를 배출해야 한다면 [Count](http://reactivex.io/documentation/ko/operators/count.html)
* 그리고 가장 큰 값을 가진 항목을 배출해야 한다면 [Max](http://reactivex.io/documentation/operators/max.html)
* 그리고 가장 작은 값을 가진 항목을 배출해야 한다면 [Min](http://reactivex.io/documentation/operators/min.html)
* 배출되는 항목 순서대로 각각에 집계 함수를 적용해서 결과를 배출해야 한다면 [Scan](http://reactivex.io/documentation/operators/scan.html)
</br>

## Observable이 배출한 전체 항목들을 특정 자료구조로 배출하고 싶다면 [To](http://reactivex.io/documentation/operators/to.html)
</br>

## 연산자가 특정 스케줄러 상에서 동작해야 한다면 [SubscribeOn](http://reactivex.io/documentation/operators/subscribeon.html)
* 연산자가 옵저버한테 알림을 줄 때 동작할 스케줄러를 지정해야 한다면 [ObserveOn](http://reactivex.io/documentation/operators/observeon.html)
</br>

## 특정 이벤트가 발생할 때 Observable 상에서 어떤 동작을 실행시켜야 한다면 [Do](http://reactivex.io/documentation/operators/do.html)
</br>

## 오류가 발생했을 때 Observable이 옵저버에게 오류를 알려야 한다면 [Throw](http://reactivex.io/documentation/operators/empty-never-throw.html)
* 만약 항목이 배출되지 않은 상태에서 특정 시간이 경과했다면 [Timeout](http://reactivex.io/documentation/operators/timeout.html)
</br>

## 자연스럽게 Observable을 복구해야 하는데
* 타임아웃이 발생한 경우 백업 Observable로 전환시켜 복구해야 한다면 [Timeout](http://reactivex.io/documentation/operators/timeout.html)
* 앞에서 발생한 오류 알림으로부터 복구해야 한다면 [Catch](http://reactivex.io/documentation/operators/catch.html)
  * 이전 Observable에 재구독을 시도해야 한다면 [Retry](http://reactivex.io/documentation/operators/retry.html)
</br>

## 동일한 생명주기를 가진 리소스를 Observable로 생성해야 한다면 [Using](http://reactivex.io/documentation/operators/using.html)
</br>

## Observable을 구독하고 Observable이 완료될 때까지 블로킹 상태에 있는 ```퓨처(Future)```를 전달받고 싶다면 [Start](http://reactivex.io/documentation/operators/start.html)
</br>

## 구독자의 요청 전까지 Observable이 항목을 구독자에게 배포하지 말아야 한다면 [Publish](http://reactivex.io/documentation/operators/publish.html)
* 그리고 맨 마지막 항목만을 배출해야 한다면 [PublishLast](http://reactivex.io/documentation/operators/publish.html)
* 그리고 배출 이후에 구독자가 구독을 시작했다 하더라도 동일하게 배출 순서를 전달해야 한다면 [Relay](http://reactivex.io/documentation/operators/replay.html)
* 하지만 모든 구독자가 한 번에 구독을 해지할 수 있어야 한다면 [RefCount](http://reactivex.io/documentation/operators/refcount.html)
* 그리고 배출을 시작하도록 Observable에게 요청해야 한다면 [Connect](http://reactivex.io/documentation/operators/connect.html)
