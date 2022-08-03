> 해당 내용은 [Kotlin World](https://kotlinworld.com/) 글을 통해 공부하여 정리했습니다.

<br>

# Android Coroutine Scope
`CoroutineScope` 는 `Coroutine Job` 이 실행되는 범위이며, `CoroutineScope` 가 해제되면 `Job` 또한 해제가 됩니다.  
그렇다면 만약에 `CoroutineScope` 가 생명 주기에 따라 올바르게 할당 및 해제되지 않으면 어떻게 될까요?  
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
위 코드를 실행하면 액티비티가 파괴되어도 Flow를 계속 collect 하는 모습을 볼 수 있습니다. 이 상태에서 다시 액티비티를 생성하면 기존 Job은 취소되지 못한 채 새로운 Job이 수행되어 기존 Job은 제어권을 잃은 채 계속 수행되기 떄문에 메모리 낭비가 일어납니다.  
<br>

그렇기에 안드로이드에서는 메모리 낭비를 방지하기 위해 상황에 맞는 `Coroutine Scope` 가 존재합니다.

<br>
<br>
<br>

# lifecycleScope 와 viewModelScope
안드로이드에는 액티비티의 생명주기에 맞춰진 `lifecycleScope` 와 viewModel의 생명주기에 맞춰진 `viewModelScope` 가 있습니다.  
<br>

### lifecycleScope
`lifecycleScope` 는 `LifecycleOwner` 인터페이스의 확장 프로퍼티이며, 액티비티의 생명주기를 따릅니다.
```kotlin
public val LifecycleOwner.lifecycleScope: LifecycleCoroutineScope  
	get() = lifecycle.coroutineScope
```
그렇기에 lifecycleScope를 사용하게 되면 액티비티가 파괴될 때 같이 해제됩니다.
```kotlin
// OnCreate()

val num: Flow<String> = flow{
	repeat(100){
		emit("number : $it")
		delay(1000)
	}
}

lifecycleScope.launch{
	num.collect{
		println(it)
	}
}
```
<br>

하지만 lifecycleScope 에도 문제점이 있는데 액티비티가 `onStop` 상태일 경우엔 Job이 백그라운드에서 계속 수행되기 때문에 앱의 메모리 사용량을 증가시킬 수 있고, 원치 않는 데이터를 사용이 일어날 수 있습니다.  
<br>

문제를 해결하기 위해 `onStop` 에서 Job을 취소하면 되지만 보일러 플레이트 코드를 생성하는 것이며, 만약 취소하는 코드를 깜빡하고 작성하지 않을 경우 문제점이 해결되지 않습니다.  
<br>

이 문제를 해결하기 위해 안드로이드에서는 `onStart` 와 `onStop` 에서 Job을 수행 및 취소를 할 수 있는 API인 `repeatOnLifecycle` 함수를 제공하고 있습니다. 
```kotlin
public suspend fun LifecycleOwner.repeatOnLifecycle(  
	state: Lifecycle.State,  
	block: suspend CoroutineScope.() -> Unit  
): Unit = lifecycle.repeatOnLifecycle(state, block)
```
`repeatOnLifecycle` 함수는 액티비티의 상태를 인자로 받아 앱이 포그라운드에 있을 경우에만 Job을 수행하게 해줍니다.
```kotlin
// OnCreate()

val num: Flow<String> = flow{
	repeat(100){
		emit("number : $it")
		delay(1000)
	}
}

lifecycleScope.launch{
	repeatOnLifecycle(Lifecycle.State.STARTED){
		num.collect{
			println(it)
		}
	}
}
```
<br>
repeatOnLifecycle 함수를 사용함으로 백그라운드에서 메모리 낭비를 막을 수 있게 됐습니다. 하지만 Job이 백그라운드에서 수행해야 할 경우 사용하면 안되므로 상황에 따라 활용을 해야합니다.

<br>
<br>

### viewModelScope
`viewModelScope` 또한 `ViewModel` 클래스를 확장한 프로퍼티이므로 ViewModel의 생명주기를 따릅니다.
```kotlin
public val ViewModel.viewModelScope: CoroutineScope  
	get() {  
		val scope: CoroutineScope? = this.getTag(JOB_KEY)  
		if (scope != null) {  
			return scope  
	    }  
	    return setTagIfAbsent(  
			JOB_KEY,  
			CloseableCoroutineScope(SupervisorJob() + Dispatchers.Main.immediate)  
		)  
	}
```
ViewModel 에서 Job을 생성할 경우 viewModelScope를 사용합니다.
```kotlin
class MainViewModel: ViewModel(){
	val num: Flow<String> = flow{
		repeat(1000){
			emit("number : $it")
			delay(1000)
		}
	}

	fun collectFlow(){
		viewModelScope.launch{
			num.collect{
				println(it)
			}
		}
	}
}
```