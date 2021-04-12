# 7. 동기화 관련 발생할 수 있는 문제와 해결

공유 자원이 동기화가 되지 않으면 동시성 문제가 발생할 수 있다. 이이전에서 말한 상호 배제와 교착 방지, 기아 방지를 통해 해결해야 한다. 하지만 상호 배제에 대한 기법만 언급하였다. 실제로 동기화 관련 문제들이 발생하는 상황을 보자.

## 생산자-소비자 문제 (Producer-Consumer Problem)

한정-버퍼 문제 (Bounded-Buffer Problem) 라고도 한다.

생산자 프로세스와 소비자 프로세스가 있고 데이터가 생산자 -> 소비자로 흐른다 생각해 보자. 소비자가 메시지 큐나 메모리 영역에 접근하여 데이터 (메시지)를 가져오고 생산자는 그 영역에 데이터를 집어 넣는다. 이런 경우 소비자가 가져올 데이터가 없거나 생산자가 데이터가 넣을 자리가 없을 수 있다. 이런 문제를 생산자-소비자 문제라고 한다.

생산자와 소비자를 적절한 규칙을 통해 동기화를 하여 원활하게 통신하도록 해결해야 한다. 예를 들면 소비자는 데이터 공간이 비어있지 않을 때 까지 접근하며 생산자는 남은 공간이 있을 때에만 접근한다.

이 문제는 프로세스 IPC에선 메모리를 공유하는 상황에서 발생하는 문제이다. 스레드 간에도 하나의 스레드가 생산, 다른 하나의 스레드가 소비를 하는 입장이면 발생할 수 있다.

### 해결 방안

뮤텍스 1개와 세마포어 2개를 사용한다. 뮤텍스는 생산 혹은 소비하는 임계 영역에 삽입하며 새마포어 두개는 버퍼 내부에 삽입 가능한 사이즈와 현재 버퍼 내부 크기 (0)를 삽입한다.

### C 코드 예제

```c
#include <stdio.h>
#include <signal.h>
#include <stdlib.h>
#include <unistd.h>
#include <pthread.h>
#include <semaphore.h>

int g_data[20];
int g_data_pointer = 0;

sem_t *s_mutex;
sem_t *s_empty;
sem_t *s_full;

void push_data(int data)
{
	sem_wait(s_empty);
	sem_wait(s_mutex);
	g_data[g_data_pointer] = data;
	g_data_pointer++;
	sem_post(s_mutex);
	sem_post(s_full);
}

int pop_data(void)
{
	int	rtn;
	sem_wait(s_full);
	sem_wait(s_mutex);
	g_data_pointer--;
	rtn = g_data[g_data_pointer];
	sem_post(s_mutex);
	sem_post(s_empty);
	return (rtn);
}

void *print_buf(void *arg)
{
	int tmp;
	while (1)
	{
		usleep(500 * 1000);
		tmp = pop_data();
		printf("data : %d\n", tmp);
	}
	pthread_exit(0);
}

void *insert_buf(void *arg)
{
	for (int i = 0; i < 40; i++)
	{
		printf("Insert : %d\n", i);
		push_data(i);
		//usleep(100 * 1000);
	}
	pthread_exit(0);
}

void ctrl_c_key(int code)
{
	sem_close(s_mutex);
	sem_close(s_empty);
	sem_close(s_full);
	sem_unlink("sem_mutex");
	sem_unlink("sem_empty");
	sem_unlink("sem_full");
	exit(0);
}

int main(int argc, char *argv[])
{
	pthread_t gen;
	pthread_t use;
	int main_var;

	signal(SIGINT, ctrl_c_key);
	main_var = 0;
	s_mutex = sem_open("sem_mutex", O_CREAT, S_IRWXU, 1);
	s_empty = sem_open("sem_empty", O_CREAT, S_IRWXU, 20);
	s_full = sem_open("sem_full", O_CREAT, S_IRWXU, 0);
	pthread_create(&gen, NULL, insert_buf, NULL);
	pthread_create(&use, NULL, print_buf, NULL);
	pthread_join(gen, NULL);
	pthread_join(use, NULL);
	return (0);
}

```

현재 사용중인 환경 (Mac OS 빅서)에선 semaphore 라이브러리에 현재 프로세스 내에서만 유효한 세마포어가 deprecated 되어서 프로세스 끼리 공유 가능한 형태로 세마포어를 만들어야 한다. 그래서 종료할 때 반드시 사용한 세마포어를 unlink 하여야 이 프로그램을 다시 실행할 때 세마포어가 초기화 된다.

## 독자 - 저자 문제 (Readers-Writers Problem)

한 데이터베이스 (큐, 스택 등 어떤것이든 데이터에 관한 것이라면 무엇이든)에 여러 사용자가 (이 사용자는 스레드, 프로세스를 포함하는 개념이다) 접근할 때 문제이다.

사용자는 데이터에 접근해서 데이터를 작성(수정)하는 사람(저자)과 데이터를 읽기만 하는 사람(독자) 두 가지로 나눌 수 있을 것이다.

### 해결 방안

상황을 몇 가지로 나누어 보자.

- 아무런 저자도 데이터에 접근하지 않을 때

  - 독자 1명 혹은 여러 명이 데이터에 접근할 때

    독자끼리는 (저자가 데이터에 접근하지 않는다면) 상호간에 상호 배제는 불필요하다. 독자가 동시에 얼마나 접근하던 데이터는 불변하므로 상관 없다.

- 저자 한 명이 데이터에 접근할 때

  - 독자 1명 혹은 여러 명이 접근할 때

    저자가 데이터를 접근할 때에는 독자가 데이터에 접근해서는 안된다. 한 순간에 대해 독자 혹은 저자만 접근해야 한다. 독자와 저자 간은 상호 배제가 적용되어야 한다.

