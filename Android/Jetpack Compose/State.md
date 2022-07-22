# Jetpack Compose State
앱의 상태는 시간이 지남에 따라 값이 변할 수 있습니다. 상태의 범위는 매우 광범위하며 합니다.
- 메모장에 작성 된 여러 메모들
- 사용자가 텍스트 필드에 작성 중인 텍스트
- 사용자가 앱에서 스크롤할 때 스크롤의 위치

Jeckpack Compose를 앱에서 상태를 저장하고 사용하는 위치와 방법을 명시적으로 나타낼 수 있습니다.

<br>
<br>
<br>

# 상태 및 구성
컴포즈는 XML 기반 뷰와 다르게 자동으로 업데이트 하지 않고 상태가 변화할 때 마다 업데이트를 하는데 이를 `리컴포지션` 이라고 합니다. 
```kotlin
@Composable
fun HelloContent(){
	Column(
		modifier = Modifier.padding(16.dp)
	){
		Text(
			text = "Hello, ",
			modifier = Modifier.padding(bottom = 8.dp),
			style = MaterialTheme.typography.h5
		)
		TextField(
			value = "",
			onValueChange = { },
			label = { Text("Name") }
		)
	}
}
```
<p align="center">
	<img src="https://user-images.githubusercontent.com/103296212/179394862-445b4801-7a84-4a6a-a535-0220b9bbc575.gif">
<p/>

코드를 실행하고 입력을 했지만 아무런 반응이 없습니다. `TextField` 가 자체적으로 업데이트를 하지 않고 `value` 매개변수가 변해야 업데이트를 하기 때문입니다. 이를 해결하기 위해 상태를 변경 해주어야 합니다.

<br>
<br>
<br>

# 컴포저블의 상태
구성 가능한 함수는 `remember` API를 사용해 메모리에 객체를 저장할 수 있습니다.  
`remember` 에 계산된 값은 초기 컴포지션 중에 컴포지션에 저장되고 저장된 값은 리컴포지션 중에 반환됩니다.
<br>

```kotlin
interface State<out T>{
	val value: T
}
```
컴포즈에서 상태는 하나의 값을 저장할 수 있는 인터페이스로 작성되어 있습니다. 이 상태 값을 변경하기 위해선 `State` 를 구현한 `MutableState` 타입의 객체를 생성합니다.  
`mutableStateOf` 함수는 관찰 가능한 `MutableState` 를 생성하고, 이 상태는 런타임 시 Compose에 통합되는 관찰 가능 유형입니다.
```kotlin
interface MutableState<T> : State<T>{
	override var value: T
	operator fun component1(): T
	operator fun component2(): (T) -> Unit
}
```
`MutableState` 는 구조 분해 선언을 사용합니다. `component1` 은 State의 값을 반환하는 함수고, `component2` 는  상태를 변경하는 함수입니다.  
`value` 매개변수가 변경되면 `value` 의 값을 읽는 구성 가능한 함수의 리컴포지션이 예약됩니다.
<br>

`MutableState` 타입의 객체를 생성하는 방법은 세 가지가 있습니다.
- val value = remember { mutableStateOf("") }
- var value by remember { mutableStateOf("") }
- val (value, setValue) = remember { mutableStateOf("") }

세 가지 선언 모두 동일한 선언이며 용도에 따라 선언 방법을 선택해야 합니다.  
두 번째 방법의`by` 위임을 이용한 선언은 다음 함수를 가져와야 합니다.
```kotlin
import androidx.compose.runtime.getValue
import androidx.compose.runtime.setValue
```
<br>

이제 상태 생성 선언 방법도 알았으니 `TextField` 를 리컴포지션 하기 위해 상태를 매개변수 값에 넣어보겠습니다.

```kotlin
@Composable
fun HelloContent(){
	var text by remember { mutableStateOf("") }

	Column(
		modifier = Modifier.padding(16.dp)
	){
		Text(
			text = "Hello, $text",
			modifier = Modifier.padding(bottom = 8.dp),
			style = MaterialTheme.typography.h5
		)
		TextField(
			value = text,
			onValueChange = { text = it },
			label = { Text("Name") }
		)
	}
}
```
<p align="center">
	<img src="https://user-images.githubusercontent.com/103296212/179396213-f18d6b9c-9eb3-4ab7-a477-400e51d29395.gif">
<p/>

`remember` 가 리컴포지션 될 때 상태는 유지 해주지만 아래와 같은 구성이 변경 될 때는 상태가 유지되지 않습니다.
- 화면이 가로 모드 혹은 세로 모드로 전환 됐을 때
- OS 사용자 언어를 변경 했을 때
- 멀티 윈도우 환경으로 전환 했을 때
- etc.

