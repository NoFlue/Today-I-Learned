# Modifier 수정자
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
<br>
<br>
<br>
<br>
# 수정자 확장 함수
작업하면서 수월하게 사용할 수 있는 수정자를 위주로 정리했으며 나중에 점점 사용하면서 추가해 나갈 예정입니다.  
설명이 안되어 있는 매개변수는 아직 공부가 덜 된 내용입니다. 다만 비슷한 수정자 확장 함수에서 설명한 매개변수 또는 직관적인 이름을 가진 매개변수의 설명은 생략합니다.
<br>
<br>
<br>
## 작업
### Clickable
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
<br>
<br>
### combinedClickable
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
실험용 API 이기 때문에 수정되거나 사라질 수 있습니다.
```kotlin
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
<br>
<br>
<br>
## 테두리
### border
```kotlin
Modifier.border(
	border: BorderStroke,
	shape: Shape = RectangleShape
)

Modifier.border(
	width: Dp
	color: Color,
	shape: Shape = RectangleShape
)

Modifier.border(
	width: Dp,
	brush: Brush,
	shape: Shape = RectangleShape
)
```
컴포저블에 테두리를 추가할 수 있습니다.
- border : BorderStoke 객체를 이용해 테두리의 너비와 색을 지정합니다.
- shape : 테두리의 모양을 지정합니다.
- brush : Brush 객체를 이용해 테두리의 색을 지정합니다.
```kotlin
//border, shape
Box(  
    modifier = Modifier  
        .size(100.dp)  
        .background(Color.White)  
        .border(border = BorderStroke(2.dp, Color.Black), shape = CutCornerShape(16.dp))  
) 

// width, color, shape
Box(  
    modifier = Modifier  
        .size(100.dp)  
        .background(Color.White)  
        .border(width = 2.dp, color = Color.)  
)

// width, brush, shape
val gradientBrush = Brush.horizontalGradient(  
    colors = listOf(Color(0xff937DC2), Color(0xffC689C6), Color(0xffFCC5C0)),  
	startX = 0f,  
    endX = 300f,  
    tileMode = TileMode.Repeated  
)

Box(  
    modifier = Modifier  
        .size(100.dp)  
        .background(Color.White)  
        .border(width = 2.dp, brush = gradientBrush, shape = RoundedCornerShape(16.dp))  
)
```
<img width="323" alt="border" src="https://user-images.githubusercontent.com/103296212/179296406-e3cc5d01-518b-452a-bbc0-2051e1fc3624.png">

<br>
<br>
<br>

## 그리기
### background
```kotlin
Modifier.background(
	color: Color,
	shape: Shape = RectangleShape
)

Modifier.background(
	brush: Brush,
	shape: Shape = RectangleShape,
	alpha: Float = 1.0f
)
```
지정한 모양으로 배경을 그립니다.
- alpha : 배경의 투명도를 설정합니다 ( 0.0f ~ 1.0f )
```kotlin
//color, shape
Box(  
    modifier = Modifier  
        .size(100.dp)  
        .background(  
            color = Color.LightGray  
    )  
)

//brush, shape, alpha
val gradientBrush = Brush.horizontalGradient(  
    colors = listOf(Color(0xff937DC2), Color(0xffC689C6), Color(0xffFCC5C0)),  
	startX = 0f,  
    endX = 300f,  
    tileMode = TileMode.Repeated  
)

Box(  
    modifier = Modifier  
        .size(100.dp)  
        .background(  
            brush = gradientBrush,  
		    shape = RoundedCornerShape(16.dp),  
		    alpha = 0.2f  
		)  
)
```
<img width="273" alt="background" src="https://user-images.githubusercontent.com/103296212/179297916-137041f3-ce7c-4d56-b037-82fa90842b1e.png">
<br>
<br>
<br>

### alpha
```kotlin
Modifier.alpha(alpha: Float)
```
콘텐츠의 투명도를 설정합니다 ( 0.0f ~ 1.0f )
<br>
<br>
<br>
### clip
```kotlin
Modifier.clip(shape: Shape)
```
콘텐츠를 모양에 맞게 자릅니다.
<br>
<br>
<br>
### shadow
```kotlin
Modifier.shadow(
	elevation: Dp,
	shape: Shape = RectangleShape,
	clip: Boolean = elevation > 0.dp,
	ambientColor: Color = DefaultShadowColor,
	spotColor: Color = DefaultShadowColor
)
```
그림자를 그리는 `graphicsLayer` 를 그립니다.
- elevation: 그림자의 크기를 설정합니다.
```kotlin
val gradientBrush = Brush.horizontalGradient(  
    colors = listOf(Color(0xff937DC2), Color(0xffC689C6), Color(0xffFCC5C0)),  
	startX = 0f,  
    endX = 300f,  
    tileMode = TileMode.Repeated  
)

