# 스택 구현 및 스택 내의 데이터 정렬하기 (push_swap)

## 스택

스택은 데이터를 다루기 위한 추상적인 자료 구조이다. (이런 것들을 추상 자료형, Abstruct Data Type이라고 한다.) **스택** 이라는 말에서 드러나듯이 스택은 말 그대로 데이터를 쌓는 것을 의미한다.

데이터를 쌓게 되면 맨 밑의 데이터부터 한 층씩 데이터가 맨 위의 데이터까지 쌓여 있는 것을 연상할 수 있다. (실제로 데이터가 쌓여 있는 것은 아니고 쌓여 있는 것처럼 생각하는 것이다. 스택은 데이터를 잘 다루기 위한 추상화 자료형이라는 것을 상기하자.)

근데 사실 push_swap에서 "스택" 이란 자료구조를 구현하라고 강제하지 않는다. (이 과제의 "스택" 에 사용되는 연산이 스택보단 덱에 더 가깝다.)

## 덱 (deque)

덱은 큐와 스택을 섞어놓은듯한 특성을 가지고 있다. 큐의 양 단에서 push와 pop을 할 수 있다. 자세한 설명은 생략한다.

## push_swap

push_swap은 간단하고(연산이 적고) 효율이 좋은 (시간복잡도가 낮은) 정렬을 구현하는 과제이다.

push_swap에는 두 개의 프로그램을 만들어야 한다.

1. checker

   정수 인자들을 인수로 받아 표준 입력으로 정렬 연산 명령어를 읽는다.

   checker는 정수들이 정렬되었으면 OK를 출력하고 정렬되어있지 않으면 KO를 출력한다.

2. push_swap

   정수 인자들을 입수로 받아 가장 적은 수의 연산 명령어들을 사용해 받은 정수 인자들을 정렬하는 정렬 연산 명령어를 표준 출력으로 출력한다.

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

여기서 발견한 문제는 이렇게 quick sort를 이용해 정렬하면 역으로 정렬하는 과정에서 시간복잡도가 엄청나게 높아지는 단점이 있다. ~~여러 생각을 해 보아야 하겠지만 균일하게 하기 위해 처음 정렬을 시작하기 전에 rotate 연산을 전체 스택 크기의 반 정도를 반복하면 어느 정도 pivot 횟수가 줄어드는 것을 확인하였다.~~

## 시행착오 끝에 quick sort 기본 기능 구현

약 6일간의 시행착오 끝에 quick sort 기반으로 정렬이 되도록 구현하였다.

시간이 이렇게 오래 걸린 이유는

- deque / list 간 데이터의 흐름 파악 혼동
- 변수명을 이상하게 지음
- 정렬 방향 혼동
- 기존에 정렬 알고리즘을 너무 간략하게 짜놓고 코드로 구현
- 디버깅을 전혀 고려하지 않은 코딩

등이 있었다. 쉬운 작업은 아니지만 구현한 기능에 비해 너무 많은 시간을 소비했다.

일단 데이터를 처리하는 알고리즘을 짜는 것 치고 list -> deque -> stack으로 이어지는 ADT 구현을 너무 엄밀하게 하지 못했다. (정확히는 변수명 명명, 데이터 입/출력 등을 혼동되게 설계하여 기능 구현에 혼동이 있었다. 예를 들어 push와 pop 연산을 헷갈린다던지...)

또 기능 구현을 하기에 슈도코드도 없었으며 너무 직감적으로 즉석으로 기능 구현을 하느냐 코드도 엄청 길어졌다. 즉석에서 기능 구현을 하면 디버깅이 힘들다.

안그래도 디버깅이 힘든 상황에서 디버깅을 전혀 고려하지 않으며 코딩하였다. lldb로 내부 변수라도 체크를 해가며 디버깅한것도 아니였으며 단순히 잘 될 것이랑 근거없는 믿음으로 로그를 찍는 코드 하나 입력하지 않았었다.

이 이유들이 5~6일씩이나 투자하여 quick sort를 구현해 낸 비결이다. 나처럼 비효율적으로 코딩하지 않길 바란다.

