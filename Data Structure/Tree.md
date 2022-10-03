> [윤성우의 열혈 자료구조](https://www.orentec.co.kr/booklist/DA_ST_1/book_sub1.php) 를 읽고 정리한 자료이며 Kotlin으로 코드를 작성했습니다

<br>
<br>

# Tree 자료구조
`Tree` 는 노드들이 나뭇가지처럼 노드를 늘려가며 뻗어나가는 비선형 계층적 관계를 표현하는 자료구조입니다.

<p align="center">
    <img src="https://mblogthumb-phinf.pstatic.net/20140219_285/4717010_13927847504767z2aF_PNG/350px-Sorted_binary_tree_svg.png?type=w2">
</p>

트리 관련한 용어는 다음과 같습니다.
- 노드(Node) : 트리의 구성요소에 해당되는 A, B, C, D 등과 같은 요소
- 간선(Edge) : 노드와 노드를 연결하는 연결선
- 루트 노드(Root Node) : 트리 구조에서 최상위에 존재하는 F와 같은 노드
- 단말 노드(Terminal Node) : 아래로 또 다른 노드가 연결되어 있지 않은 A, C, E, H와 같은 노드
- 내부 노드(Internal Node) : 단말 노드를 제외한 모든 노드, 또 다른 노드가 이어진 B, D, F, G, I와 같은 노드

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/192889794-ae6cacfc-3537-4b81-a967-aaed3002c939.png">
</p>

트리에서는 각 층별로 숫자를 매겨서 트리의 `레벨` 이라 하고, 트리의 최고 레벨을 `높이` 라고 합니다.

<br>
<br>
<br>

# 서브 트리와 이진 트리

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/192887989-4622c13b-459f-4949-addc-391a89ac4380.png">
</p>

서브 트리는 큰 트리에 속하는 작은 트리를 말합니다. 위 사진의 F가 루트 노드인 트리의 서브 트리는 `B가 루트 노드인 트리` 와 `G가 루트 노드인 트리` 입니다.  
<br>

이진 트리(Binary Tree)는 이름으로 유추할 수 있듯이 최대 두 개의 자식 노드를 가질 수 있는 트리입니다.
이진 트리를 만족하기 위해선 다음과 같은 조건을 만족해야 합니다.
- 루트 노드를 중심으로 두 개의 서브 트리로 나뉘어진다.
- 나뉘어진 두 서브 트리도 모두 이진 트리이어야 한다.

서브 트리로 예로 든 위 사진의 트리도 이진 트리라고 볼 수 있습니다.  

이진 트리는 상황에 따라 `정 이진 트리` , `완전 이진 트리` , `포화 이진 트리` , `이진 트리` 로 나눌 수 있습니다.

## 이진 트리의 종류
`포화 이진 트리` 는 단말 노드를 제외한 모든 노드들이 2개의 자식을 가지는 이진 트리입니다. 포화 이진 트리의 단말 노드는 무조건 내부 노드 + 1 개를 가지고 있습니다([증명](https://www.geeksforgeeks.org/handshaking-lemma-and-interesting-tree-properties/))  

`완전 이진 트리` 는 노드가 위에서 아래로, 왼쪽에서 오른쪽의 순서대로 노드가 체워진 이진 트리입니다. 쉽게 생각하면 마지막 레벨을 제외한 모든 레벨에 노드가 꽉 채워져 있어야 하고 마지막 레벨의 노드는 왼쪽부터 순서대로 채워진 트리입니다.

> Full Binary Tree 와 Complete Binary Tree 의 그래프는 다음 [사이트](https://www.programiz.com/dsa/complete-binary-tree)에서 확인할 수 있습니다.

마지막으로 `포화 이진 트리` 는 모든 레벨에 노드가 꽉 차있는 이진 트리를 말합니다.

<br>
<br>
<br>

# 이진 트리 구현
```kotlin
class TreeNode<E>{
    var leftNode: TreeNode<E>? = null
        private set
    var rightNode: TreeNode<E>? = null
        private set
    var data: E? = null

    fun makeLeftSubTree(subTreeNode: TreeNode<E>){
        leftNode?.let { InorderTraverse(it) }
        leftNode = subTreeNode
    }

    fun makeRightSubTree(subTreeNode: TreeNode<E>){
        rightNode?.let{ InorderTraverse(it) }
        rightNode = subTreeNode
    }

    fun InorderTraverse(treeNode: TreeNode<E>) {
        treeNode.leftNode?.run {
            InorderTraverse(this)
            null
        }

        treeNode.data = null

        treeNode.rightNode?.run {
            InorderTraverse(this)
            null
        }
    }
}
```

이진 트리를 구현할 때 서브 트리를 생성하기 위해선 서브 트리에 다른 이진 트리를 저장하기만 하면 안되고 기존 서브 트리를 메모리에서 해제해야 합니다.  

```kotlin
fun makeLeftSubTree(subTreeNode: TreeNode<E>){
    if(leftNode != null) {
        leftNode = null
    }
    leftNode = subTreeNode
}
```

위 코드와 같이 기존의 `leftNode` 만 삭제할 경우 `leftNode` 에 저장된 기존 노드를 삭제할 뿐 데이터들은 메모리에 남아있기 때문에 모든 노드를 순회하며 메모리에서 해제해주어야 합니다.  
<br>

노드를 순회하는 방법은 루트 노드를 언제 방문했나에 따라 3가지로 나눠집니다.
- 전위 순회(Preorder Traversal) : 루트 노드를 먼저
- 중위 순회(Inorder Traversal) : 루트 노드를 중간에
- 후위 순회(Postorder Traversal) : 루트 노드를 마지막에

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/193485873-98d9a304-06da-4b7d-b710-0c9eacbea1f5.png">
</p>

높이가 2인 트리 또한 재귀적으로 구현한다면 쉽게 순화할 수 있습니다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/103296212/193486460-c4862f66-d2f9-40f1-887d-2955fc3a0e7e.png">
</p>

위 그림을 중위 순회하는 단계는 다음과 같습니다.
- 1단계 : 왼쪽 서브 트리를 순회한다.
- 2단계 : 루트 노드를 방문한다.
- 3단계 : 오른쪽 서브 트리를 순회한다.

서브 트리라고 순회하는 방법이 다른게 아니기 때문에 서브 트리도 중위 순회 방법을 그대로 사용하면 됩니다.  
따라서 이진 트리 전체를 중위 순회하는 방법은 다음과 같습니다.

```kotlin
fun InorderTraverse(treeNode: TreeNode<E>){
    InorderTraverse(treeNode.leftNode)
    println(treeNode.data)
    InorderTraverse(treeNode.rightNode)
}
```

다만 다음 재귀문에서는 문제점이 있습니다. `재귀 탈출 조건이 없는 것` 입니다. 서브 트리가 `null` 일 수도 있으니 null 확인을 해주면 됩니다.

```kotlin
fun InorderTraverse(treeNode: TreeNode<E>){
    treeNode.leftNode?.let { InorderTraverse(it) }
    println(treeNode.data)
    treeNode.rightNode?.let { InorderTraverse(it) }
}
```

또한 노드를 방문은 출력 뿐만 아닌 다른 목적이 있을 수 있습니다.

```kotlin
fun InorderTraverse(subTreeNode: TreeNode<E>, action: (TreeNode<E>) -> Any?){
    subTreeNode.leftNode?.let { 
        InorderTraverse(it, action)
    }
    action(subTreeNode)
    subTreeNode.rightNode?.let {
        InorderTraverse(it, action)
    }
}
```