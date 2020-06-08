# Binary Search Tree (이진 탐색 트리)

## 1. 개요 및 특징

이진 탐색트리는 이진 탐색과 이진 트리를 결합한 자료 구조이다.

자료가 선형으로 나열되어 있는 자료(예 - 리스트 / 배열)에 대한 이진 탐색을 수행하기 위해서는 원소가 항상 정렬되어 있어야 하며 원소를 새로 삽입할 때 정렬 순서에 맞게 자료를 삽입해야 하므로 원소의 위치를 삽입이 발생할 때 마다 변경해야 하는 단점이 있다.

이진 탐색 트리는 이진 탐색에 비해 일반적인 상황에 대해 처리 속도가 빠르지만 최악에 상황에서는 리스트나 배열과 처리 속도가 동일해지며 배열을 통해 이진 트리를 구현할 경우 메모리 낭비가 심하다.

이진탐색트리는 내부 데이터가 포화이진트리에 가까워질수록 데이터의 삽입 및 검색이 간단해지며 포화이진트리에 가까울수록 데이터를 탐색하는데 걸리는 시간 복잡도는 O(log n)이 된다. 하지만 삽입되는 순열이 정렬되어 있으면 왼쪽 혹은 오른쪽으로 편향된 편향트리가 되며 이 경우에 데이터를 삽입 및 검색 시 시간복잡도는 O(n)이 된다. 아래 기본 구조와 동작에서 해당 상황에 대한 상세한 설명은 별도로 없지만 이진 탐색 트리를 직접 구현하여 보면 직관적으로 왜 그렇게 되는지 이해할 수 있다.

이진탐색트리를 중위 순회로 순회하면 오름차순 정렬이 된다.

해당 예제에서는 연결 리스트를 이용한 이진 트리를 이용하여 이진 탐색 트리를 구현하며 동일한 원소가 삽입되는 경우가 없음을 전제로 진행한다.

### 1. 기본 구조 및 동작

이진 트리에 값을 집어 넣을 때 부모 노드의 값을 기준으로 왼쪽 노드에는 큰 값을, 오른쪽 노드에는 작은 값을 집어 넣는다. 만약 `5 - 2 - 7 - 4 - 8 - 1 - 9` 의 순서대로 이진 트리에 값을 집어넣는다 생각해보자.

빈 트리에 5를 집어 넣을 때에는 무엇과도 비교할 대상이 없으므로 루트 노드에 값을 집어 넣는다.

```
(Depth 0)               [5]
```

다음 숫자 2를 집어 넣는다 생각해보자. 루트노드의 5보다 2가 작으므로 왼쪽 서브노드에 접근한다. 현재 루트노드는 왼쪽 서브트리가 없으므로 신규 리프 노드를 생성하여 값을 삽입한다.

```
(Depth 0)               [5]
                       /
(Depth 1)           [2]
```

다음 숫자 7을 집어 넣는다 생각해보자. 루트노드의 5보다 7이 크므로 오른쪽 서브노드에 접근한다. 현재 루트노드는 오른쪽 서브트리가 없으므로 신규 오른쪽 리프 노드를 생성한다.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
```

다음 숫자 4를 집어 넣는다 생각해보자. 루트노드의 5보다 4가 작으므로 왼쪽 서브노드에 접근한다. 왼쪽 서브노드 2보다 4가 크므로 오른쪽 서브트리에 접근해야 한다.  현재 깊이 1의 값 2를 가지고 있는 노드는 오른쪽 서브트리가 없으므로 신규 오른쪽 리프 노드를 생성한다.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
                       \
(Depth 2)               [4]
```

다음 숫자 8을 집어 넣는다 생각해보자. 루트노드의 5보다 8이 크므로 오른쪽 서브노드에 접근한다. 오른쪽 서브노드 값 7보다 8이 크므로 오른쪽 서브트리에 접근해야 한다.  현재 깊이 1의 값 7을 가지고 있는 노드는 오른쪽 서브트리가 없으므로 신규 오른쪽 리프 노드를 생성한다.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
                       \         \