현재 코드의 흐름 (슈도코드)은 다음과 같다.

> a 스택이 정렬될 때 까지 b 스택으로 pivot 나누기
>
> pivot의 리스트와 a 스택과 b 스택 pivot 값 사이의 pivot이 null이 아닐 때 까지 밑 작업 반복
>
> > a 스택의 pivot 위로 원소가 존재하지 않으면 b 스택을 나누고, pivot 리스트의 유뮤에 따라 특정 범위만큼 스택 b pivot 나누기
> >
> > 원소가 하나 존재하면, 크기를 비교해 상황에 따라 swap
> >
> > 하나 이상 존재하면 pivot ~ top 까지 pivot을 나누며 새 pivot 값을 a 스택과 b 스택 pivot 값 사이의 pivot 리스트에 넣음

작성하기 힘들어 엄밀하게 작성하지 않았으며, 그냥 단순하게 메인 Pivot과 서브 Pivot을 나눠서 정렬 작업을 하는 알고리즘이다.

이정도만 해도 충분히 빠른 속도이지만 push_swap에서는 아마도 이보다 빠른 속도를 요구할 것이다.

여기서 속도 loss가 발생할 수 있는 부분을 생각해 보자.

- 만약 pivot 작업을 하는 구간이 정/역으로 정렬되어 있으면 pivot을 나눌 필요가 없음.
  - 이 부분에서 실재로 loss가 발생하는지 체크 필요
- pivot을 하는 구간 내의 원소 개수에 따라 정렬을 간단하게 처리할 수 있음.
  - 예를 들어 a 스택 top에 2 - 1 - 3 순서로 들어있고 a 스택의 pivot이 3이면 pivot을 나눌 때 다음과 같이 동작한다.
    - pb - pb
    - 스택 b에서 top은 1이 됨. 다시 pivot
    - rb
    - 다시 스택 a에 대해 동작하는데 이 경우는 stack a에 대한 pivot이 2로 바뀜
    - 다시 스택 b로 돌아가서 정렬 수행...
  - 상세하게 적은 것은 아니지만 위와 같은 상황에서는 단순히 스택 a에 대해 sa 한번만 수행하면 정렬되는 상황이다.
- 전처리를 이용한 정렬 최적화
  - 전처리를 하는 방법은 여러가지가 있겠지만 가장 대표적인 케이스는 역으로 정렬되어 있을 때다. 이 경우엔 quick sort는 최악의 정렬속도가 나오는데 단순히 원소 순서만 정방향으로 바꾼다면 quick sort를 할 필요가 없어진다.
  - 전처리 케이스를 추가하여 정렬 속도를 줄인다.

현재 생각나는 부분은 위 세가지인데 저 세가지를 한번 수정해 본다.

## 추가적인 최적화 방법

### quick sort의 pivot

quick sort는 분할 과정에서 pivot을 기준으로 pivot보다 크고 작은 소 그룹으로 나누게 되는데, 여기서 pivot을 어떤 것으로 잡느냐에 따라 quick sort의 정렬 속도가 크게 차이가 나게 된다. 그래서 pivot을 소 그룹에서 한쪽에 치우쳐진 값으로 정할 때 정렬 대상이 역정렬 되어 있으면 O(N2)의 시간복잡도가 나오게 된다.

그러면 어떤 값을 pivot으로 정해야 정렬이 가장 빠르게 될까? 이상적으로는 소 그룹 내 원소들의 중간값을 pivot으로 정해야 가장 이상적이다. 하지만 이런 경우 정렬을 위한 정렬을 해야 한다는 부분이 문제가 될 수도 있다.

하지만 push_swap의 목적을 잘 생각해봐야 한다. push_swap 내부에서 어떤 일이 벌어지는지 알 필요는 없다. push_swap 내부에서 구동되는 속도는 크게 중요하지 않고 주어진 배열이 어떤 연산들을 사용해야 잘 정렬되는지 출력해주는 프로그램이다.

