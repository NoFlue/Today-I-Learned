# Flow 한계점
Flow의 구조는 다음과 같습니다.
<p align="center">
	<img src="https://developer.android.com/static/images/kotlin/flow/flow-entities.png?hl=ko">
</p>

하지만 Flow는 데이터의 흐름만 발생시키기 때문에 소비자에서 데이터를 저장할 수 없다는 단점이 있습니다.  
<br>

Flow만 이용해 데이터를 저장하기 위해선 다음과 같은 방법을 사용해야 합니다.
- 화면이 재구성될 때 마다 데이터 가져오기
- 데이터를 ViewModel에 저장하여 사용하기

**첫 번째 방법**은 OS의 설정이 변경될 때 마다 `onDestory` 와 `onCreate` 가 매번 호출되어 데이터를 서버 혹은 DB로 부터 가져오기 때문에 매우 비효율적입니다.
<br>

**두 번째 방법**은 OS의 설정이 변경되더라도 ViewModel의 생명주기는 액티비티의 생명주기보다 길기 때문에 **첫 번째 방법** 보다 효율적인 방법입니다.  
하지만 ViewModel에서 데이터를 저장하기 위해선 데이터 홀더 변수가 필요하기 때문에 UI가 데이터 홀더 변수를 구독하기 위해선 `fetching 로직` 을 작성해야 합니다.
<br>

**두 번째 방법** 에서 데이터 홀더 변수와 Flow를 같이 사용해 Flow의 최신 데이터만 저장하는 방법을 사용해 문제를 해결할 수 있지만 UI 상태가 많아지게 되면 보일러 플레이트 코드를 만들게 된다는 문제점이 존재합니다.  

<br>
<br>
<br>

# StateFlow
`StateFlow` 는 위 문제들을 해결하기 위해 나왔습니다. `StateFlow` 는 데이터 홀더 역할을 하면서 Flow의 데이터 스트림 역할까지 합니다.
<br>

`StateFlow` 는 `Flow` 와 다르게 `Hot stream` 입니다.
> `Cold stream` : collect() 를 호출할 때 마다 flow block을 재실행합니다.

> `Hot stream` : collect() 를 호출하더라도 flow block이 실행되지 않고, collect() 시점에서 emit된 데이터를 전달받습니다.

<br>

`StateFlow` 의 상태를 업데이트하고 흐름에 전송하려면 `MutableStateFlow` 클래스의 `value` 속성에 새 값을 할당해야 합니다.
```kotlin
private val _state = MutableStateFlow(10) // 10을 초기 값으로
val state: StateFlow<Int> = _state
```
<br>

값을 전달할 떄는 `emit()` 을 사용해도 되지만 value 속성에 값을 전달할 수 있습니다.
```kotlin
_state.emit(15)

_state.value = 15
```
<br>
<br>

아래는 `StateFlow` 의 데이터를 수집하는 예제입니다.
```kotlin
// TestViewModel.kt
private val _state = MutableStateFlow(999) // 10을 초기 값으로
val state: StateFlow<Int> = _state

suspend fun startSendDataToStateFlow(){
    repeat(5){
        _state.value = it
        delay(500)
    }
}


// MainActivyty.kt
override fun onCreate(savedInstanceState: Bundle?){
    // Define TestViewModel

    lifecycleScope.launch(Dispatcher.Main){
        testViewModel.state.collect{
            Log.i(TAG, "StateFlow : $it ${Thread.currentThread().name}")
        }
    }

    lifecycleScope.launch(Dispatcher.Main){
        testViewModel.startSendDataToStateFlow()
    }
}
```

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/183913540-4932662e-baa4-4552-935b-759ac420a66a.png">
</p>

해당 코드는 `emit` 하기 전 데이터 스트림을 수집하기 때문에 초기값인 `999` 가 먼저 나온 후 전달 받은 데이터를 출력합니다.
<br>

```kotlin
// TestViewModel.kt
private val _state = MutableStateFlow(999) // 10을 초기 값으로
val state: StateFlow<Int> = _state

suspend fun startSendDataToStateFlow(){
    repeat(5){
        _state.value = it
        delay(500)
    }
}


// MainActivyty.kt
override fun onCreate(savedInstanceState: Bundle?){
    // Define TestViewModel

    lifecycleScope.launch(Dispatcher.Main){
        testViewModel.startSendDataToStateFlow()
    }

    lifecycleScope.launch(Dispatcher.Main){
        delay(1100)
        testViewModel.state.collect{
            Log.i(TAG, "StateFlow : $it ${Thread.currentThread().name}")
        }
    }
}
```

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/183915169-e5e8aafc-9d85-41bd-960d-faede659aff8.png">
</p>

데이터 스트림의 수집을 1.2초 후에 시작했지만 `flow block` 이 이미 실행됐기 때문에 값을 2부터 전달 받게 됩니다.
<br>

위 예제를 통해 `StateFlow` 가 왜 `Hot stream` 인지 알게 되었습니다.

<br>
<br>
<br>

# stateIn
기존 `Flow` 를 `StateFlow` 로 변환하기 위해 `stateIn` 함수를 사용하여 변환할 수 있습니다.
