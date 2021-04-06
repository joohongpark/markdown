# 6. 상호 배제와 솔루션

이전에서 다룬 상호 배제에 대해 예제와 자세히 확인해 보자.

근본적으로 경쟁 상태에 있는 코드는 상호 배제만을 해결하면 안된다. (그에 대한 자세한건 다음 장에) 일단 앞서 얘기했던 주로 사용하는 세 가지 방법에 대해 확인해 보자.

## 1. Mutex Lock

락 혹은 뮤텍스라고 부른다. 상호 배제를 구현하기 위한 가장 기본적인 방법이다.

가장 쉽고 간단하게 사용할 수 있는 방법이다. 임계 영역을 다른 스레드가 실행 중이라면 접근하지 못하도록 계속 대기하다가 그 스레드가 임계 영역을 벗어나면 접근하도록 해 주는 것이다.

실생활에서의 비유를 들자면 한 가정에 여려명이 살고 있는데 화장실이 하나만 있고 화장실이 사용 중일 때에만 문을 걸어 잠그는 것에 비유 할 수 있다.

하지만 이런 상황 특성 상 임계 구역을 하나만 사용하는 상황에 대해 정할 수 밖에 없다. (집에 여러개의 화장실이 있는 경우는 생각하지 않는 것이다.)

멀티스레드를 사용할 수 있는 대부분의 언어들이 뮤텍스 락을 키워드나 라이브러리 형태로 지원해 준다.

보통 lock/unlock 두 개의 함수로 Lock/Unlock을 한다.

### 장점과 단점

사용하기도 쉽고 구현하기도 쉽고 생각하기도 쉽다. 하지만 여러 스레드에 대해 동일한 임계 영역으로 묶여 있을 때 어느 한 스레드에서 계속해서 점유하고 있을 때 다른 스레드는 접근하지 못한다. 또 교착 상태가 발생하게 될 때 디버깅이 힘든 편이다.

### C에서의 예제

```c
# gcc -lpthread main.c
#include <stdio.h>
#include <pthread.h>

int g_var = 0;
pthread_mutex_t mutex;

void *run_inc(void *arg)
{
	for (int i = 0; i < 1000; i++)
	{
    // LOCK (어떤 스레드도 진입하지 않았으면 진입함)
		pthread_mutex_lock(&mutex);
		g_var++;
		(*(int *)arg)++;
    // UNLOCK (다른 스레드에서 진입할 수 있도록 UNLOCK함)
		pthread_mutex_unlock(&mutex);
	}
	pthread_exit(0);
}

int main(int argc, char *argv[])
{
  // 스레드 10개 사용
	pthread_t threads[10];
	int main_var;

	main_var = 0;
  // 뮤텍스 변수 초기화
	pthread_mutex_init(&mutex, NULL);
	for (int i = 0; i < 10; i++)
	{
    // 스레드 생성
		pthread_create(&threads[i], NULL, run_inc, (void *)&main_var);
	}
	for (int i = 0; i < 10; i++)
	{
    // 스레드 반환
		pthread_join(threads[i], NULL);
	}
	printf("g_var : %d\n", g_var);
	printf("main_var : %d\n", main_var);
	return (0);
}
```

위 코드에서 pthread_mutex_t 타입의 변수를 사용하는 함수들을 넣었다 뺏다 하며 실행결과를 비교해 보자. 정상적인 상황은 10000이 출력되는 것이다.

당연하겠지만 위 lock/unlock 함수는 원자적 함수로 구현되어 있을 것이다.

## 2. Semaphore

세마포어의 어원은 철도의 신호기에서 나왔으며 철도의 길이 교차가 될 때 열차의 진입을 통제하는 역할을 한다. 여기서의 세마포어도 마찬가지로 교차가 되는 임계 구역에 대해 프로세스나 스레드가 진입해도 되는지, 진입하면 안되는지 알려준다. 위 락과 비슷하게 진입하지 않으면 기다리고 있는다.

세마포어 값은 정수로 표현되며 세마포어 제어 함수들이 있다. 세마포어를 지원하는 언어마다 명칭이 다르겠지만 보통 두개는 반드시 존재한다.

- Proberen

  네덜란드어로 "시험하다" 라는 의미가 있다. 세마포어라는 개념을 만든 사람이 네덜란드 사람이라 네덜란드 단어를 사용한다. 영어로 하면 try일 것이다.

  해당 함수를 입장 구역에 삽입하는데 세마포어 정수값이 0이라면 대기하고 있으며 0이 아니라면 세마포어를 1 감소시키고 임계 구역에 진입한다.

- Verhogen

  네덜란드어로 "증가" 라는 의미가 있다. 해당 함수를 퇴장 구역에 삽입하여 임계 구역을 나갈 때 세마포어를 1 증가시킨다.

만약에 세마포어 값이 1이라면 이건 바이너리(이진) 세마포어라고 부르며 뮤텍스 락과 동일하게 동작한다. 카운팅 세마포어는 사용 가능한 자원이 여러개일 때 사용할 수 있다.

