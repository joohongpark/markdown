# 스택 구현 및 스택 내의 데이터 정렬하기 (push_swap)

## 스택

스택은 데이터를 다루기 위한 추상적인 자료 구조이다. (이런 것들을 추상 자료형, Abstruct Data Type이라고 한다.) **스택** 이라는 말에서 드러나듯이 스택은 말 그대로 데이터를 쌓는 것을 의미한다.

데이터를 쌓게 되면 맨 밑의 데이터부터 한 층씩 데이터가 맨 위의 데이터까지 쌓여 있는 것을 연상할 수 있다. (실제로 데이터가 쌓여 있는 것은 아니고 쌓여 있는 것처럼 생각하는 것이다. 스택은 데이터를 잘 다루기 위한 추상화 자료형이라는 것을 상기하자.)

근데 사실 push_swap에서 "스택" 이란 자료구조를 구현하라고 강제하지 않는다. (이 과제의 "스택" 에 사용되는 연산이 스택보단 덱에 더 가깝다.)

## 덱 (deque)

덱은 큐와 스택을 섞어놓은듯한 특성을 가지고 있다. 큐의 양 단에서 push와 pop을 할 수 있다. 자세한 설명은 생략한다.

### 주요 연산

스택에는 아주 기본적인 연산 두 가지가 존재한다.

- PUSH : 스택에 데이터를 추가함. 데이터는 맨 위 데이터 위에 한 층이 쌓임.
- POP : 스택에서 데이터를 뽑아냄. 데이터는 맨 위 데이터가 뽑힘.

그 외 보조하기 위한 여러 연산들이 존재하나 저 두 가지 연산이 가장 핵심이다.

## push_swap

push_swap은 간단하고(연산이 적고) 효율이 좋은 (시간복잡도가 낮은) 정렬을 구현하는 과제이다.

push_swap에는 두 개의 프로그램을 만들어야 한다.

1. checker

   정수 인자들을 인수로 받아 표준 입력으로 정렬 연산 명령어를 읽는다.

   checker는 정수들이 정렬되었으면 OK를 출력하고 정렬되어있지 않으면 KO를 출력한다.

2. push_swap

   정수 인자들을 입수로 받아 가장 적은 수의 연산을 사용해 받은 정수 인자들을 정렬하는 정렬 연산 명령어를 출력한다.

push_swap은 이 과제에서 사용되는 스택 관련 전용 연산이 존재한다. 이 연산을 이용해서 스택을 구현하고 정렬하는 과제이다.

쉴게 설명하면 push_swap에서는 정렬되지 않은 숫자 배열을 받아 이를 스택화 하여 이를 정렬시킬 수 있는 최적의 알고리즘을 통해 주어진 스택 연산으로 정렬할 수 있도록 한다. 이 연산들은 표준 출력으로 출력한다.

checker는 인수로 숫자 배열을 받고 표준 입력으로 이를 정렬하는 스택 연산을 받아 이 연산대로 스택이 정렬이 되면 OK, 정렬이 되지 않으면 KO를 출력한다.

### push_swap의 스택 연산

push_swap에 사용되는 스택 연산(명령어)이 존재한다.

push_swap에서는 스택을 두 개 사용하며 각각 a, b라고 부른다. 또 a에는 정렬되지 않은 정수가 들어있고 b는 비어있다.

스택 연산들을 적절히 사용하여 a에 들어있는 원소들을 오름차 순으로 정렬한다.

- sa : swap a - a스택의 맨 위 (top)의 두 개의 원소의 위치를 바꾼다. 만약 스택에 1개 이하의 원소밖에 없다면 수행하지 않는다.
- sb : swap b - b스택의 맨 위 (top)의 두 개의 원소의 위치를 바꾼다. 만약 스택에 1개 이하의 원소밖에 없다면 수행하지 않는다.
- ss : sa와 sb를 동시에 수행한다.
- pa : push a - b의 맨 위 (top)의 하나의 원소를 pop 하여 a에 push한다. b에 원소가 없다면 수행하지 않는다.
- pb : push b - a의 맨 위 (top)의 하나의 원소를 pop 하여 b에 push한다. b에 원소가 없다면 수행하지 않는다.
- ra : rotate a - a의 맨 위 (top)에 있는 하나의 원소를 맨 밑 (bottom) 으로 집어넣는다.
- rb : rotateb - b의 맨 위 (top)에 있는 하나의 원소를 맨 밑 (bottom) 으로 집어넣는다.
- rr : ra와 rb를 동시에 실행한다.
- rra : reverse rotate a - a의 맨 밑 (bottom)에 있는 하나의 원소를 맨 위 (top)로 집어넣는다.
- rrb : reverse rotate b - b의 맨 밑 (bottom)에 있는 하나의 원소를 맨 위 (top)로 집어넣는다.
- rrr : rra와 rrb를 동시에 실행한다.

