# ViewModel
앱 실행 중 OS 설정에 변동이 생기면 액티비티가 파괴(onDestory)되고 새로운 액티비티가 재생성(onRestart)되는데 이 과정에서 데이터들이 초기화가 됩니다.  
`onSaveInstanceState` 와 `onRestoreInstanceState` 를 사용해 소량의 데이터를 `Bundle` 객체를 통해 시스템 메모리에 저장할 수 있지만 직렬화/역직렬화 비용을 발생 시키며 소량의 데이터라는 제한 때문에 매번 사용할 수는 없습니다.  
<br>
만약 Activity가 살아있는 한 계속 유지되는 데이터가 있으면 굳이 메모리에 저장 후 복원할 필요는 없을 것 같습니다.  
이 문제점을 해결하기 위해 인스턴스의 수명이 액티비티의 생명주기 보다 더 긴 `ViewModel` 이 만들어지게 되었습니다.
<br>
<br>
<br>

# ViewModel 생명 주기
ViewModel의 생명주기는 `ViewModelProvider` 로 전달 받은 액티비티의 생명주기를 따릅니다. 
```kotlin
val viewModel: MainViewModel = ViewModelProvider(this).get(MainViewModel::class.java)
```
<p align="center">
	<img src="https://developer.android.com/static/images/topic/libraries/architecture/viewmodel-lifecycle.png?hl=ko">
</p>
<p align="center">
	ViewModel 생명주기 [출처 : developer.android.com]
</p>

시스템에서 액티비티를 처음 생성(onCreate)될 때 ViewModel을 요청합니다. 이후 액티비티의 생명이 끝나기 전까지 ViewModel은 계속 존재합니다.
<br>
<br>
<br>

# ViewModel 구현
ViewModel은 상황에 따라 구현할 수 있는 방법이 나뉘어집니다.
- 생성자에 파라미터가 없는 경우
- 생성자에 파라미터가 있는 경우
<br>

## 파라미터가 없는 경우
```kotlin
class PersonViewModel(): ViewModel(){
	var name: String = "Kim"

	fun printName() = println(name)
}

//ViewModelProvider
val personViewModel by lazy{
	ViewModelProvider(this).get(PersonViewModel::class.java)
}

//Android KTX
val personViewModel by viewModels<PersonViewModel>()
```
파라미터가 없는 경우는 ViewModelProvider의 인자에 액티비티 생명주기를 전달하고, 반환할 ViewModel 클래스를 가져옵니다.  
<br>

`Android KTX` 는 Android Jetpack 과 Android 라이브러리에 포함된 코틀린 확장 프로그램 세트입니다. 자세한 내용은 [Android KTX](https://developer.android.com/kotlin/ktx) 를 참고해주세요.  
<br>

## 파라미터가 있는 경우
그렇다면 파라미터가 있으면 어떻게 구현해야 할까요? 파라미터가 없는 경우에 선언한 ViewModel 들을 봐도 인자를 전달할 수 있는 부분이 없어 보입니다.  
인자 값을 전달하기 위해선 `ViewModelProvider.Factory` 를 사용해야합니다.
```kotlin
class PersonViewModel(val String: name): ViewModel(){
	fun printName() = println(name)
}

//Factory 클래스 생성
class PersonViewModelFactory(val String: name): ViewModelProvider.Factory{
	override <T: ViewModel> create(modelClass: Class<T>): T{
		if(modelClass.isAssignableFrom(PersonViewModel::class.java){
			return PersonViewModel(name) as T
		}
		throw IllegalArgumentException("Not found ViewModel class.")
	}
}

//ViewModelProvider
val personViewModel by lazy {
	ViewModelProvider(this, PersonViewModelFactory("Kim"))
			.get(PersonViewModel::class.java)
}

//Android KTX
val personViewModel by viewModels<PersonViewModel>(){
	PersonViewModelFactory("Kim")
}
```
> `Inheritance from an interface with '@JvmDefault' members is only allowed with -Xjvm-default option` 에러가 날 경우 아래 코드를 `build.gradle` 에 추가해주세요.
```c
kotlinOptions {  
	freeCompilerArgs = ['-Xjvm-default=enable']  
}
```
<br>
<br>
<br>

# JetPack Compose ViewModel

`Jetpack Compose` 에서는 `viewModel()` 함수를 호출하여 컴포저블에서 ViewModel을 사용할 수 있습니다.

> 생명 주기와 범위 지정으로 인해 화면 수준의 컴포저블에서 ViewModel 인스턴스를 액세스하고 호출해야 합니다.  
> 또한 ViewModel 인스턴스는 다른 컴포저블의 인자로 전달하면 안됩니다.

`viewModel()` 함수를 사용하기 위해선 아래의 종속 항목을 추가하면 됩니다.
> androidx.lifecycle:lifecycle-viewmodel-compose:2.4.1:$lifecycle_version