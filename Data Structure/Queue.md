> [윤성우의 열혈 자료구조](https://www.orentec.co.kr/booklist/DA_ST_1/book_sub1.php) 를 읽고 정리한 자료이며 Kotlin으로 코드를 작성했습니다

<br>
<br>

# Queue
`Stack` 이 LIFO 라면 `Queue` 는 FIFO(First-In, First-Out) 입니다. 큐는 선입선출 자료구조이기 떄문에 먼저 들어온 데이터가 먼저 나오게 됩니다.
<p align="center">
    <img src="https://t1.daumcdn.net/cfile/tistory/9929C0495C932BB115">
</p>

큐의 ADT는 큐에 데이터를 넣는 `enqueue` 와 데이터를 꺼내는 `dequeue` 가 있습니다. 큐를 연산하는 ADT 뿐만 아닌 다른 ADT도 정의할 수 있습니다.
- `fun isEmpty(): Boolean` : 큐가 비어있는지 확인합니다.
- `fun peek(): E` : 저장순서가 가장 앞선 데이터를 반환합니다.

<br>
<br>
<br>

# Queue의 원리
큐는 연결 리스트와 같이 큐의 머리를 `F(Front)` , 큐의 꼬리를 `R(Rear)` 로 가리킵니다.  

`enqueue` 연산 시에는 R 이 다음 칸을 가리키게 되고, 그 자리에 새 데이터가 저장됩니다. 그렇다면 `dequeue` 연산 시에는 기존 데이터들이 앞으로 한 칸씩 이동해야 하는데 이렇게 되면 F 가 불필요하게 되고, 이동해야 한다는 단점이 있습니다.  
이 방식을 해결하기 위해 일반적으로는 F를 이동시키는 방법을 사용합니다.
<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/191456252-e0960da2-f101-4756-bd9c-cf72959e0a57.png">
</p>

하지만 이 방법도 완전하지 않은데, C 데이터가 배열의 끝에 저장되어 있기 때문에 `enqueue` 연산을 더이상 못하게 됩니다. 연산을 하기 위해선 R 이 첫 인덱스로 이동을 시켜야 하는데 이를 해결하기 위해 원형 큐(Circular Queue) 가 나오게 되었습니다.  
<br>

원형 큐를 사용함으로 배열을 효율적으로 사용할 수 있게 되었습니다. 
<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/191459399-ddfc4d41-e1f6-41fa-b3e6-7929fc485c4c.png">
    <div></div>
    <img src="https://user-images.githubusercontent.com/103296212/191460254-d61b9d60-f552-4c33-b83b-f975a68e5230.png">
</p>

원형 큐를 이용해 연산하면 선형과 다르게 F와 R이 회전하기 떄문에 `dequeue` 연산으로 인한 사용할 수 없는 빈 공간이 생기지 않습니다.  
하지만 이 또한 완벽한 큐가 될 수가 없는데 이는 `꽉찬 상태` 와 `빈 상태` 를 구분할 수 없기 때문입니다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/191462560-bdaf7ea5-9849-44b9-bdac-a48892dffbf1.png">
</p>

`꽉찬 상태` 와 `빈 상태` 둘 다 똑같이 F가 R 앞에 위치해 있습니다. 그렇기 때문에 F와 R 위치로만 상태를 구별하기 어렵습니다. 다른 방법을 사용하여 구별할 수 있지만 일반적으로 가장 널리 알려진 해결책은 다음과 같습니다.
> 배열을 꽉 채우지 않고 N-1개 채워졌을 떄, 꽉찬 상태로 간주한다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/191464238-0650e3f9-8649-47f1-8823-6b5d05a65a59.png">
</p>

꽉찬 상태일 경우 F가 R보다 한 칸 앞에 있지만 빈 성태일 경우 F와 R이 같은 위치에 있습니다.  
<br>

이제 모든 문제를 해결했으니 원형 큐를 구현해보겠습니다.

<br>
<br>
<br>

# Circular Queue 구현
```kotlin
class Queue {
    private var front: Int = 0
    private var rear: Int = 0
    private val data = Array<Int>(10) { 0 }

    val isEmpty: Boolean
        get() = front == rear

    fun peek() = if (isEmpty) null else data[front + 1]

    fun nextPosIndex(pos: Int) = if (pos == 9) 0 else pos + 1

    fun enqueue(value: Int): Boolean {
        if (nextPosIndex(rear) == front) return false

        rear = nextPosIndex(rear)
        data[rear] = value
        return true
    }

    fun dequeue(): Int? {
        if (isEmpty) return null

        front = nextPosIndex(front)
        return data[front]
   }
}
```

```kotlin
fun main() {
    val queue = Queue()

    repeat(5){
        queue.enqueue(it)
    }

    while (!queue.isEmpty){
        print("${queue.dequeue()} ")
    }
}
```
결과는 `0 1 2 3 4` 로 구현한 Queue가 잘 작동하는 모습을 볼 수 있습니다.  

위 코드에서 원형큐임을 알 수 있는 부분은 `fun nextPosIndex(pos: Int)` 함수입니다. `rear 및 front` 가 배열의 마지막 인덱스에 도달할 경우 인덱스가 0으로 옮겨져 순환을 하기 떄문입니다.

<br>
<br>
<br>

# Deque 
`Deque` 은 `Double-ended-queue`를 줄여서 표현한 것으로, 양방향으로 넣고 뺄 수 있는 자료구조입니다. 스텍과 큐를 조합한 형태의 자료구조 이기 떄문에 ADT는 다음과 같습니다.
- 앞으로 넣기
- 앞으로 뺴기
- 뒤로 넣기
- 뒤로 빼기

`Deque` 은 덱 또는 `dequeue` 와 같은 발음인 디큐로 불립니다. `dequeue` 와 혼동되지 않기 위해 덱이라 부릅니다.