`rememberSaveable` 을 사용하면 `Bundle` 에 저장할 수 있는 모든 값을 자동으로 저장하기 때문에 화면 구성이 변경되어도 상태가 유지됩니다.
```kotlin
var state by rememberSaveable { mutableStateOf() }
```

<br>
<br>
<br>

# 상태를 저장하는 또 다른 방법
`rememberSaveable` 로 상태를 저장하지만 만약 `Bundle` 에 저장할 수 있는 타입이 아닌 경우도 있습니다. 이 문제를 해결하기 위한 몇 가지 방법이 있습니다.  
<br>

### Parcelize
`@Parcelize` 주석을 추가하면 Parcelable이 되어 `Bundle` 에 저장할 수 있게 됩니다.
```kotlin
@Parcelize
data class Person(val name: String, val age: Int): Parcelable

@Composable
fun PersonDetail(){
	var selectedPerson by rememberSaveable {
		mutableStateOf(Person("Kim", 21))
	}
}
```
<br>

### MapSaver
`@Parcelize` 가 적합하지 않을 경우 `MapSaver` 를 이용해  시스템이 `Bundle` 에 저장될 수 있는 Map 형태로 객체를 변환할 수 있습니다.
```kotlin
data class Person(val name: String, val age: Int)

val personSaver = run {
	val nameKey = "Name"
	val ageKey = "Age"
	mapSaver(
		save = { mapOf(nameKey to it.name, ageKey to it.age) },
		restore = { Person(it[nameKey] as String, it[ageKey] as Int)
	)
}

@Composable
fun PersonDetail(){
	val selectedPerson = rememberSaveable(stateSaver = personSaver){
		mutableStateOf(Person("Kim", 21))
	}
}	
```
<br>

### ListSaver
`listSaver` 를 사용하고 인덱스를 키로 사용하여 Map 대신 List로 정의할 수 있습니다.
```kotlin
data class Person(val name: String, val age: Int)

val personSaver = listSaver<Person, Any>(
	save = { listOf(it.name, it.age) },
	restore = { Person(it[0] as String, it[1] as Int) }
)

@Composable
fun PersonDetail(){
	val selectedPerson = rememberSaveable(stateSaver = personSaver) {
		mutableStateOf(Person("Kim", 21))
	}
}
```

<br>
<br>
<br>

# 상태 호이스팅
### Stateful 과 Stateless
이때까지 만든 코드들은 컴포저블 내부에서 상태를 저장하고 변경하므로 Stateful 컴포저블입니다. Stateful 컴포저블은 호출자가 상태를 제어할 필요가 없고 상태를 직접 관리하지 않아도 상태를 사용할 수 있는 경우에 유용합니다. 하지만 재사용하기 어렵고 테스트 하기 어렵다는 단점이 있습니다.  
<br>
Stateless 컴포저블은 Stateful과 반대로 상태를 가지지 않는 컴포저블입니다. 외부에서 상태의 값을 가져오기 때문에 Jetpack Compose의 장점인 컴포저블 재사용이 수월합니다.  
<br>
Stateful 컴포저블의 상태를 Stateless 컴포저블로 끌어올리기 위해서는 `상태 호이스팅` 을 사용하는 방법으로 해결할 수 있습니다.  
<br>
<br>
<br>
`상태 호이스팅` 은 컴포저블을 Stateless로 만들기 위해 상태를 컴포저블의 호출자로 옮기는 패턴입니다. 상태를 끌어올리기 위해선 상태 변수를 두 매개변수로 바꾸는 것입니다.
- `value: T` 표시할 상태의 값
- `onValueChange: (T) -> Unit` T 값이 새 값일 경우 변경하도록 요청하는 이벤트

이제 Stateful 컴포저블을 Stateless 컴포저블로 변환 해보겠습니다.
```kotlin
@Composable
fun HelloScreen(){
	var text by remember { mutableStateOf("") }
	
	HelloContent(value = text, onValueChange = { text = it ))
}

@Composable
fun HelloContent(
	value: String,
	onValueChange: (String) -> Unit
){
	Column(
		modifier = Modifier.padding(16.dp)
	){
		Text(
			text = "Hello, $value",
			modifier = Modifier.padding(bottom = 8.dp),
			style = MaterialTheme.typography.h5
		)
		TextField(
			value = value,
			onValueChange = onValueChange,
			label = { Text("Name") }
		)
	}
}
```
`HelloContent` 에서 상태를 끌어올림으로써 더 쉽게 컴포저블을 추론할 수 있고, `HelloScreen` 뿐만 아니라 다른 컴포저블에서도 재사용 할 수 있습니다.  
또한 `HelloScreen` 을 수정하거나 교체해도 `HelloContent` 는 변경할 필요가 없어집니다.