당연히 위의 두 함수는 원자성을 만족해야 한다.

### 장점과 단점

단순히 뮤텍스 락을 하는 것보단 다양칸 케이스에 대해 대응할 수 있으며 스레드의 상호 배제를 적용하는 뮤텍스 락에 비해 세마포어는 보통 프로세스-프로세스 사이의 상호 배제를 적용할 수 있다. 하지만 뮤텍스 락과 마찬가지로 디버깅이 쉽지 않으며 교착 상태가 발생할 수 있다.

### C에서의 예제

```c
# gcc -lpthread main.c
#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>

int g_var = 0;
sem_t *semaphore;

void *run_inc(void *arg)
{
	for (int i = 0; i < 1000; i++)
	{
    // P(S)
		sem_wait(semaphore);
		g_var++;
		(*(int *)arg)++;
    // V(S)
		sem_post(semaphore);
	}
	pthread_exit(0);
}

int main(int argc, char *argv[])
{
	pthread_t threads[10];
	int main_var;

	main_var = 0;
  // 세마포어 초기화 (이진 세마포어)
	semaphore = sem_open("sem_test", O_CREAT, 0644, 1); 
	for (int i = 0; i < 10; i++)
	{
    // 스레드 생성
		pthread_create(&threads[i], NULL, run_inc, (void *)&main_var);
	}
	for (int i = 0; i < 10; i++)
	{
    // 스레드 반환
		pthread_join(threads[i], NULL);
	}
	printf("g_var : %d\n", g_var);
	printf("main_var : %d\n", main_var);
	sem_close(semaphore);
	return (0);
}
```

이진 세마포어를 적용해 위 예제를 세마포어로 적용한 예제이다. 세마포어 초기화 함수 sem_open은 다른 프로세스에서도 접근할 수 있도록 공유되는 메모리 영역 "sem_test" 에 대해 접근하는 것이다.

## 3. Monitor

모니터는 뮤텍스와 비슷하게 상호 배제를 적용할 때 사용된다. 모니터는 일종의 추상적인 자료구조로 내부에 배타 동기와 조건 동기를 위한 큐가 내장되어 있으며 이 큐에 실행할 스레드가 내장되어 있고 한번에 하나의 스레드만 구동되도록 한다.

보통 언어/프레임워크 차원에서 지원해주거나 하며 고급 언어에서는 내장되어 있는 경우가 많다.

C에서는 자체적으로 지원해 주지 않으며 자바에서는 synchronized 라는 키워드로 모니터를 지원해 준다.

### 장점과 단점

세마포어와 모니터는 다른 개념이다. 세마포어는 사용 가능한 자원을 카운트 해서 사용 가능한 자원이 있으면 스레드들이 사용하게 해 주는 것이며 모니터는 지정한 부분에 대해 상호 배제를 적용하게 해 주는 것이다. 모니터는 한 순간에 하나의 스레드에서만 동작될 수 있다.

### 자바에서의 예제

```java
public class Main {
    private static Object object = new Object();
    private static int val1 = 0;
    private static int val2 = 0;
    private static int val3 = 0;

    synchronized public static void addsafe1() {
        val1 = val1 + 1;
    }

    public static void addsafe2() {
        synchronized (object) {
            val2 = val2 + 1;
        }
    }

    public static void addsafe3() {
        val3 = val3 + 1;
    }

    public static void main(String[] args) throws InterruptedException {
        Runnable test1 = () -> {
            for (int i = 0; i < 10000; i++)
                addsafe1();
        };
        Runnable test2 = () -> {
            for (int i = 0; i < 10000; i++)
                addsafe2();
        };
        Runnable test3 = () -> {
            for (int i = 0; i < 10000; i++)
                addsafe3();
        };
        Thread[] add1 = new Thread[10];
        Thread[] add2 = new Thread[10];
        Thread[] add3 = new Thread[10];
        for (int i = 0; i < 10; i++)
        {
            add1[i] = new Thread(test1);
            add2[i] = new Thread(test2);
            add3[i] = new Thread(test3);
            add1[i].start();
            add2[i].start();
            add3[i].start();
        }
        for (int i = 0; i < 10; i++)
        {
            add1[i].join();
            add2[i].join();
            add3[i].join();
        }
        System.out.println("val1 : " + val1);
        System.out.println("val2 : " + val2);
        System.out.println("val3 : " + val3);
    }
}
```

addsafe 1~3 을 이용해서 더하는 스레드 10개씩을 만들어 테스트를 하는 코드다. addsafe3은 아무런 상호배제를 적용하지 않은 메소드이며 addsafe1, addsafe2는 모니터를 적용한 예제이다.

synchronized 키워드는 메소드 전체에 적용하거나 원하는 블록에 대해서만 적용할 수 있다. 그리고 설명은 생략하지만 synchronized 키워드와 static 한 메소드에 대해 잘 생각해 보아야 한다.