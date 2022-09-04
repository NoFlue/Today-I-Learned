> [윤성우의 열혈 자료구조](https://www.orentec.co.kr/booklist/DA_ST_1/book_sub1.php) 를 읽고 정리한 자료이며 Kotlin으로 코드를 작성했습니다

<br>
<br>

# Stack
`Stack` 은 "쌓다" 라는 의미이며, 데이터를 쌓아 올리는 선형 자료구조입니다. 일상생횔에서도 쉽게 찾을 수 있으며, `설거지 후 쌓아 올린 접시들`, `쌓아 올린 상자 더미들` 이 예시라고 볼 수 있습니다.  
<br>

스택은 `후입선출` 방식의 자료구조이기 때문에 마지막에 들어온 데이터가 먼저 나오는 구조입니다. 그렇기 때문에 `Later-In, First-Out (LIFO)` 구조의 자료구조라고 불립니다.
<p align="center">
    <img src="https://blog.kakaocdn.net/dn/CSWsW/btq2t9Wc0um/qTQgiVXqjxA0l9weuUKTH1/img.png">
</p>

<br>

스택 자료구조의 ADT는 다음과 같이 정의할 수 있습니다.
-  `fun empty(): Boolean` : 스택이 비어있는지 확인합니다.
- `fun <T> push(item: E)` : 스택에 데이터를 추가합니다.
- `fun <T> pop(): E` : 마지막에 저장된 데이터를 삭제합니다.
- `fun <T> peek(): E` : 마지막에 저장된 데이터를 확인합니다.

<br>
<br>
<br>

# Stack 구현
스택은 `배열 리스트 기반` 과 `연결 리스트 기반` 으로 구현할 수 있습니다. 연결 리스트 기반으로 만드는 경우는 기존 연결 리스트의 데이터 추가를 순차적으로 하고, ADT를 스택과 같이 정의하면 되기 때문에 배열 리스트 기반으로 구현해보겠습니다.  
<br>

```kotlin
class Stack<E>{
    private val storage = arrayListOf<E>()
}
```

스택은 데이터들을 쌓아 놓을 저장소를 필드로 생성해주면 됩니다.

## push
`push` 는 데이터를 추가하는 메서드입니다.
```kotlin
fun push(item: E) = storage.add(item)
```

## pop
`pop` 은 마지막에 저장된 데이터를 삭제하는 메서드이며, 삭제한 데이터를 반환합니다.
```kotlin
fun pop(): E{
    if (storage.isEmpty()) throw EmptyStackException()
    else return storage.removeLast()
}
```

## peek
`peek` 은 마지막에 저장된 데이터를 확인합니다.
```kotlin
fun peek() = storage.last()
```

## empty
`empty` 는 스택이 비었는지 확인합니다.
```kotlin
fun empty() = storage.isEmpty()
```

자바의 배열리스트를 이용했기 때문에 매우 쉽게 구현할 수 있었습니다. 즉, 스택의 성질만 이용한다면 배열리스트를 스택으로 사용할 수 있습니다.  
<br>

스택은 데이터를 추가하는 방법이 매우 제한적이기 때문에 코틀린의 확장 함수를 이용해 해결할 수 있습니다.
```kotlin
fun <T: Any> Stack<T>.push(items: Iterable<T>){
    for(item in items){
        this.push(item)
    }
}

fun <T: Any> Stack<T>.push(vararg items: T){
    this.push(items.asList())
}

fun <T: Any> stackOf(vararg items: T): Stack<T>{
    val stack = Stack<T>()
    
    for(item in items){
        stack.push(item)
    }
    return stack
}


// main
val stack = stackOf(1, 2, 3, 4)  //[1, 2, 3, 4]
stack.push(5, 6, 7) // [1, 2, 3, 4, 5, 6, 7]
stack.push(listOf(8, 9, 10)) // [1, 2, 3, 4, 5, 6, 7, 8, 9 ,10]
```