> [윤성우의 열혈 자료구조](https://www.orentec.co.kr/booklist/DA_ST_1/book_sub1.php) 를 읽고 정리한 자료이며 Kotlin으로 코드를 작성했습니다

# ADT (Abstract Data Type) 추상 자료형
`추상 자료형(ADT)` 는 구체적인 기능의 완성 과정을 언급하지 않고, 순수하게 기능이 무엇인지를 나열한 것입니다. OOP 언어의 Class와 매우 유사하다고 볼 수 있습니다.  
<br>

`자료 구조` 는 추상 자료형이 정의한 연산들의 구현체를 가리키기 떄문에 `추상 자료형` 과는 다릅니다.  
<br>



# 리스트
리스트는 **배열을 기반으로 구현된 `배열 리스트`** 와  **메모리의 동적 할당을  기반으로 구현된 `연결 리스트`** 두 가지 종류가 있습니다.  
<br>

두 리스트는 각각 장단점이 존재합니다.
- 배열 리스트
    - 장점 : 인덱스를 기준으로 값을 얻기 때문에 한 번에 참조가 가능하다.
    - 단점 : 정적이기 때문에 초기에 배열의 길이를 정해주어야 한다. 삭제 과정에서 데이터의 이동이 매우 빈번하다.
- 연결 리스트
    - 장점 : 배열의 길이가 동적이며, 각 노드가 다음 데이터의 주소 값을 가지기 때문에 삽입 및 삭제를 할 경우 연결된 데이터의 주소 값만 바꾸면 되기 떄문에 효율적이다.
    - 단점 : 데이터 조회를 할 경우 첫 번째부터 순차대로 조회하기 때문에 최대 O(n)의 시간이 걸린다.

연결 리스트가 어떤 원리로 데이터의 연산을 하는지 직접 코드를 작성하여 확인을 해보겠습니다.

<br>
<br>
<br>

# 연결 리스트 구현
연결 리스트는 여러 노드들의 연결로 리스트를 이루고 있습니다.
```kotlin
data class Node<T>(
    var data: T,
    var next: Node<T>? = null
)
```
노드는 값을 가지는 `data` 와 다음 노드를 가리키는 `next` 로 이루어져 있습니다.  
배열 리스트와 달리 노드끼리 연결을 하여 리스트를 이루고 있기 때문에 배열의 길이가 동적일 수 있습니다.
<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/185076242-ba99ee05-ad28-4f79-99bc-70e5e9dde244.png">
</p>
<br>

연결 리스트는 두 가지 방식으로 구현을 할 수 있는데, 새 노드를 추가할 떄 머리에 추가하는 방법과 꼬리에 추가하는 방법이 있습니다. 역시 각 방법마다 장단점이 존재합니다.
- 머리
    - 장점 : tail 변수가 불필요하다.
    - 단점 : 저장된 순서를 유지하지 않는다. ( 머리에 값을 순차적으로 추가하기 때문에 역순으로 저장된다. )
- 꼬리
    - 장점 : 저장된 순서가 유지된다.
    - 단점 : tail 변수가 필요하다.

<br>

```kotlin
class LinkedList<T>{
    private var headNode: Node<T>? = null
    private var lastNode: Node<T>? = null
    var size = 0
        private set
}
```
연결 리스트의 필드입니다. 꼬리와 머리 모두 추가하는 방법을 이용해 연결 리스트를 구현하며 연결 리스트의 구조를 알아보겠습니다. 
<br>
<br>

## 탐색
연결 리스트의 특정 위치의 노드를 찾기 위해서는 순차적으로 조회해야 합니다.
```kotlin
fun findNodeByPosition(position: Int): Node<T>?{
    var currentNode = headNode

    repeat(position){
        currentNode = currentNode?.next
    }

    return currentNode
}
```
<br>
<br>

## 삽입
```kotlin
fun insertAtHead(data: T){
    val newNode = Node(data, headNode)
    headNode = newNode
    size++

    if(lastNode == null) lastNode = newNode
}
```
<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/185084995-c31b4442-48ea-4672-b65b-bde90bb2c832.png">
</p>
B 값을 가진 새로운 노드의 next를 머리를 가리키게 한 다음 머리가 새로운 노드를 가리키게 하여 노드를 삽입할 수 있습니다.  
<br>

```kotlin
fun insetAtLast(data: T){
    val newNode = Node(data, null)
    lastNode?.next = newNode
    lastNode = newNode
    size++

    if(headNode == null) headNode = newNode
}
```
<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/185096636-5efc3750-a717-4bf6-80e6-9e8c4739c88f.png">
</p>
B 값을 가진 새로운 노드를 기존의 마지막 노드였던 A의 next와 연결한 다음 마지막 노드를 새로운 노드로 지정해주어 노드를 삽입할 수 있습니다.

<br>
<br>

## 삭제
```kotlin

```