여기서 일반 정렬 알고리즘과 다른 부분이 생긴다. push_swap은 결과적으로는 정렬을 위한 정렬을 해도 된다.

### 수동으로 pivot 정하기

#### 초기 a 스택 pivot 정렬 시 직접 pivot 값 정하기

본격적으로 a스택과 b스택을 번갈아가며 pivot을 나누기 이전 a 스택을 나눌 때 현재는 bottom에 있는 값을 pivot으로 삼아서 한다. 그래서 bottom 값이 적절히 중간값이 아닐 때 느려지게 되는 현상이 발생했다.

그래서 실험을 해 보았다. 랜덤하게 100까지의 수열을 생성 할 때 연산 횟수가 1348까지 발생하는 수열은 바닥값이 가장 작음 -> 가장 큼 순서로 배열되어 있었다. 따라서 배열 전체의 순회를 많이 돌 수 밖에 없어 보였다.

동일한 수열의 바닥값을 bottom -> top 순서로 50/75/87/94/97 으로 변경한 후 loop를 다시 돌려 보면 무려 1013이란 값으로 감소했다. 무려 300번의 연산 횟수를 줄일 수 있는 것이다.

어차피 정렬 자체는 완전히 실시간으로 할 필요가 없으므로 (push_swap은 가장 적은 "연산"을 이용해 정렬을 하도록 "계획"을 세우는 과제이다.) 인수로 주어진 배열을 먼저 적절한 정렬 알고리즘을 통해 정렬한 후 정렬된 값과 인덱스를 통해 pivot을 수동으로 지정하기로 했다.

기존엔 원소 개수가 500개일 때 평균 8000대부터 최대 12000까지 증가하는 현상이 있었지만 초기에 a 스택을 정렬할 때 해당 방법으로 정렬 테스트를 해 보니 6000 ~ 9000번으로 확연히 감소하였다.

### pivot 값을 어떻게 정하는가?

위에서 적절한 정렬 알고리즘을 사용한다고 했다. 정렬하고자 하는 그룹에서 정렬을 한 다음 전체 index의 중간값을 가지는 index가 중간값이다. 어떤 알고리즘을 사용해도 상관 없겠으나 나는 동일하게 quick sort 알고리즘을 택했다.

### push_swap 최적화

처음엔 단순히 기능 구현을 위해 코드가 비효율적으로 짜져있다. 중복된 부분을 제거해 가면서 pivot을 선택하는 부분을 모두 중간값을 고르는 식으로 변경하였다. 이릏게 모두 바꾸니 정렬 연산 횟수가 균일하게 5000 ~ 6000번대로 감소하였다.

### 소 그룹이 3개일 때 최적화

소그룹이 2개일 때를 제외하고 모두 pivot을 나누도록 되어 있다. 하지만 그룹이 3개일 때는 궂이 pivot을 나누지 않고 적은 연산으로 정렬을 할 수 있는 방법이 정해져 있다. 이 솔루션을 적용하니 5000번대 초반이 나오게 되었다.

### 명령어 최적화

현재는 명령어들을 모두 즉시 표준출력에 출력하도록 되어 있다. 이 명령어들은 "압축"을 시킬 수 있다. 또 불필요한 명령어들이 끼어 있기도 하다. 이런 것들을 모두 제거하면 4000번대 후반까지 진입할 수 있다.

명령어들을 즉시 표준출력에 출력하지 않고 문자열 그대로 버퍼에 담아두거나 linked list 등으로 명령어 단위로 저장해서 명령어들을 압축시킨다.

- 예를 들어 ra -> rb / rb -> ra 의 명령어는 rr로 압축시킬 수 있다.
- 또 rra -> rrb / rrb -> rra 의 명령어는 rrr로 압축시킬 수 있다.
- rr -> rrr / rrr -> rr 순으로 실행되는 명령어는 불필요하므로 삭제시킬 수 있다.
- 또 pa -> pb / pb -> pa / ra -> rra / rb -> rrb / sa -> sa / sb -> sb 와 같이 실행하나 마나 동일한 명령어 쌍은 삭제시킬 수 있다.