(Depth 2)               [4]       [8]
```

다음 숫자 1을 집어 넣는다 생각해보자. 루트노드의 5보다 1이 작으므로 왼쪽 서브노드에 접근한다. 왼쪽 서브노드의 2보다 1이 작으므로 왼쪽 서브노드에 접근한다. 왼쪽 서브노드엔 노드가 존재하지 않으므로 신규 리프를 생성한다.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
                   /   \         \
(Depth 2)        [1]   [4]        [8]
```

마지막으로 숫자 9를 집어 넣는다 생각해보자. 5보다 9가 크므로 오른쪽 접근하며 7보다 9가 크므로 오른쪽 접근한다. 8보다 9가 크므로 오른쪽에 접근한다. 서브 노드가 없으므로 리프 노드를 생성한다.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
                   /   \         \
(Depth 2)        [1]   [4]        [8]
                                     \
(Depth 3)                            [9]
```

다음과 같이 특정 순열에 대한 이진 탐색 트리가 생성되었다. **위 이진 탐색 트리에 중위 순회로 접근을 한다면 접근 순서는 순열이 오름차순으로 정렬된 결과와 같게 된다.**

위 과정에 대한 삽입 알고리즘 (슈도 코드)을 재귀적으로 구현할 시의 예제는 다음과 같다.

```
// 재귀 함수를 이용한 구현
function insert(node, data)
{
    if(인수로 입력된 node가 null일 경우)
        인수로 입력된 node에 신규 leaf 노드 생성 후 데이터 삽입
    else if(node가 존재하고 node의 데이터 값이 인수 data보다 작을 때)
        insert(인수로 입력된 node의 왼쪽 서브 node)
    else // node가 존재하고 node의 데이터 값이 인수 data보다 클 때
        insert(인수로 입력된 node의 오른쪽 서브 node)
}
insert(루트 노드, 데이터) // 해당 함수 실행 시 데이터 삽입됨.
```

insert 함수는 재귀적으로 node가 null이 될 때까지 조건에 따라 실행된다. 다음과 같이 구현할 시 인수로 입력되는 노드가 함수가 호출될 때 어떤 식으로 접근되는지도 고려하여야 한다.

재귀 함수를 이용하지 않고 반복문으로 구현할 시의 예제는 다음과 같다.

```
function insert(node, data)
{
    접근 노드 = 루트 노드
    while(true)
    {
        if(접근노드가 null 일 시 )
            루트 노드에 신규 리프 노드와 데이터 삽입
            반복문 탈출
        else
            if(접근 노드의 데이터 값이 인수 data보다 클 때)
            {
                if(접근 노드의 왼쪽 필드가 null 이면)
                    접근 노드의 왼쪽 트리에 신규 리프 노드와 데이터 삽입
                    반복문 탈출
                else
                    접근 노드 = 접근 노드의 왼쪽 서브 트리
            }
            else
            {
                if(접근 노드의 오른쪽 필드가 null 이면)
                    접근 노드의 오른쪽 트리에 신규 리프 노드와 데이터 삽입
                    반복문 탈출
                else
                    접근 노드 = 접근 노드의 오른쪽 서브 트리
            }
            
    }
}
```

## 2. 이진탐색트리에서 데이터 탐색

다음 트리에서 숫자 8을 검색한다 가정하여 보자.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
                   /   \         \
(Depth 2)        [1]   [4]        [8]
                                     \
(Depth 3)                            [9]
```

접근 방법은 위 삽입과 유사하다. 노드의 값에 접근하여 비교 후 일치하는 데이터가 존재하면 탐색을 마친다.

먼저 루트노드의 값 5와 8을 비교한다. 8은 5보다 크므로 오른쪽 서브트리에 접근한다. 오른쪽 서브트리의 값 7은 8보다 작으므로 다시 오른쪽 서브트리에 접근한다. 해당 서브트리의 값은 탐색하고자 하는 값과 같으므로 검색을 중단한다.

위와 동일한 트리에서 해당 노드에 값이 존재하지 않는 6을 검색한다 가정하여 보자.

먼저 루트노드의 값 5와 6을 비교한다. 6은 5보다 크므로 오른쪽 노드에 접근한다. 오른족 서브트리 노드의 값 7은 6보다 크므로 왼쪽 서브트리에 접근한다. 하지만 해당 트리는 존재하지 않으므로 검색 실패 결과를 전달한다.

