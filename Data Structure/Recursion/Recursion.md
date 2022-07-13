# 재귀 Recursion
> [윤성우의 열혈 자료구조](https://www.orentec.co.kr/booklist/DA_ST_1/book_sub1.php) 를 읽고 정리한 자료이며 Kotlin으로 코드를 작성했습니다

## 재귀함수 
재귀 함수는 함수 내에서 자기 자신을 다시 호출하는 함수입니다
```kotlin
fun Recursive(){
	println("함수 호출!")
	Recursive()
}
```
하지만 위 코드를 보면 재귀가 계속 호출되기 때문에 무한 루프에 빠진다는 문제점이 있습니다.
이를 해결하기 위해선 탈출구를 만들어 줘야 합니다. 그러기 위해 반환할 조건을 만들어 줍니다.
```kotlin
fun Recursive(num: Int){
	if(num == 0) return // num이 0 일 경우 재귀 함수 탈출
	
	println("함수 호출!")
	Recursive(num - 1) // num - 1의 값을 인자로 전달
}
```

이런 재귀 알고리즘은 알고리즘의 어려운 문제를 단순화하는데 사용 되는 자료구조 입니다.

그렇다면 재귀 함수는 어떻게 사용해야 좋은 알고리즘이 될 수 있을까?

재귀 함수를 사용하기 위해선 아래 조건을 만족해야 합니다.
>Recursive case (재귀 조건)
재귀 함수는 큰 문제를 간단하게 여러 개로 쪼개서 문제를 푸는 방법으로 활용합니다.
즉, 하위 문제에 대한 해답을 얻어 상위 문제의 해답을 얻는 방식입니다.


>Base case (종료 조건)
 재귀 함수가 무한 루프에 빠지지 않게 탈출하는 조건을 명시해야 합니다.
 재귀가 더 이상 쪼갤 수 없는 명확한 값을 제시하는 경우 종료로 판단합니다.
 
 ## 재귀 함수 활용
 ### 피보나치 수열 : Fibonacci Sequence
 피보나치 수열은 재귀적인 형태를 띠는 대표적인 수열이며 다음과 같이 전개 됩니다
 <center> 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, ...  </center>
 
 피보나치 수열을 수학적으로 표현하면
 
![](http://www.sciweavers.org/download/Tex2Img_1657708375.png)

이 조건을 그대로 코드로 옮겨보면 다음과 같은 재귀 함수가 나옵니다
```kotlin
fun Fibonacci(num: Int): Int {  
    return if (num == 1) 0  
	  else if (num == 2) 1  
	  else Fibonacci(num - 1) + Fibonacci(num - 2)  
}  
  
fun main() {  
    println(Fibonacci(11))   // 55
}
```
Index가 1, 2 일 경우 각각 0과 1을 반환 하지만 3 이상 일 경우 문제를 여러 개로 쪼개어 해답을 얻는 방식을 사용하며, 최종 답을 얻을 경우 재귀를 탈출합니다

예로 들면 수열의 5번 째 값을 얻기 위해서는 `Fibonacci(5)` 를 `Fibonacci(3) 와 Fibonacci(4)` 로 쪼개고 `Fibonacci(3)와 Fibonacci(4)` 는 각각 `Fibonacci(1) + Fibonacci(2)` 와  `Fibonacci(2) + Fibonacci(3)` 로 나눕니다.
```
Fibonacci(5) = Fibonacci(3) + Fibonacci(4) 
		= (Fibonacci(1) + Fibonacci(2)) + (Fibonacci(2) + Fibonacci(3))
		= (0 + 1) + (1 + (Fibonacci(1) + Fibonacci(2))
		= (0 + 1) + (1 + (0 + 1)) = 3
```
피보나치 수열을 통해 위에서 본 재귀 조건과 종료 조건이 어떤 과정을 거치는지 알아 볼 수 있습니다


### 이진 탐색
이진 탐색 알고리즘은 반복문을 통해 구현할 수 있지만 논리 자체는 재귀적이기 때문에 재귀함수로도 구현할 수 있습니다
```kotlin
fun BSearchRecur(arr: List<Int>, first: Int, last: Int, target: Int): Int {  
    if(first > last) return throw IllegalArgumentException("해당 숫자를 찾을 수 없습니다")  
  
    val mid = (first + last) / 2  
  
	if(arr[mid] == target) return mid  
    else if (target < arr[mid]) return BSearchRecur(arr, first, mid - 1, target)  
    else return BSearchRecur(arr, mid + 1, last, target)  
}  
  
fun main() {  
    val list = listOf(1, 3, 5, 6, 7, 10, 32)
    
    println(BSearchRecur(list, 0, list.size, 10))  
}
```
이진 탐색은 큰 문제를 작은 문제로 쪼개어 푸는 방법이 아니기 때문에 재귀 조건을 만족하지 않으므로 좋은 재귀 알고리즘이라고 할 순 없지만 반복문을 이용한 문제는 재귀로도 풀 수 있다는 점에 이진 탐색을 재귀로 풀어 보았습니다.
