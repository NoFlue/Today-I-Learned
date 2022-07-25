# Canvas
Jetpack Compose의 Canvas를 이용하면 커스텀 그래픽을 기존보다 쉽게 그릴 수 있습니다. 또한 Compose는 선언형 UI 접근 방식이기에 이러한 이점들이 있습니다.
- 그래픽 요소의 상태를 최소화하므로 상태 프로그래밍의 실수를 피할 수 있습니다.
- 항목을 그릴 때 모든 옵션이 예상되는 위치에 있습니다.
- 뷰 기반의 UI보다 효율적인 방법으로 객체를 생성하고 해제합니다.

<br>

커스텀 그래픽을 그리기 위해선 다른 컴포저블과 마찬가지로 `Canvas` 컴포저블을 레이아웃에 배치하면 됩니다.
```kotlin
Canvas(modifier = Modifier.fillMaxSize()){
	/* DrawScope */
}
```
Canvas는 그래픽을 그릴 수 있는 `DrawScope` 의 범위를 가지고 있으며, `size` 필드를 통해 Canvas의 크기를 가져올 수 있습니다.  
<br>
`drawLine` 를 사용해 대각선을 그려보겠습니다.
```kotlin
Canvas(modifier = Modifier.fillMaxSize()){
	drawLine(
		start = Offset(x = 0f, y = 0f),
		end = Offset(x = size.width, y = size.height),
		color = Color(0xff000000)
	)
}
```
<p align="center">
	<img src="https://user-images.githubusercontent.com/103296212/180814192-7aee02fd-1e5b-49cc-bdf3-2a40ae0b7b00.png">
</p>

<br>

Canvas를 이용해 그래픽을 그리기 위해선 위치를 지정해주어야 합니다. 
<p align="center">
	<img src="https://miro.medium.com/max/1400/1*zNbMO65FggNq-EUXyqFadg.jpeg">
</p>
<p align="center">
	출처 : https://medium.com/@droidvikas
</p>

왼쪽에서 오른쪽으로 x축, 위에서 아래쪽으로 y축이 형성되어 있습니다. 예로 들면 위 좌표평면에 있는 검은색 원의 중심은 약 ( 200, 200 ) 좌표에 있습니다.  
<br>
`drawLine` 와 같이 선으로 나타내는 함수는 시작점과 끝점을 지정해주기 위해 `start` 와 `end` 매개변수의 값을 전달해 나타낼 수 있습니다.  
`drawRect`, `drawArc` 와 같은 면으로 나타내는 함수는 왼쪽 상단을 기준으로 좌표를 설정하는 `topLeft` 매개변수의 값을 전달해 위치를 지정합니다.
```kotlin
//drawScope
drawArc(  
    size = Size(300f, 300f),  
    color = Color(0xff1776d1),  
    startAngle = 270f,  
    sweepAngle = 270f,  
    useCenter = false,  
    style = Stroke(  
	    cap = StrokeCap.Round,  
	    width = 75f  
    ),  
    topLeft = Offset(x = size.width - 400f, 150f),  
)  
drawRect(  
    color = Color(0xff47c832),  
    size = Size(200f, 200f),  
    topLeft = Offset(center.x - 100f, center.y - 100f)  
)
```
<p align="center">
	<img width="330" src="https://user-images.githubusercontent.com/103296212/180868646-00664f91-55a0-4365-91b8-d5ec57bcad58.png">
</p>

<br>
<br>
<br>

# DrawScope
`DrawScope` 의 확장 함수를 이용해 도형의 위치를 옮기거나 회전하는 등 여러 작업을 할 수 있습니다.  
<br>

### DrawScope.inset()
```kotlin
inset(300f, 500f){
	drawRect(
		color = Color(0xff000000),
		size = Size(300f, 300f),
	)
}
```
<p align="center">
	<img width="324" src="https://user-images.githubusercontent.com/103296212/180872321-0cbb369d-b1c4-424a-9d55-b9d310ee1f2d.png">
</p>

`inset()` 을 사용하여 해당 그래픽의 범위의 기본 매개변수를 조정하고, 경계를 변경한다는 역할을 한다고 적혀있는데 사용해본 결과 `Padding` 과 비슷한 역할을 하는 것 같습니다.
> 여러 번 써보고 이 내용은 나중에 다시 고치자

<br>

### DrawScope.rotate()
```kotlin
rotate(45f){
	drawRect(
		color = Color(0xff000000),
		size = Size(300f, 300f),
	)
}
```
<p align="center">
	<img width="214" alt="스크린샷 2022-07-26 오전 5 57 04" src="https://user-images.githubusercontent.com/103296212/180872732-924c7e71-d3ee-435e-b9ad-8d93b32f528f.png">
</p>

`rotate()` 를 사용하여 그래픽을 회전할 수 있습니다.

<br>

### DrawScope.translate()
```kotlin
translate(50f, 100f){
	drawRect(
		color = Color(0xff000000),
		size = Size(300f, 300f),
		topLeft = center
	)
}
```
<p align="center">
	<img width="220" src="https://user-images.githubusercontent.com/103296212/180873285-e7e31599-9b04-41c0-a497-d070f2b9681b.png">
</p>

`translate()` 를 사용하여 그래픽의 위치를 옮길 수 있습니다.  

<br>

### DrawScope.withTransform()
```kotlin
withTransform({
	translate(50f, 100f)
	rotate(45f)
}){
	drawRect(
		color = Color(0xff000000),
		size = Size(300f, 300f),
		topLeft = center
	)
}
```
<p align="center">
	<img width="248" src="https://user-images.githubusercontent.com/103296212/180873896-f665e01b-8361-4e81-b67f-6a13d6ab70d3.png">
</p>

`withTransform()` 을 사용해 한 가지 이상의 변환 함수를 같이 사용할 수 있습니다.  
<br>
> 이 뿐만 아니라 다양한 [DrawScope 확장 함수](https://developer.android.com/reference/kotlin/androidx/compose/ui/graphics/drawscope/DrawScope?hl=ko)들이 존재합니다.