## push_swap 과제 해결 흐름

개인차가 있겠지만 나는 다음과 같이 전략을 세웠다.

1. 스택 구현과 스택 연산 구현
2. checker 프로그램 구현
3. push_swap 구현
   1. 정렬 알고리즘 구현

가장 먼저 스택 및 관련 연산부터 구현해본다.

## 스택/연산 구현

### 스택

스택을 구현할 수 있는 방식은 여러 가지가 있다. 배열에 주욱 나열해도 되고 링크드 리스트를 사용해도 된다. 배열을 사용하면 링크드 리스트를 구현할 필요가 없는 장점이 있지만 배열 크기를 동적으로 관리하여야 하며 push/pop 연산 시 어느 부분을 가리키는지 기록을 해 놓아야 한다. 또 단순히 메모리에 나열하는 식으로 구현하고 push/pop 연산을 반복하면 메모리 참조 인덱스가 올라가 오류가 날 수 있다.

따라서 단순히 배열을 통해 구현할 거면 이런 부분에 대한 구현이 필요하다. (이런 경우 보통 원형 큐 형태로로 구현한다.)

하지만 링크드 리스트를 이용하면 push/pop 할 때에만 그 원소에 대해서 동적으로 할당/해제하면 되고 이전에 링크드 리스트를 구현한 과제를 가져다 사용해도 되기 때문에 링크드 리스트로 구현한다.

### 스택 연산

스택 연산 중 스택에 없는 (스택 기본 연산으로 구현할 수 없는) rotate 연산이 있다. 이 연산은 맨 밑에 있는 원소를 뽑아야 하는데 push/pop 만으로만 구현하려면 불필요한 연산을 많이 하게 되므로 이 부분에 대해선 queue 형태로 구현한다. 사실 위에서 말했듯이 여기서 사용하는 자료구조는 스택보단 덱에 더 가깝다.

## 과제 수행

### Makefile

일단 Makefile부터 작성해본다. make 결과는 바이너리가 두 개 생성되어야 하며 공용으로 사용하는 소스코드, checker/push_swap 각각에 대해서만 사용되는 소스코드가 있을 것이다. 이것들을 따로따로 구분지어서 Makefile을 작성해야 한다.

### ADT

구현에 사용되는 자료구조를 구현한다. 먼저 덱 (계속 말하듯이 여기서 사용되는 연산 중 rotate 라는 연산은 스택을 쓰는것보다 덱을 쓰는게 좋다. 덱은 큐처럼 파이프마냥 앞뒤가 뚫려있음)의 연산에 대한 함수를 구현한다.

push/pop/peek 동작을 함수로 구현해본다.

- push_front
- pop_front
- peek_front
- push_back
- pop_back
- peek_back

### push_swap 연산 구현

위 연산자 함수들을 이용해 연산 (명령어)에 따라 스택에 위 연산을 하는 함수를 만든다.

hint : strncmp와 위 함수 이용해서 하나의 함수 내에서 연산을 할 수 있도록 한다. 근데 이렇게 구현할 필요는 없고 난 이렇게 구현했다.

### push_swap

push_swap을 구현해야 한다. 스택 내부에 있는 원소들을 어떠한 방법으로 정렬하던 현재 가장 문제가 되는것은

- push_swap에서 제공하는 연산만 사용해야 하는 것
- 시간복잡도를 가능한 한 최소한으로 하는것

이다.

관련 알고리즘이 이미 정립되어 있을 수도 있으므로 구글 등에 관련 키워드(how to sort elements in stack) 를 이용해서 찾아본다.

하지만 찾아본 일반적인 "스택" 을 사용하는 알고리즘은 모두 시간복잡도가 O(N*N) 이기 때문에 과제에서 요구하는 시간복잡도를 채우지 못할 것이다.

그래서 정렬 알고리즘 중 널리 사용되며 시간복잡도가 좋은 편인 알고리즘들을 고려해보기로 했다.