위 과정에 대한 검색 알고리즘 (슈도 코드)을 재귀적으로 구현할 시의 예제는 다음과 같다.

```
// 재귀 함수를 이용한 구현
function search(node, data)
{
    if(인수로 입력된 node가 null일 경우)
        검색 실패
    else if(node가 존재하고 node의 데이터 값이 인수 data와 같을 때)
        검색 성공
    else if(node가 존재하고 node의 데이터 값이 인수 data보다 작을 때)
        search(인수로 입력된 node의 왼쪽 서브 node)
    else // node가 존재하고 node의 데이터 값이 인수 data보다 클 때
        search(인수로 입력된 node의 오른쪽 서브 node)
}
search(루트 노드, 데이터)
```

insert 함수는 재귀적으로 호출되며 검색 실패 혹은 성공할때까지 반복한다.

재귀 함수를 이용하지 않는 방법은 다음과 같다.

```
function search(data)
{
    접근 노드 = 루트 노드
    while(true)
    {
        if(인수로 입력된 node가 null일 경우)
            검색 실패
            반복문 탈출
        else if(접근 노드의 데이터 값이 인수 data와 같을 때)
            검색 성공
            반복문 탈출
        else if(접근 노드의 데이터 값이 인수 data보다 작을 때)
            접근 노드 = 접근 노드의 왼쪽 서브 node
        else // 접근 노드의 데이터 값이 인수 data보다 클 때
            접근 노드 = 접근 노드의 오른쪽 서브 node
    }
}
```

포인터(레퍼런스)를 계속 조작해야 하는 삽입보다 비교적 단순하다.

## 3. 이진트리에서 데이터 삭제

이진 트리에서 데이터를 삭제하는 경우는 보통 3가지 케이스를 나누어 생각한다.

### 1. 삭제하려는 노드가 자식 노드가 없을 때

삭제하려는 노드가 자식 노드가 없을 때 해당 노드만 제거하면 된다.

리프 노드를 삭제한다 해도 트리 구조가 깨지지 않는다.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
                   /   \         \
(Depth 2)        [1]   [4]        [8]
                                     \
(Depth 3)                            [9]
```

위 트리에서 리프 노드를 삭제한다 해도 트리 구조에 영향을 끼치지 않는 것을 알 수 있다.

리프 노드의 삭제 알고리즘은 다음과 같다.

```
[삭제 대상 노드의 부모 노드]가 존재하지 않을 시에는 루트 노드이므로
    루트 노드 = null
아니면 [삭제 대상 노드의 부모 노드]의 왼쪽 노드가 삭제 대상 노드일 시
    [삭제 대상 노드의 부모 노드]의 왼쪽 노드 = null
아니면 [삭제 대상 노드의 부모 노드]의 오른쪽 노드가 삭제 대상 노드일 시
    [삭제 대상 노드의 부모 노드]의 오른쪽 노드 = null
```

단순히 삭제 대상 노드에 접근하여 해당 노드를 제거하면 된다.

### 2. 삭제하려는 노드가 자식 노드를 하나만 가지고 있을 때

이 상황에서도 동일하게 해당 노드만 제거하면 된다. 왜냐면 해당 노드를 제거한다 해도 트리 구조에 영향을 끼치지 않기 때문이다.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
                   /   \         \
(Depth 2)        [1]   [4]        [8]
                                     \
(Depth 3)                            [9]
```

위 트리에서 `7` , `8` 을 삭제한다 해도 단순히 해당 노드만 삭제하고 이어 붙이는 것이므로 트리 구조에 영향을 끼치지 않는 것을 알 수 있다.

자식 노드를 하나만 가지고 있는 노드의 삭제 알고리즘은 다음과 같다.

```
[삭제 대상 노드의 부모 노드]가 존재하지 않을 시에는 루트 노드이므로
    루트 노드 = 삭제 대상 노드의 자식 노드
아니면 [삭제 대상 노드의 부모 노드]의 왼쪽 노드가 삭제 대상 노드일 시
    [삭제 대상 노드의 부모 노드]의 왼쪽 노드 = [삭제 대상 노드의 자식(왼쪽/오른쪽) 노드]
아니면 [삭제 대상 노드의 부모 노드]의 오른쪽 노드가 삭제 대상 노드일 시
    [삭제 대상 노드의 부모 노드]의 오른쪽 노드 = [삭제 대상 노드의 자식(왼쪽/오른쪽) 노드]
```