Box(  
    modifier = Modifier  
        .size(100.dp)  
        .shadow(  
            elevation = 10.dp,  
		    shape = RoundedCornerShape(16.dp),  
		)  
        .background(  
            brush = gradientBrush,  
			shape = RoundedCornerShape(16.dp),  
		)
)
```
<img width="289" alt="shadow" src="https://user-images.githubusercontent.com/103296212/179300001-de44460a-518a-4884-b8a3-c7e95fa810a9.png">

왼쪽은 `shadow` 를 `background` 보다 먼저 선언 했을 때  
오른쪽은 `shadow` 를 `background` 보다 늦게 선언 했을 때  
<br>
수정자의 선언의 순서가 최종 결과에 영향을 미친다는 예시를 여기서 볼 수 있습니다.
<br>
<br>
<br>
##  패딩
### padding
```kotlin
Modifier.padding(all: Dp)

Modifier.padding(
	start: Dp,
	top: Dp,
	end: Dp,
	bottom: Dp
)

Modifier.padding(
	horizontal: Dp,
	vertical: Dp
)

Modifier.padding(paddingValues: PaddingValues)

Modifier.absolutePadding(
	left: Dp,
	top: Dp,
	right: Dp,
	left: Dp
)
```
콘텐츠의 가장자리를 기준으로 dp 만큼 공간을 띄웁니다
```kotlin
Text(  
    "Android Jetpack Compose",  
    modifier = Modifier.padding(16.dp)  
)  

Text(  
    "Android Jetpack Compose",  
    modifier = Modifier.absolutePadding(left = 6.dp, right = 32.dp)  
)  

Text(  
    "Android Jetpack Compose",  
    modifier = Modifier.padding(horizontal = 24.dp, vertical = 8.dp)  
)
```
<img width="176" alt="padding" src="https://user-images.githubusercontent.com/103296212/179302702-e71e8fe7-0d51-4d0e-a99e-9543be7b45d9.png">

<br>
<br>
<br>

## 크기
### aspectRatio
```kotlin
Modifier.aspectRatio(
	ratio: Float,
	matchHeightConstraintsFirst: Boolean = false
)
```
콘텐츠의 가로세로 비율을 조정합니다.  
#### matchHeightConstraintsFirst
- false인 경우 : `Constraints.maxWidth` `Constraints.maxHeight` `Constraints.minWidth` `Constraints.minHeight` 의 순서로 수신되는 제약조건을 매칭하여 가로세로 비율을 조정합니다.
- true인 경우 : `Constraints.maxHeight` `Constraints.maxWidth` `Constraints.minHeight` `Constraints.minWidth` 의 순서로 수신되는 제약조건을 매칭하여 가로세로 비율을 조정합니다.
<br>
만약 size가 50dp 이고 가로세로 비율이 2 일 때  
`matchHeightConstraintsFirst = false` 일 경우 우선 순서가 `Constraints.maxWidth` 이므로 `width == 50 , height == 25`  
`matchHeightConstraintsFirst = true` 일 경우 우선 순서가 `Constraints.maxHight` 이므로 `width == 100 , height == 50`  
```kotlin
Box(  
    modifier = Modifier  
        .size(75.dp)  
        .background(color = Color.LightGray)  
        .aspectRatio(2f, matchHeightConstraintsFirst = true)  
)

Box(  
    modifier = Modifier  
        .size(75.dp)  
        .background(color = Color.LightGray)  
        .aspectRatio(2f, matchHeightConstraintsFirst = false)  
)
```
<img width="310" alt="스크린샷 2022-07-16 오전 5 29 45" src="https://user-images.githubusercontent.com/103296212/179305992-96190f4d-3863-4033-915e-082c99484685.png">

