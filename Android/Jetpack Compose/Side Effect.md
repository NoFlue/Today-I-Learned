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