삭제 할 노드의 자식 노드가 하나만 있으면 삭제할 노드의 부모 노드와 삭제할 노드의 자식 노드를 이어주면 된다.

### 3. 삭제하려는 노드가 자식 노드를 두개 가지고 있을 때

이 상황에서는 삭제 대상 노드를 삭제하고 나서 그래프의 구조가 변하게 된다.

예를 들어 아래 트리에서 원소 `2`를 제거하는 것을 생각해보자.

```
(Depth 0)               [5]
                       /    \
(Depth 1)           [2]      [7]
                   /   \         \
(Depth 2)        [1]   [4]        [8]
                                     \
(Depth 3)                            [9]
```

2를 제거할 때 해당 위치에 대체할 노드가 들어가야 한다. 왼쪽 자식 노드와 오른쪽 자식 노드 중 어느 노드를 넣을 지 고려해야 하는데 단순히 삭제 노드의 자식 노드를 집어 넣으면 안된다.

해당 경우에서는 2를 제거하면 2 다음으로 작거나 2 다음으로 큰 노드를 해당 자리에 집어 넣어야 한다.

왜냐하면 2에서 갈라지는 노드의 왼편은 모두 2보다 작고 오른편은 모두 2보다 크다. (상기 예에는 1, 4밖에 없지만 이진탐색트리 규칙을 생각해보면 이는 자명하다.) 따라서 이진탐색트리 규칙을 유지하기 위해서 2 다음으로 작거나 큰 수를 둘 중 하나(1 혹은 4)를 노드 2에 덮어 씌우고 기존 노드는 삭제한다. 

알고리즘을 정리하면 다음과 같다. (해당 알고리즘에서는 삭제할 노드 다음으로 큰 수를 대체하는 것으로 함.)

```
삭제할 노드를 탐색한다.
삭제할 노드의 오른쪽 서브트리에서 전위순회 방법으로 삭제할 노드 다음으로 큰 노드를 검색한다.
삭제할 노드에 삭제할 노드 다음으로 큰 수를 덮어 씌운다.
삭제할 노드 다음으로 큰 노드를 삭제한다. (삭제시 해당 노드는 리프 노드이거나 자식이 하나만 있는 노드이므로 위 1, 2 방법을 이용하여 삭제한다.)
```

## 4. Java 구현

하기 자바 구현 코드는 위에서 언급한 삽입, 탐색, 삭제를 구현하였으며 삽입과 탐색은 두 가지 방법으로 구현하였다.

