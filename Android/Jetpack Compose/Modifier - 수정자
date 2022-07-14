# Modifier 수정자
Modifier(수정자)는 컴포저블을 장식하거나 행동을 추가할 수 있는 수단 입니다.
쉽게 말하면 UI의 디자인을 수정하거나 클릭과 같은 기능을 추가할 수 있습니다.
> - 컴포저블의 크기, 동작 및 모양 변경
> - 사용자 입력 처리
> - 클릭, 드래그, 스크롤 등과 같은 상호작용 추가

수정자는 Modifier 확장 함수를 호출하여 사용할 수 있습니다
```kotlin
Text(
	...,
	modifier = Modifier
		.padding(16.dp)
		.wrapContentSize(Alignment.Center),
)
```



## 수정자의 순서
수정자는 순서에 따라 컴포저블이 반영하는 최종 결과에 영향을 줍니다.
Jetpack Compose 문서의 [수정자 순서 예시](https://developer.android.com/jetpack/compose/modifiers#order-modifier-matters) 를 보면 알 수 있듯이 Padding과 Clickable의 위치가 어디에 있냐에 따라 전체 넓이가 클릭 될 수 있고 Padding이 적용된 넓이가 클릭이 될 수 있습니다.



## 수정자 확장 함수
개인적으로 작업할 때 많이 쓰이는 수정자를 정리 했으며 나중에 점점 사용하면서 추가해 나갈 예정입니다.
설명이 안되어 있는 매개변수는 아직 공부가 덜 된 내용입니다.
<br>
<br>
### 작업
#### Clickable
```kotlin
Modifier.clickable(
	enabled: Boolean = true,
	onClickLabel: String? = null,
	role: Role? = null,
	onClick: (() -> Unit)?
)
```
컴포저블에 클릭 이벤트를 추가할 수 있습니다
- enabled : 클릭 상호작용을 비활성화/활성화 할 수 있습니다
- onClickLabel : 해당 컴포저블에 클릭 이벤트를 넣은 의도를 작성합니다
- onClick : 클릭 할 시 호출되는 콜백 함수 입니다
```kotlin
//Example
@Composable
fun ClickableBox(){
	var clicked by remember { mutableStateOf(false) }

	Box(
		modifier = Modifier
			.size(100.dp)
			.background(if (clicked) Color.Red else Color.Blue)
			.clickable { clicked = !clicked },
	){}
}
```
![Clickable Box](https://user-images.githubusercontent.com/103296212/179088522-349d068f-2823-45d6-86db-598f9e6308ed.gif)
<br>
#### combinedClickable
```kotlin
@ExperimentalFoundationApi
Modifier.combinedClickable(
	enabled: Boolean = true,
	onClickLabel: String? = null,
	role: Role? = null,
	onLongClickLabel: String? = null,
	onLongClick: (() -> Unit)? = null,
	onDoubleClick: (() -> Unit)? = null,
	onClick: (() -> Unit)?
)
```
컴포저블에 다양한 클릭 이벤트를 추가할 수 있습니다.
실험용 API 이기 때문에 수정되거나 사라질 수 있습니다
```kotlin
//Example
@OptIn(ExperimentalFoundationApi::class)  
@Composable  
fun TextBox(){
	var text by remember{ mutableStateOf("Default") }

	Box(modifier = Modifier.fillMaxSize()){
		Box(
			modifier = Modifier
				.size(20.dp)
				.align(Alignment.Center)
				.border(width = 3.dp, color = Color.Black)
				.combinedClickable(
					onLongClick = { text = "Long Click" },
					onDoubleClick = { text = "Double Click" },
					onClick = { text = "Click" }
				)
		){
			Text(text = text, modifier = Modifier.align(Alignment.Center))
		}
	}
}
```
![combinedClickable](https://user-images.githubusercontent.com/103296212/179090357-810347a9-410c-4212-9f3f-b3b62a1f1d45.gif)
