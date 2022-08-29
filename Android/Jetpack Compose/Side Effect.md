# 부수 효과
`Composable` 은 각자 Lifecycle을 보유하고 있고, 단방향으로만 상태를 전달하는 특성이 있습니다.
<br>

Composable을 사용할 때 여러 Composable을 겹쳐서 사용합니다. 상태 호이스팅을 사용해 외부 Composable이 내부 Composable에게 상태를 단방향으로 전달해줍니다.  
하지만 만약 내부 Composable에서 상태를 변경하게 될 경우 어떻게 될까? 이런 경우엔 Composable이 양방향 전달을 하기 때문에 예측할 수 없는 효과가 생기게 되는데 이를 `Side Effect` 라고 합니다.  
<br>

`부수 효과(Side Effect)` 는 컴포저블 외부에서 발생 하는 앱의 상태에 대한 변경사항입니다.
<br>

Compose는 부수 효과를 처리하기 위한 `Effect API` 를 제공합니다.
- LaunchedEffect : Composable Lifecycle Scope 에서 suspend fun 을 실행하기 위해 사용
- DisposableEffect : Composable이 Dispose 될 떄 정리되어야 할 Side Effect를 정의하기 위히 사용
- SideEffect : Compose의 State를 Compose에서 관리하지 않는 객체와 공유하기 위해 사용

<br>
<br>
<br>

# LaunchedEffect
```kotlin
@Composable
fun LaunchedEffect(
    key1: Any?,
    block: suspend CoroutineScope.() -> Unit
)
```
`LaunchedEffect` 는 Composable이 컴포지션 될 떄 `suspend fun` 을 실행합니다.  
`key1` 의 값이 바뀔 때 마다 suspend fun 을 취소하고 재실행하여 매번 리컴포지션 될 떄 마다 LaunchedEffect가 재실행되는 비효율적인 문제를 해결합니다.  
<br>

`key` 는 1개 뿐만 아닌 여러 개를 지정할 수 있습니다.
```kotlin
@Composable
fun LaunchedEffect(
    vararg keys: Any?,
    block: suspend CoroutineScope.() -> Unit
)
```

<br>

만약 LaunchedEffect를 한 번만 실행하고 싶다면 `key` 값에 `true` 또는 `Unit` 을 전달해주는 방법을 사용하면 됩니다.

<br>
<br>
<br>

# DisposableEffect
만약 Composable이 Dispose될 떄 Dispose 되어야 할 부수 효과가 있다면 `DisposableEffect` 를 사용하면 됩니다.
```kotlin
@Composable
@NonRestartableComposable
fun DisposableEffect(
    key1: Any?,
    effect: DisposableEffectScope.() -> DisposableEffectResult
)
```
`key1` 은 LaunchedEffect와 같은 파라미터인 DisposableEffect가 재실행되는 키 값이고, `effect` 람다식은 `DisposableEffectResult` 를 반환하는 식입니다.  
<br>

```kotlin
DisposableEffect(key){
    // 키 값이 변경될 경우 재실행 되는 코드
    onDispose{
        // Composable 이 Dipose 될 때 Dispose 되어야 하는 효과 제거
    }
}
```
`onDispose` 은 `DisposableEffectReuslt` 를 반환하기 때문에 람다식에서 마지막에 작성되어야 합니다.  

```kotlin
inline fun onDispose(
    crossinline onDisposeEffect: () -> Unit
): DisposableEffectResult = object: DisposableEffectResult {
    override fun dispose() {
        onDisposeEffect()
    }
}
```  
<br>

아래 코드는 Lifecycle의 상태에 따른 메세지 출력 코드입니다.
```kotlin
setContent{
    TestScreen(
        onStartLogging = { println("Logging Started") },
        onStopLogging =  { println("Logging Stopped") }
    )
}


@Composable
fun TestScreen(
    lifecycleOwner: LifecycleOwner = LocalLifecycleOwner.current,
    onStartLogging: () -> Unit,
    onStopLogging: () -> Unit
){
    val startLogging by rememberUpdatedState(onStartLogging)
    val stopLogging by rememberUpdatedState(onStopLogging)

    DisposableEffect(lifecycleOwner){
        val observer = LifecycleEventObserver { _, event ->
            if(event == Lifecycle.Event.ON_START) startLogging()
            else if (event == Lifecycle.Event.ON_STOP) stopLogging()
        }

        lifecycleOwner.lifecycle.addObserver(observer)
        println("Observer Attached")

        onDispose{
            lifecycleOwner.lifecycle.removeObserver(observer)
            println("Observer Removed")
        }
    }
}
```

Composable이 Composition 될 경우 `Observer Attached`, `Logging Started` 문자열이 출력되며, Lifecycle의 상태에 따라 `Logging Started`, `Logging Stopped`가 출력되고, Composable이 Dispose 될 경우 `Logging Stopped`, `Observer Removed` 문자열이 출력됩니다.
<br>

이벤트 옵저버를 생성할 때 파라미터인 `onStartLogging` 과 `onStopLogging` 람다식을 안쓰고 `rememberUpdatedState` 로 값을 저장해 사용한 이유는 다음과 같습니다.  

> `LaunchedEffect` 블럭 내부에서 접근을 해야 하나, 변경 시 `LaunchedEffect` 가 재시작되지 않도록 하는 경우

무거운 작업을 수행하거나 `LaunchedEffect` 의 `key` 값을 true 및 Unit을 주었을 때 `LaunchedEffect` 가 재시작 되지 않고 외부 Composable 에서 바뀐 상태를 업데이트하기 위해서 사용합니다.  
<br>

`rememberUpdatedState` 는 `remember { mutableStateOf() }` 의 사용과 크게 다르지 않습니다.
```kotlin
var state by remember { mutableStateOf(value) }

@Composable
fun <T> rememberUpdatedState(newValue: T): State<T> = remember {
    mutableStateOf(newValue)
}.apply { value = newValue }
```
기존에 쓰던 코드에 `apply` 스코프 함수를 사용해 직접적으로 상태의 값을 설정해주는 차이 뿐입니다.  

값을 직접 넣어줌으로 외부 Composable에서 값이 변경되면 수정된 값으로 업데이트하게 됩니다.