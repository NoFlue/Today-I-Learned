> [윤성우의 열혈 자료구조](https://www.orentec.co.kr/booklist/DA_ST_1/book_sub1.php) 를 읽고 정리한 자료이며 Kotlin으로 코드를 작성했습니다

# 우선순위 큐
`우선순위 큐` 는 큐와 달리 먼저 들어온 데이터가 나오는 자료구조가 아닌 **들어간 순서에 상관없이 우선순위가 높은 데이터가 먼저 나오**는 자료구조입니다. 우선순위를 비교하기 위해선 비교할 데이터들의 우선순위를 나타내는 정보가 있어야 하며, 이 정보는 정수 뿐만 아닌 비교할 수 있는 자료의 형태면 됩니다. 
<br>

우선순위 큐를 구현하기 위한 방법은 3가지가 존재합니다.
- 배열을 기반으로 구현하는 방법
- 연결리스트를 기반으로 구현하는 방법
- 힙을 이용하는 방법

하지만 배열과 연결리스트를 기반으로 구현하게 되면 데이터를 삽입할 위치를 찾기 위해서 최악의 상황일 때 **첫 번째 데이터부터 마지막 데이터와 비교해야하는 문제점**이 발생할 수 있습니다. 소량의 데이터를 가진 리스트라면 상관없지만 대량의 데이터를 가지고 있을 겨우 데이터의 수에 비례해서 성능을 저하시키는 주 원인이 됩니다.
<br>

그렇기 때문에 우선순위 큐는 `힙(Heap)` 을 사용하여 구현하는 것이 일반적입니다.

<br>
<br>

# 힙(Heap)
`힙` 은 **완전 이진 트리**이며, **모든 노드에 저장된 값은 자식 노드에 저장된 값보다 크거나 같아**야합니다. 비교하는 값은 데이터의 값이 될수도 있고, 우선 순위가 될 수도 있습니다. 

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/194693388-f0b9fb65-21f8-4b92-ab62-740ef676b3bb.png">
</p>

위 그림은 힙을 만족하는 연결리스트를 이용한 이진 트리이며, 루트 노드로 올라갈수록 데이터의 값이 커지는 `최대 힙(max heap)` 입니다. 반면 루트 노드로 올라갈수록 데이터의 값이 작아지는 힙은 `최소 힙(min heap)` 입니다.
<br>

## 힙의 저장과 삭제

힙의 저장 과정은 다음과 같습니다.
> 새로운 데이터는 우선순위가 제일 낮다는 가정하에서 마지막 위치에 저장하고, 부모 노드와 비교해가며 위치를 바꿔준다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/194693901-2617ff61-52ec-4c4f-9d34-c3933e3e573c.png">
</p>

힙의 삭제는 최고 우선순위인 루트 노드의 데이터를 삭제하는 방식입니다. 하지만 루트 노드를 삭제하게 되면 트리의 구조가 무너지기 때문에 다른 노드가 루트 노드를 채워야합니다.  
> 루트 노드를 채우기 위해선 힙의 마지막 노드를 루트 노드로 옮긴 후 자식 노드와 비교해가며 위치를 바꿔준다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/194694298-2e6f058e-e0e0-4d1c-8e30-00706f0a84d3.png">
</p>
<br>

## 힙으로 구현한 성능
힙으로 구현한 이유는 배열과 연결리스트로 구현한 것 보다 더 나은 성능이 있기 때문에 구현한 것인데 그렇다면 어떤 면에서 더 적합할까요?
<br>

- 배열과 연결리스트 기반 데이터 저장의 시간 복잡도 : O(n)
- 배열과 연결리스트 기반 데이터 삭제의 시간 복잡도 : O(1)

배열과 연결리스트 기반의 데이터 저장 방식은 배열에 저장된 모든 데이터와의 비교 과정을 거쳐야하기 때문에 O(n)의 시간 복잡도를 가지며, 삭제의 과정은 맨 앞의 데이터만 삭제하면 되기 때문에 O(1)의 시간 복잡도를 가집니다.  

힙은 **트리의 높이에 해당하는 수만큼만 비교연산을 진행**하면 되기 때문에 시간 복잡도는 다음과 같습니다.
- 힙 기반 데이터 저장의 시간 복잡도 : O($log{_2}{n}$)
- 힙 기반 데이터 삭제의 시간 복잡도 : O($log{_2}{n}$)

<br>

## 힙의 구현
이때까지 힙을 설명하기 위해서 연결리스트 기반의 힙으로 구현했습니다. 하지만 연결리스트로 구현한 힙은 단점이 있습니다.
> 연결리스트로 힙을 구현하면, 새로운 노드를 힙의 **마지막 위치**에 삽입하기 어렵다.

이 문제를 배열 기반의 힙으로 쉽게 해결할 수 있습니다.  
힙과 같이 새로운 노드를 추가한 이후에도 완전 이진 트리를 유지해야 하는 경우엔 배열 기반으로 트리를 구현해야 합니다.
<br>

## 배열 기반의 힙
배열을 기반으로 힙을 만들기 위해선 노드에 고유한 번호를 부여하며, 이 고유한 번호는 배열의 인덱스 값이 됩니다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/194696535-6348559e-6d39-4e16-a712-302230bf5ee7.png">
</p>

구현을 편하기 위해서 루트 노드의 인덱스를 1로 잡았기 때문에 인덱스가 0인 위치에는 아무 값을 넣지 않습니다.  

기본적으로 배열 기반으로 힙을 구현하기 위해서는 다음과 같은 정보를 알아야 합니다.
- 왼쪽 자식 노드의 인덱스 값 : 부모 노드의 인덱스 값 * 2
- 오른쪽 자식 노드의 인덱스 값 : 부모 노드의 인덱스 값 * 2 + 1
- 부모 노드의 인덱스 값 : 자식 노드의 인덱스 값 / 2

<br>
<br>

# 배열 기반의 힙 구현
힙을 구현하기 전 다음 사실들을 정리하고 구현을 해보겠습니다.
- 힙은 완전 이진 트리이다.
- 힙의 구현은 배열 기반으로 하며, 인덱스가 0인 요소는 비워둔다.
- 힙의 고유 번호는 배열의 인덱스 값이다.
- 힙에 저장된 노드의 개수와 마지막 노드의 인덱스 값은 같다.
- 우선순위를 나타내는 정수 값이 작을수록 높은 우선순위를 나타낸다.

힙 구현 코드
```kotlin
data class HeapNode(
    var priority: Int,
    var data: Int? = null
)

class Heap{
    var numOfData: Int = 0
    var heapArr: Array<HeapNode?> = arrayOfNulls(100)

    fun isEmpty() = numOfData == 0

    fun getParentIndex(idx: Int) = idx / 2

    fun getLeftChildIndex(idx: Int) = idx * 2

    fun getRightChildIndex(idx: Int) = getLeftChildIndex(idx) + 1

    fun getHighPriorityChildIndex(idx: Int): Int{
        if(getLeftChildIndex(idx) > numOfData){
            return 0
        }
        else if(getLeftChildIndex(idx) == numOfData){
            return getLeftChildIndex(idx)
        }
        else{
            if(heapArr[getLeftChildIndex(idx)]!!.priority > heapArr[getRightChildIndex(idx)]!!.priority){
                return getRightChildIndex(idx)
            }else{
                return getLeftChildIndex(idx)
            }
        }
    }
    
    fun enqueue(data: Int, priority: Int){
        var index = numOfData + 1
        val node = HeapNode(priority, data)
        
        while(index != 1){
            if(priority > heapArr[index]!!.priority){
                heapArr[index] = heapArr[getParentIndex(index)]
                index = getParentIndex(index)
            }else{
                break
            }
        }
        
        heapArr[index] = node
        numOfData += 1
    }
    
    fun dequeue(): HeapNode {
        val lootNode = heapArr[1]!!
        val node = heapArr[numOfData]!!
        
        var parentIndex = 1
        var childIndex = getHighPriorityChildIndex(parentIndex)
        
        while(node.priority > heapArr[childIndex]!!.priority){
            heapArr[parentIndex] = heapArr[childIndex]
            parentIndex = childIndex
        }
        
        heapArr[parentIndex] = node
        numOfData -= 1
        
        return lootNode
    }
}
```