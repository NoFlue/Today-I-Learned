# LiveData
`LiveDate` 는 관찰 가능한 데이터 홀더 클래스 이며, 생명 주기를 인식합니다. 액티비티, 프래그먼트, 서비스 등 생명 주기의 영향을 받는 데이터 클래스 홀더입니다. 수명 주기 인식을 통해 `LiveData` 는 활동 수명 주기 상태에 있는 앱 구성요소 관찰자만 업데이트합니다.  
<br>
LiveData는 `Observer` 패턴으로 데이터의 값이 변할 때 마다 그 값을 처리합니다. 생명 주기의 영향을 받기 때문에 Observer 클래스로 표현되는 관찰자가 `STARTED` 혹은 `RESUMED` 상태이면 LiveData는 관찰자에게 데이터 업데이트를 요청합니다.  
<br>
<p align="center">
	<img src="https://miro.medium.com/max/1342/1*kjSdZieZlUhixRTjX0bMew.png">
</p>

관찰자가 `STARTED` 혹은 `RESUMED` 상태일 경우 데이터가 변경 되었을 때 옵저버 콜백이 실행되지만 `STOPED`  일 경우 변경된 값을 업데이트 하지 않으며 다시 `RESUMED` 상태가 되었을 때 최근에 변경된 데이터를 업데이트합니다. 

<br>
<br>
<br>

# LiveData 사용의 이점
- UI와 데이터의 상태 일치 보장
	LiveData는 옵저버 패턴을 따르기 때문에 데이터가 변경될 때 `Observer` 객체에 알립니다. 코드를 통합하여 이러한 `Observer` 객체에 UI를 업데이터 하면 앱 데이터가 변경될 때 마다 관찰자가 대신 UI를 업데이트 하므로 개발자가 업데이트할 필요가 없습니다.

- 메모리 누수 없음
	관찰자는 생명주기가 파괴(onDestroy)되면 자동으로 삭제됩니다.

- 중지된 활동으로 인한 비정상 종료 없음
	관찰자의 생명 주기가 비활성 상태에 있으면 관찰자는 어떤 LiveData 이벤트도 받지 않습니다.

- 수명 주기를 더 이상 수동으로 처리하지 않음
	UI 구성요소는 관련 데이터를 관찰하기만 하며 관찰을 중지하거나 다시 시작하지 않습니다. LiveData는 관찰하는 동안 관련 수명 주기 상태의 변경을 인식하므로 자동으로 관리합니다.

- 최신 데이터 유지
	수명 주기가 비활성화되면 다시 활성화될 때 최신 데이터를 받습니다.

- 적절한 구성 변경
	기기 회전과 같은 구성 변경으로 인해 액티비티 또는 프래그먼트가 다시 생성되면 사용 가능한 최신 데이터를 즉시 받게 됩니다.

- 리소스 공유
	앱에서 시스템 서비스를 공유할 수 있도록 싱글톤 패턴을 사용하는 LiveData 객체를 확장하여 시스템 서비스를 래핑할 수 있습니다. LiveData 객체가 시스템 서비스에 한 번 연결되면 리소스가 필요한 모든 관찰자가 LiveData 객체를 볼 수 있습니다.

<br>
<br>
<br>

# LiveData 생성
LiveData는 Collections를 구현한 List와 같은 객체를 비롯하여 **모든 데이터와 함께 사용할 수 있는 래퍼**입니다.  
LiveData는 일반적으로 ViewModel 객체 내에 저장됩니다.
```kotlin
class MyViewModel: ViewModel() {
	private val _name = MutableLiveData<String>()
	
	val name = LiveData<String>()
		get() = _name
}
```
LiveData는 불변 타입이므로 LiveData를 상속한 `MutableLiveData` 타입에서 값을 변경할 수 있으며, 값을 변경하기 위해선 `setValue()` , `postValue()` 메서드를 사용해야 합니다.
- `setValue(T)` : `Main Thread` 에서 LiveData를 갱신합니다.
- `postValue(T)` : `Background Thread` 에서 LiveData를 갱신합니다.

<br>
<br>
<br>

# LiveData 관찰
LiveData를 생성했으니 관찰자를 통해 데이터가 변경될 때 마다 UI를 업데이트 해보겠습니다.
```kotlin
class MainActivity: AppCompatActivity(){
	private val myViewModel by viewModels<MyViewModel>()

	override fun onCreate(saveInstanceState: Bundle?){
		super.onCreate(saveInstanceState)

		val nameObserver = Observer<String> { newName ->
			nameTextView.text = newName
		}

		myViewModel.name.observe(this, nameObserver)
	}
}
```
대부분의 앱에서 `onCreate()` 메서드는 LiveData를 관찰하기 적합한 위치입니다.
- `onResume()` 메서드에서 중복 호출을 피하기 위함
- 액티비티나 프래그먼트가 활성 상태가 되는 즉시 데이터를 포함되도록 하기 위함  
<br>

LiveData는 `observe()` 메서드를 호출해 값이 변경될 경우 업데이트를 처리합니다.
```kotlin
@MainThread
fun observe(owner: LifecycleOwner, observer: Observer<Any!>)
```
`observer()` 메서드가 호출될 경우 `Observer` 의 `onChanged()` 메서드가 즉각 호출되어 LiveData에 저장된 최신 값을 제공합니다.  
`setValue()` 와 `postValue()` 또한 호출될 경우 `onChanged()` 메서드가 호출되어 값이 변경됩니다.

<br>
<br>
<br>

# Jetpack Compose LiveData
Jetpack Compose에서 LiveData를 `State<T>` 로 변환하여 사용할 수 있습니다.
```kotlin
class MyViewModel: ViewModel() {
	private val _name = MutableLiveData<String>()
	
	val name = LiveData<String>()
		get() = _name
}


val myViewModel by viewModels<MyViewModel>()
val myName by myViewModel.name.observeAsState()

Text("Name : $myName"}
```
```kotlin
@Composable
fun <T: Any?> LiveData<T>.observeAsState(): State<T?>
```
`observeAsState()` 메서드를 사용해 `State<T>` 타입으로 변환할 수 있으며 LiveData와 마찬가지로 값이 변경될 때 관찰자가 업데이트를 처리합니다.
