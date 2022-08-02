> 해당 내용은 [Kotlin World](https://kotlinworld.com/) 글을 통해 공부하여 정리했습니다.

<br>

# Android Coroutines Scope
`CoroutineScope` 는 `Coroutine Job` 이 실행되는 범위이며, `CoroutineScope` 가 해제되면 `Job` 또한 해제가 됩니다.  
그렇다면 만약에 `CoroutineScope` 가 생명 주기에 따라 옳바르게 할당 및 해제되지 않으면 어떻게 될까요?  
<br>
아래는 `GlobalScope` 를 이용해 1초 마다 숫자를 출력하는 `Flow` 를 `Collect` 하는 코드입니다.
```kotlin
// OnCreate()

val num: Flow<String> = flow{
	repeat(100){
		emit("number : $it")
		delay(1000)
	}
}

GlobalScope.launch{
	num.collect{
		println(it)
	}
}
```
위 코드를 실행하면 액티비티가 파괴 되어도 Flow를 수집하는 작업은 멈추지 않기 때문에 메모리 낭비가 일어나게 됩니다.
> 실제로 코드를 실행해봤지만 뒤로가기로는 액티비티가 파괴가 되지 않기 때문에 예시를 찍지 못하고 가져오게 되었다