```java
public class BinarySearchTree {
    public ObjectNode rootNode = null;

    public void insert(int data) {
        // approach에 직접 생성자 이용하여 대입하면 실제로 노드에 반영이 안되기 때문0에 다음과 같이 구현함.
        ObjectNode approach = rootNode; // 초기에 루트 노드에 접근함
        while (true) {
            if (approach != null) {
                if(approach.data > data) {
                    if(approach.leftNode == null) {
                        approach.leftNode = new ObjectNode(data);
                        break;
                    } else {
                        approach = approach.leftNode;
                    }
                } else {
                    if(approach.rightNode == null) {
                        approach.rightNode = new ObjectNode(data);
                        break;
                    } else {
                        approach = approach.rightNode;
                    }
                }
            } else { // 초기 상태일때만 실행됨
                rootNode = new ObjectNode(data);
                break;
            }
        }
    }

    public void insertRecursive(int data) {
        if(rootNode == null) {
            rootNode = new ObjectNode(data);
        } else {
            insert(rootNode, data);
        }
    }

    private void insert(ObjectNode node, int data) {
        if(node.data > data) {
            if(node.leftNode == null) {
                node.leftNode = new ObjectNode(data);
            } else {
                insert(node.leftNode, data);
            }
        } else {
            if(node.rightNode == null) {
                node.rightNode = new ObjectNode(data);
            } else {
                insert(node.rightNode, data);
            }
        }
    }

    public boolean search(int data) {
        ObjectNode approach = rootNode; // 초기에 루트 노드에 접근함
        boolean returnValue = false;
        while (true) {
            if (approach == null) {
                break;
            } else if(approach.data == data) {
                returnValue = true;
                break;
            } else if(approach.data > data) {
                approach = approach.leftNode;
            } else {
                approach = approach.rightNode;
            }
        }
        return returnValue;
    }

    public boolean searchRecursive(int data) {
        return search(rootNode, data);
    }

    private boolean search(ObjectNode node, int data) {
        boolean returnValue;
        if(node == null) {
            returnValue = false;
        } else if(node.data == data) {
            returnValue = true;
        } else if(node.data > data) {
            returnValue = search(node.leftNode, data);
        } else {
            returnValue = search(node.rightNode, data);
        }
        return returnValue;
    }

    private boolean deleteLeafNode(ObjectNode parentNode, ObjectNode subNode) {
        boolean returnValue = false;
        if(parentNode == null) {
            parentNode = rootNode;
        }
        // parentNode 에 접근해서 subNode 를 삭제함.
        if(parentNode.leftNode == subNode) {
            parentNode.leftNode = null;
            returnValue = true;
        } else if(parentNode.rightNode == subNode) {
            parentNode.rightNode = null;
            returnValue = true;
        } else if(parentNode == rootNode) {
            rootNode = null;
            returnValue = true;
        }
        return returnValue;
    }

    private boolean deleteEitherNode(ObjectNode parentNode, ObjectNode subNode) {
        boolean returnValue = false;
        if(parentNode == null) {
            parentNode = rootNode;
        }
        if(parentNode.leftNode == subNode) {
            if(subNode.leftNode != null) {
                parentNode.leftNode = subNode.leftNode;
            } else {
                parentNode.leftNode = subNode.rightNode;
            }
            returnValue = true;
        } else if(parentNode.rightNode == subNode) {
            if(subNode.leftNode != null) {
                parentNode.rightNode = subNode.leftNode;
            } else {
                parentNode.rightNode = subNode.rightNode;
            }
            returnValue = true;
        } else if(parentNode == rootNode) {
            if(subNode.leftNode != null) {
                rootNode = subNode.leftNode;
            } else {
                rootNode = subNode.rightNode;
            }
            returnValue = true;
        }
        return returnValue;
    }

    public boolean delete(int data) {
        // 검색 로직 그대로 카피
        ObjectNode approach = rootNode; // 초기에 루트 노드에 접근함
        ObjectNode parent = null;
        boolean returnValue = false;
        while (true) {
            if (approach == null) {
                break;
            } else if(approach.data == data) {
                returnValue = true;
                break;
            } else if(approach.data > data) {
                parent = approach;
                approach = approach.leftNode;
            } else {
                parent = approach;
                approach = approach.rightNode;
            }
        }
        if(returnValue) {
            if(approach.leftNode == null && approach.rightNode == null) { // leaf node
                returnValue = deleteLeafNode(parent, approach);
            } else if(approach.leftNode == null ^ approach.rightNode == null) { // 둘중 하나만 자식이 있을때
                returnValue = deleteEitherNode(parent, approach);
            } else { // 둘다 자식이 있을때
                ObjectNode replaceNode = approach.rightNode; // 해당노드보다 큰 노드를 찾아야 하므로 right node에 접근
                ObjectNode parentNodeOfReplace = approach;
                while(replaceNode.leftNode != null) {
                    parentNodeOfReplace = replaceNode;
                    replaceNode = replaceNode.leftNode;
                }
                // 1. 값 복사
                approach.data = replaceNode.data;
                // 2. 대체 노드 제거
                if(replaceNode.rightNode == null) { // leaf node
                    returnValue = deleteLeafNode(parentNodeOfReplace, replaceNode);
                } else { // rightNode가 null이 아닐때
                    returnValue = deleteEitherNode(parentNodeOfReplace, replaceNode);
                }
            }
        }
        return returnValue;
    }
    public void print() {
        ObjectNode.inorder(rootNode);
        System.out.println();
    }
}

```

