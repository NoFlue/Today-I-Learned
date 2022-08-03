# Flow
`Flow` 는 데이터 스트림이며, 코루틴에서 리액티브 프로그래밍을 지원하기 위한 구성요소 입니다.  
<br>

<p align="center">
	<img src="https://developer.android.com/static/images/kotlin/flow/flow-entities.png?hl=ko">
</p>

`Flow` 는 세 가지 구성요소로 이루어져 있습니다.
- Producer(생성자)
- Intermediary(중간 연산자)
- Consumer(소비자)

<br>
<br>

## Producer
생성자는 데이터를 발행하는 역할을 하며, Flow를 만들기 위해서는 `Flow Builder API` 를 사용합니다.  `Flow` 는 `flow { }` 빌더 함수 블록 내부에서 `emit()` 함수를 사용해 데이터를 생성합니다.  
<br>

```kotlin
class DummyRemoteDataSource(
	private val dummyApi: DummyApi,
	private val refreshIntervalMs: Long = 5000
){
	val lastestData: Flow<List<Dummy>> = flow{
		while(true){
			val dummyDataList = dummyApi.fetchLastestDummy()
			emit(dummyDataList)
			delay(refreshIntervalMs)
		}
	}
}

interface DummyApi{
	suspend fun fetchLastestDummy(): List<Dummy>
}
```

<br>
<br>

## Intermediary
중간 연사자는 생성자가 생성한 데이터 스트림을 수정할 수 있습니다. 예로 들면 여러 데이터 목록들 중 원하는 데이터만 필터링해서 가져올 수 있습니다.  
<br>

대표적인 중간 연산자는 `map` , `filter` , `take` 등이 있습니다.
```kotlin
class DummyRepository(
	private val dummyRemoteDataSource: DummyRemoteDataSource
){
	fun getDummyDataWithCondition(condition: (Dummy) -> Boolean) =
		dummyRemoteDataSource.lastestData
			.map { dummy -> dummy.filter { condition(this) } }
}
``` 

<br>
<br>

## Consumer
소비자는 데이터 스트림의 값을 가져오기 위해 `collect` 를 사용합니다. `collect` 는 정지 함수이므로 코루틴 내에서 실행 되어야 합니다.
```kotlin
class DummyViewModel(
	private val dummyRepository: DummyRepository
): ViewModel(){
	fun getDummyDataList(condition: (Dummy) -> Boolean){
		viewModelScope.launch{
			dummyRepository.getDummyDataWithCondition(condition).collect { dummy ->
				/* ... */
			}
		}
	}
}
```
생산자는 `while(true` 루프로 항상 일정한 간격으로 소비자에게 데이터를 전달하며, ViewModel이 삭제되어 ViewModelScope가 취소되기 전까지 이 작업을 반복합니다.