일단 선택정렬, 버블정렬은 사용하지 않기로 했다. 시간복잡도가 너무 높다.

여러 정렬을 고려하던 중 슬랙 등을 참조해본 결과 퀵 정렬을 사용하는 분들이 많은 것으로 보였다. 따라서 퀵 정렬을 고려해 보았다.

퀵 정렬의 특징은

- pivot을 기준으로 pivot보다 작은 그룹, 큰 그룹으로 나눈다.
- 작은 그룹과 큰 그룹에 대해 재귀적으로 정렬을 수행한다.

가 있었다. 직관적으로 생각해 보기에 마침 해당 과제에서 사용되는 저장소도 두가지이며 특정 기준에 대해 작은 혹은 큰 원소들을 다른 배열에다 집어넣어놓고 정렬 알고리즘을 재귀적으로 수행하면 될 수도 있을것 같다는 영감을 얻었다.

또 pivot을 기준으로 순회하며 나누는 데 해당 과제에서 사용되는 rotate 연산을 사용하면 좋을 수도 있을 것 같다는 생각을 하였다.

## quick sort -> pivot

잠정적으로 quick sort를 사용해 보기로 했다. 임의의 pivot을 사용해서 그룹을 나누는 동작에 대한 테스트를 해보기로 했다.

먼저 pivot을 나누는 함수를 만들어 보았다.

만들어 보고 push_swap이 잘 나누는가 테스트를 `https://github.com/o-reo/push_swap_visualizer` 를 이용해서 확인해 보았다.

일단 pivot 나누는 것을 a 내부에 정렬이 될 때 까지 진행한다. 정렬이 되어 있지 않으면 계속 pivot을 나누며 정렬이 되어 있으면 pivot을 아예 나누지 않는다.

여기서 발견한 문제는 이렇게 quick sort를 이용해 정렬하면 역으로 정렬하는 과정에서 시간복잡도가 엄청나게 높아지는 단점이 있다. 여러 생각을 해 보아야 하겠지만 균일하게 하기 위해 처음 정렬을 시작하기 전에 rotate 연산을 전체 스택 크기의 반 정도를 반복하면 어느 정도 pivot 횟수가 줄어드는 것을 확인하였다.

하지만 이것은 바로 적용하지 않을 것이고 기능 구현이 된 상태에서 특정 상황에서 시간복잡도를 줄이는 해법으로 사용해 볼 생각이다.

### b 스택에 쌓여있는 데이터

데이터가 어느정도 길고 데이터가 무작위로 정렬되어 있다면 여러 개의 pivot으로 나누어진 b 스택이 위 결과물로 나올 것이다. 이 스택을 어떻게 정렬하느냐가 문제이다.

이 때엔 케이스를 나눠서 생각해 보았다.

현재 p1은 a에, p2는 원소들 사이에 b에 위치한다.

1. 만약 피봇 p1과 p2 사이에 원소가 없다면?

   이 경우는 원소이자 피봇 p1과 p2 사이 원소 값이 존재하지 않는 상황이다. 만약 처음에 a의 pivot을 잘라내는 과정을 수행하였다면 a엔 원소들이 맞는 순서대로 정렬되어 있는 상황이므로 그대로 p2를 a로 옮긴다. (pa 1회)

2. 만약 p1과 p2 사이에 원소가 e 하나만 있다면?

   이 경우도 p1 >  e  >  p2 이므로 그대로 a에 push 하면 된다. (pa 2회)

3. 만약 p1과 p2 사이에 원소가 e1, e2 두개 있다면? (p1-e1-e2-p2)

   b 스택에서 e1과 e2의 대소관계를 먼저 파악한다. 만약 b 스택 내에서 e1 > e2 라면 옳게 정렬되어 있으므로 그대로 a에 push 하면 되고 이런 상황에서는 pa를 3회 수행한다.

   만약 e1 < e2 라면 먼저 p1을 뽑아야 하므로 pa 1회 수행 후 swap b를 1회 수행하고 pa를 2회 수행한다.

4. p1과 p2 사이에 원소가 3개 이상일 경우

   이 경우엔 p2와 가까운 원소 en을 찾는다. 찾는 도중 원소들이 역정렬 되어 있으면 원소 개수만큼 그대로 pa를 수행하지만 그렇지 않다면? pivot을 나누어야 한다.

