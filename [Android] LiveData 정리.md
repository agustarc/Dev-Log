# LiveData
* **관찰 가능한 데이터 홀더 클래스**
  * 일반적인 Observable 클래스와 달리 ```LiveData```는 ```Activity```, ```Fragment```, ```Service``` 같은 앱 컴포넌트의 생명주기를 따르도록 구현되어 있다.
  * 활성화된 생명주기에 있는 앱 컴포넌트의 ```Observer```만 업데이트하도록 보장한다.
  * ```LiveData```는 현재 생명주기 상태가 ```STARTED``` 또는 ```RESUMED``` 일 때 ```Observer```가 활성상태인 것으로 간주하고 활성화 중인 ```Observer```들에게만 알림을 준다.
  * 데이터 바인딩을 이용하거나 아니면 직접 ```observe``` 메서드를 호출하여 ```LifecycleOwner```를 바인딩 시켜줘야 한다.
  * ```LifecycleOwner```가 활성상태가 아니라면 ```Observer```는 값이 변경되어도 호출되지 않는다. 
  * ```LifecycleOwner```가 파괴된 후에, ```Observer```는 자동으로 제거된다. 
  * **[Lifecycle.State](https://developer.android.com/reference/androidx/lifecycle/Lifecycle.State)**
    * ```CREATED```
    * ```DESTROYED```
    * ```INITIALIZED```
    * ```RESUMED```
    * ```STARTED```
    <br>
  
* **LiveData 사용 시 장점**
  * **UI를 데이터 상태와 일치 시킬 수 있다.**
    * ```LiveData```는 옵저버패턴을 따르고 있다. 
    * 생명주기 상태에 변화가 생기면 ```LiveData```는 ```Observer``` 인스턴스에 알림을 보낸다.
    * 앱의 데이터가 변할 때마다 매번 UI를 갱신하는 대신, 옵저버에 변화가 감지될 때마다 UI를 갱신하도록 바꿀 수 있다.
  * **메모리 릭의 요소를 없앨 수 있다.**
    * 옵저버들은 Lifecycle 인스턴스와 바인드 되고, 그 Lifecycle 인스턴스가 ```destroy``` 상태가 되면 자동적으로 지워진다.
  * **정지된 Activity들 때문에 Crash가 발생할 일이 없다.**
    * Activity가 백스택에 있을 때와 같이, 옵저버의 생명주기가 비활성화 상태일 때는, 어떠한 ```LiveData```이벤트도 받지 않는다.
  * **직접 생명주기를 핸들링할 필요가 없다.**
    * UI 컴포넌트는 단지 연관된 데이터를 관찰하기만 하고, resume / stop을 신경 쓸 필요가 없다. 
    * ```LiveData```가 상태변화를 감지하여 알아서 관리해준다.
  * **항상 최신 데이터를 유지한다.**
    * 생명주기가 비활성화 상태에서 활성화 상태로 변했을 때, UI 컴포넌트는 최신의 데이터를 받는다. 
    * 예를 들어, 백그라운드에 있던 Activity가 다시 포그라운드가 되었을 때, 최신의 데이터를 받는다.
  * **Configuration이 변경되었을 때 적절하게 대응한다.**
    * ```Activity```나 ```Fragment```가 ```Configuration```이 변경되어 재생성될 때, 그 컴포넌트들은 ```LiveData```로부터 즉시 사용 가능한 최신 데이터를 제공받는다.
  * **자원 공유**
    * 싱글턴 패턴을 이용해 시스템 서비스를 래핑 하여 ```LiveData``` 인스턴스가 공유될 수 있도록 확장할 수 있다.
    * ```LiveData``` 오브젝트가 시스템 서비스에 한 번 연결되면, 리소스가 필요한 모든 옵저버는 ```LiveData``` 인스턴스를 볼 수 있다.
  <br>

* **LiveData 확장**
  * 아래 공홈 샘플 코드는 ```LiveData``` 클래스가 어떻게 확장되는지를 보여준다.
    ```kotlin
    class StockLiveData(symbol: String) : LiveData<BigDecimal>() {
        private val stockManager = StockManager(symbol)

        private val listener = { price: BigDecimal ->
            value = price
        }

        override fun onActive() {
            stockManager.requestPriceUpdates(listener)
        }

        override fun onInactive() {
            stockManager.removeUpdates(listener)
        }
    }  
    ```
  * ```onActive``` 메서드는 ```LiveData``` 인스턴스가 활성화된 ```Observer```를 가지고 있을 때 호출된다. 
    * ```mActiveCount```가 0에서 1로 될 때 호출
  * ```onInactive``` 메서드는 LiveData 인스턴스가 어떤 활성화된 ```Observe```도 가지고 있지 않을 때 호출된다. 
    * ```mActiveCount```가 1에서 0으로 될 때 호출
  * ```setValue(T)``` 메서드는 ```LiveData``` 인스턴스의 값을 갱신하고, 변화에 대하여 활성 ```Observer```들에게 알려준다.
  <br>
  
* **LiveData를 싱글턴으로 사용**
  * ```LiveData```가 라이프 사이클을 인지할 수 있다는 사실은 여러 ```Activity```, ```Fragment```, ```Service``` 사이에서 공유될 수 있다는 것을 의미한다. 
  * ```LiveData```를 싱글턴으로 구현한 공홈 샘플 코드
    ```kotlin
    class StockLiveData(symbol: String) : LiveData<BigDecimal>() {
        private val stockManager: StockManager = StockManager(symbol)

        private val listener = { price: BigDecimal ->
            value = price
        }

        override fun onActive() {
            stockManager.requestPriceUpdates(listener)
        }

        override fun onInactive() {
            stockManager.removeUpdates(listener)
        }

        companion object {
            private lateinit var sInstance: StockLiveData

            @MainThread
            fun get(symbol: String): StockLiveData {
                sInstance = if (::sInstance.isInitialized) sInstance else StockLiveData(symbol)
                return sInstance
            }
        }
    }

    class MyFragment : Fragment() {
      override fun onActivityCreated(savedInstanceState: Bundle?) {
          StockLiveData.get(symbol).observe(this, Observer<BigDecimal> { price: BigDecimal? ->
              // Update the UI.
          })
      }
    }
    ```
    <br>

* **LiveData 변형**
  * ```Observer```에게 이벤트가 전달되기 전에 저장된 값을 변경하거나, 다른 값을 기반으로 다른 ```LiveData``` 인스턴스를 리턴해야 할 필요가 있을 수도 있다.
  * ```Transformations```에서 두 개의 메서드를 제공한다. ```map```, ```switchMap```
  * RxJava의 관점에서 보면 ```switchMap```은 ```flatMap```과 유사
  * 각각의 메서드 정의
  * ```map```
    ```java
    @MainThread
    public static <X, Y> LiveData<Y> map(
            @NonNull LiveData<X> source,
            @NonNull final Function<X, Y> mapFunction) {
        final MediatorLiveData<Y> result = new MediatorLiveData<>();
        result.addSource(source, new Observer<X>() {
            @Override
            public void onChanged(@Nullable X x) {
                result.setValue(mapFunction.apply(x));
            }
        });
        return result;
    }    
    ```
  * ```switchMap```
    ```java
    @MainThread
    public static <X, Y> LiveData<Y> switchMap(
            @NonNull LiveData<X> source,
            @NonNull final Function<X, LiveData<Y>> switchMapFunction) {
        final MediatorLiveData<Y> result = new MediatorLiveData<>();
        result.addSource(source, new Observer<X>() {
            LiveData<Y> mSource;

            @Override
            public void onChanged(@Nullable X x) {
                LiveData<Y> newLiveData = switchMapFunction.apply(x);
                if (mSource == newLiveData) {
                    return;
                }
                if (mSource != null) {
                    result.removeSource(mSource);
                }
                mSource = newLiveData;
                if (mSource != null) {
                    result.addSource(mSource, new Observer<Y>() {
                        @Override
                        public void onChanged(@Nullable Y y) {
                            result.setValue(y);
                        }
                    });
                }
            }
        });
        return result;
    }    
    ```
    <br>

* **여러 LiveData 병합하기**
  * LiveData의 서브 클래스로 ```MediatorLiveData``` 클래스가 있다.
  * 이름(Mediator) 그대로 중재자 역할
  * 여러 ```LiveData```의 갱신 알림을 한곳에서 받기 위한 클래스
  * ```MediatorLiveData```에는 ```addSource``` 메서드와 ```removeSource``` 메서드가 정의되어 있다.
  * 예를 들면 아래와 같은 시나리오에 사용될 수 있다.
    * 데이터베이스에 저장된 데이터와 관련된 ```LiveData``` 인스턴스
    * 네트워크로부터 받아오는 데이터와 관련된 ```LiveData``` 인스턴스
    * UI에서 양쪽 소스로부터 갱신 알림을 받기 위해 양쪽 ```LiveData```를 ```MediatorLiveData``` 인스턴스만 관찰하면 된다.
    <br>

* **LiveData는 기본적으로 불변**
  * ```LiveData```는 감싸고 있는 데이터를 수정하기 위한 메서드를 외부에 제공하지 않는다.
  * ```setValue```, ```postValue``` 모두 ```protected```로 선언되어 있음
  * Immutable은 ```LiveData```, Mutable은 ```MutableLiveData```
  <br>

* **LiveData 데이터 갱신**
  * 데이터를 갱신하려는 스레드가 UI 스레드면 ```setValue```
  * 데이터를 갱신하려는 스레드가 UI 스레드가 아니면 ```postValue```
  <br>
  