- 저자 여러 명이 데이터에 접근할 때

  저자끼리도 서로 동시에 접근하면 안된다. 저자끼리도 상호 배제를 적용하여야 한다.

독자 - 저자 간 상호 배제가 필요하고 저자 - 저자 간 상호 배제가 필요하다, 그래서 뮤텍스 두개를 사용하면 된다.

뮤텍스는 데이터에 접근해서 수정할 수 있는 뮤텍스와 현재 독자 수를 나타내는 카운터 변수에 대한 뮤텍스 두 개를 사용한다.

```c
#include <stdio.h>
#include <signal.h>
#include <stdlib.h>
#include <unistd.h>
#include <pthread.h>

int g_data = 0;

pthread_mutex_t mutex_rw;
pthread_mutex_t mutex_reader_counter;
int reader_counter = 0;

void writer(int data)
{
	g_data = data;
}

int reader(void)
{
	int	rtn;
	rtn = g_data;
	return (rtn);
}

void *writer_thread(void *thread_num)
{
	int tmp;
	int num;
	num = *((int *)thread_num);
	while (1)
	{
		usleep(500 * 1000);

		pthread_mutex_lock(&mutex_rw);

		tmp = rand();
		printf("[thread %d] Insert : %d\n", num, tmp);
		writer(tmp);

		pthread_mutex_unlock(&mutex_rw);
	}
	pthread_exit(0);
}

void *reader_thread(void *thread_num)
{
	int tmp;
	int num;
	num = *((int *)thread_num);
	while (1)
	{
		usleep(500 * 1000);

		pthread_mutex_lock(&mutex_reader_counter);
		reader_counter++;
		if (reader_counter == 1)
			pthread_mutex_lock(&mutex_rw);
		pthread_mutex_unlock(&mutex_reader_counter);

		tmp = reader();
		printf("[thread %d] data : %d\n", num, tmp);

		pthread_mutex_lock(&mutex_reader_counter);
		reader_counter--;
		if (reader_counter == 0)
			pthread_mutex_unlock(&mutex_rw);
		pthread_mutex_unlock(&mutex_reader_counter);
	}
	pthread_exit(0);
}

int main(int argc, char *argv[])
{
	pthread_t readers[10];
	pthread_t writers[2];
	int numbers[10] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
	int main_var;

	main_var = 0;
	pthread_mutex_init(&mutex_rw, NULL);
	pthread_mutex_init(&mutex_reader_counter, NULL);
	for (int i = 0; i < 2; i++)
		pthread_create(&writers[i], NULL, writer_thread, (void *)&numbers[i]);
	for (int i = 0; i < 10; i++)
		pthread_create(&readers[i], NULL, reader_thread, (void *)&numbers[i]);
	for (int i = 0; i < 2; i++)
		pthread_join(writers[i], NULL);
	for (int i = 0; i < 10; i++)
		pthread_join(readers[i], NULL);
	return (0);
}
```

위에서 mutex_rw는 두 가지 역할을 한다.

1. 저자 여러 명이 한번에 접근하지 못하도록 하는 것
2. 독자가 1명 이상일 때 저자가 접근하지 못하도록 하는 것

1번의 경우에는 당연히 뮤텍스 때문에 접근하지 못하고 2번의 경우에는 독자 수가 없다가 1명이라도 생기면 mutex_rw에 뮤텍스 락을 걸어 저자가 접근하지 못하도록 한다. (이 동작은 mutex_reader_counter로 원자화 되어 동작을 보증받는다.)

### 한계

만약에 저자 수가 엄청나게 많을 때 상호 배제 때문에 독자가 기록하여야 하는 시간 내에 기록을 못하게 될 수도 있다. 위 해결 방안은 특정 상태가 될 때 특정 스레드나 프로세스가 기아 상태에 빠지게 되는 단점이 있다. (이에 대한 해결 방안은 여기서 다루지 않는다.)

## 식사하는 철학자 문제

위 두 문제에서 상호 배제 외에 다른 문제에 대해서 다루지 않은 것과 다르게 이 문제에서는 경쟁 상태의 모든 문제를 해결하는 데 의의가 있다.

둥근 탁자가 있고 중앙에는 스파게티가 있다. 철학자는 둥근 탁자를 빙 둘러 앉아있으며 철학자에 양 옆에는 포크가 있다. 하지만 포크의 개수와 철학자의 개수는 같으며 스파게티를 먹을 때 포크를 두 개 사용하여야 한다.

철학자는 먹기/생각하기의 행동을 할 수 있다. 또 철학자는 오랫동안 밥을 못 먹으면 기아 상태가 된다.

![](./resources/7-sync-philosopher.png)

여기서 당연히 하나의 포크를 두명 이상이 사용할 수 없으므로 포크 사용에 대해 상호 배제가 적용되어야 한다. 하지만 이 문제에 대한 핵심은 교착 상태와 기아 상태를 방지하는 것이 중요하다.

포크에 대한 상호 배제가 적용되었다는 상황 하에 교착 상태 혹은 기아 상태가 일어날 수 있는 상황을 생각해보자.

- 모든 철학자들이 동시에 식사를 하려 할 때
  - 이 경우 모든 철학자들이 아무것도 먹지 못한다 (교착 상태)
- 교착 상태를 해결하기 위해 양 옆에 포크가 사용 가능할 때 식사를 하게 하거나, 짝/홀수 철학자에 따라 순서를 정해 식사하게 하기
  - 특정 프로세스가 기아 상태에 빠질 수 있음 (식사를 아예 못하진 않겠지만 기아 상태가 길어질 수 있음)

