---
title: "[OS] 9장 프로세스 동기화 - 2"
excerpt: "운영체제 개념 정리"
categories:
  - Study
tags:
  - Study_OperatingSystem
search: true
toc: true
sidebar_main: true
---

# 전통적 동기화 예제(Classical Synchronization Problems)
## Producer-Consumer Problem
생산자-소비자 문제는 생산자가 데이터를 생산하면 소비자는 그 데이터를 소비하는 형태의 문제이다. 컴퓨터 환경에서 예를 보면, 컴파일러 -> 어셈블러, 웹 서버 -> 웹 클라이언트 등이 있다. 컴파일러에서 생성한 어셈블리어는 어셈블러에서 이를 소비하여 기계어를 만든다.

![OS09-1](https://user-images.githubusercontent.com/34755287/54337393-1ee43880-4672-11e9-9ca9-b9d0b414b833.png)

생산자-소비자 관계를 간단히 그림으로 나타내면 위와 같다. 이 관계의 대부분은 생산자에서 생산한 데이터 양을 소비자가 한 번에 소비하는 경우는 드물다. 생산한 데이터는 중간의 **buffer** 라는 저장 공간(메모리 공간)에 저장해두고 소비자는 여기서 필요한 만큼 가져간다.

버퍼의 크기는 현실적으로 유한하다. 그러므로 생산자는 버퍼 공간이 가득 차면 더 이상 저장할 수 없다. 소비자는 버퍼가 비어 있으면 가져올 수 없다. 이러한 유한한 버퍼 크기를 **bounded buffer** 라고 한다.

Bounded buffer를 가진 생산자-소비자 문제를 자바 코드를 통해 살펴보자.

```cpp
class Test {
	public static void main(String[] arg) {
		Buffer b = new Buffer(100);
		Producer p = new Producer(b, 10000);
		Consumer c = new Consumer(b, 10000);
		p.start();
		c.start();
		try {
			p.join();
			c.join();
		} catch (InterruptedException e) {}
		System.out.println("Number of items in the buf is " + b.count);
	}
}

class Buffer {
	int[] buf;
	int size;
	int count;
	int in;
	int out;
	Buffer(int size) {
		buf = new int[size];
		this.size = size;
		count = in = out = 0;
	}
	void insert(int item) {
		/* check if buf is full */
		while (count == size)
			;
		/* buf is not full */
		buf[in] = item;
		in = (in+1)%size;
		count++;
	}
	int remove() {
		/* check if buf is empty */
		while (count == 0)
			;
		/* buf is not empty */
		int item = buf[out];
		out = (out+1)%size;
		count--;
		return item;
	}
}

/****** 생산자 ******/
class Producer extends Thread {
	Buffer b;
	int N;
	Producer(Buffer b, int N) {
		this.b = b; this.N = N;
	}
	public void run() {
		for (int i=0; i<N; i++)
			b.insert(i);
	}
}
/****** 소비자 ******/
class Consumer extends Thread {
	Buffer b;
	int N;
	Consumer(Buffer b, int N) {
		this.b = b; this.N = N;
	}
	public void run() {
	int item;
	for (int i=0; i<N; i++)
		item = b.remove();
	}
}
```

Buffer 클래스의 멤버 변수를 보면
- ```buf```: Bounded buffer
- ```size```: 버퍼 크기
- ```count```: 버퍼에 저장된 데이터 개수
- ```in```: 생산한 데이터를 담을 버퍼 인덱스
- ```out```: 소비할 데이터를 가리키는 버퍼 인덱스

만약 생성자가 데이터를 계속 생성하여 버퍼의 마지막 인덱스로 가면 그 다음은 처음으로 되돌아간다.(circular buffer) 소비하는 것도 마찬가지이다.

main을 보면 크기가 100인 버퍼를 생성하고 2개의 쓰레드가 각각 생산자와 소비자 역할을 하여 각각 10000번씩 생산하고 소비한다. 정상적인 결과는 count값이 0이 출력되야 한다.

하지만 실제 코드를 수행하면 무한 루프에 빠지거나, count값에 전혀 예상하지 않은 값이 출력된다.

이 문제 역시, 동기화 문제이다. 생산자와 소비자가 동시에 접근하는 공통 변수인 ```buf, count``` 를 두 프로세스가 동시에 업데이트하기 때문이다. 다시 말하면 **임계구역에 동시에 접근** 한 것이다.

해결방법은 앞서 배웠던 세마포를 사용하여 mutual exclusion을 보장하는 것이다. 임계구역을 동시에 접근하는 것을 방지하고 하나의 프로세스만 허용해야한다.

```java
class Buffer {
	int[] buf;
	int size;
	int count;
	int in;
	int out;
	Semaphore mutex;   // 세마포 선언

	Buffer(int size) {
		buf = new int[size];
		this.size = size;
		count = in = out = 0;
		mutex = new Semaphore(1);
	}

	void insert(int item) {
		/* check if buf is full */
		while (count == size)
			;
		/* buf is not full */
		try {
		mutex.acquire();
		buf[in] = item;
		in = (in+1)%size;
		count++;
		mutex.release();
		} catch(InterruptedException e) {}
	}

	int remove() {
		/* check if buf is empty */
		while (count == 0)
			;
		/* buf is not empty */
		try {
		mutex.acquire();
		int item = buf[out];
		out = (out+1)%size;
		count--;
		mutex.release();
		return item;
		} catch(InterruptedException e) {}
		return -1;
	}
}
```

위는 임계구역에 세마포를 추가한 코드이다. 임계구역은 위에서 말했듯이 ```buf, count``` 에 접근하는 영역이므로 ```insert()```, ```remove()``` 함수 내부에 선언한 것을 볼 수 있다.

하지만 여기서 한 가지 더 문제점이 있다. 바로 **busy waiting** 이다. 위에서 busy waiting은 생산과 소비하기 전에 버퍼가 가득 찼는지 비어 있는지 확인하는 무한 반복문을 말한다. 이는 아무 일도 하지 않으면서 무한으로 반복하여 CPU를 점유하고 있으므로 매우 비효율적이다. 이를 해결할 수 있는 것도 세마포이다.

```java
class Buffer {
	int[] buf;
	int size;
	int count;
	int in;
	int out;
	Semaphore mutex, full, empty;

	Buffer(int size) {
		buf = new int[size];
		this.size = size;
		count = in = out = 0;
		mutex = new Semaphore(1);
		full = new Semaphore(0);
		empty = new Semaphore(size);
	}

	void insert(int item) {
		try {
		empty.acquire();    // 버퍼의 비어있는 공간을 1 감소시킨다.(비어있는 공간이 없으면 block)
		mutex.acquire();
		buf[in] = item;
		in = (in+1)%size;
		count++;
		mutex.release();
		full.release();    // 버퍼의 찬 공간을 1 증가시킨다.
		} catch(InterruptedException e) {}
	}

	int remove() {
		try {
		full.acquire();    // 버퍼의 찬 공간을 1 감소시킨다.(버퍼가 모두 비어있으면 block)
		mutex.acquire();
		int item = buf[out];
		out = (out+1)%size;
		count--;
		mutex.release();
		empty.release();   // 버퍼의 비어있는 공간을 1 증가시킨다.
		return item;
		} catch(InterruptedException e) {}
		return -1;
	}
}
```

busy waiting을 없애기 위해 두 개의 세마포를 더 추가하였다.
- ```empty```: 버퍼에서 비어있는 공간의 개수(초기값 size)
- ```full```: 버퍼에서 차있는 공간의 개수(초기값 0)

추가한 세마포 변수는 위와 같고, empty는 초기화할 때 버퍼는 모두 비어있으므로 버퍼의 크기로 초기화하고 full은 초기 버퍼에는 아무런 데이터가 없으므로 0으로 초기화한다.

데이터를 생성하기 전에 비어있는 공간이 있는지 확인한다. 없다면 empty세마포의 value값이 -1이 되므로 block이 되고, 있다면 임계구역 내부로 진입하여 데이터를 생성한다. 생성이 완료되면 full세마포의 value값을 1 증가시킨다.(소비자는 반대로 동작한다고 볼 수 있다. 코드참고) 이 코드를 실행시켜보면 정상적으로 결과값이 0이 출력되는 것을 확인할 수 있다.

## Readers-Writers Problem
Readers-Writers 문제는 대표적으로 공통 데이터베이스에 접근하는 경우가 있다. 하나의 데이터베이스에 여러 프로세스(readers, writers)가 접근하므로, 데이터베이스는 임계구역으로 설정해야한다. 즉, 한 번에 한 개의 프로세스만 접근가능하도록 해야하는데 이는 매우 비효율적이다.

비효율을 해결하기 위해 데이터베이스에 접근하는 프로세스 종류를 reader와 writer로 나눈다. 그리고 reader는 **데이터베이스 내의 정보를 바꾸지 않고 읽기만 하는 프로세스** 이므로, 여러 reader 프로세스가 동시에 데이터베이스를 접근하는 것을 허용한다.

writer는 데이터베이스 내용을 바꾸는 프로세스이므로 당연히 **mutual exclusion** 을 보장해야한다.

Readers-Writers 문제는 우선순위에 따라 여러 경우로 나눌 수 있다.
- The first R/W problem (readers-preference): 이 방법은 reader 프로세스에 우선권을 주는 것이다. 만약, 한 reader 프로세스가 데이터베이스를 읽고 있는 동안 writer 프로세스가 오면 당연히 접근하지 못하고 기다린다. 이 상황에서 다른 reader 프로세스가 들어온다면, writer 프로세스가 기다리는 것을 무시하고 데이터베이스에 접근하여 읽는다. 그 결과, 두 reader가 동시에 데이터베이스를 읽는 상황이 된다.

- The second R/W problem (writers-preference): 위 방법과 반대로 writer 프로세스가 기다리는 상황에서 다른 reader 프로세스가 들어온다면, 기존의 writer 프로세스 다음 순서로 기다려야한다.

- The third R/W problem: 아무에게도 우선순위를 주지 않는다.

## Dining Philosopher Problem
식사하는 철학자 문제는 원형 테이블에 5명의 철학자와 5개의 젓가락이 있는 상황이 있다고 하자. 각 철학자는 생각하고 식사하고 생각하고 식사하고를 반복한다. 단, 식사를 하기 위해서는 2개의 젓가락이 필요하다.

![OS09-2](https://user-images.githubusercontent.com/34755287/54337394-1f7ccf00-4672-11e9-9fc0-cb98c4171239.png)

이 상황을 프로그래밍을 해보자. 젓가락은 한 철학자가 가져가면 다른 철학자는 이 젓가락을 사용할 수 없다. 즉, 한 젓가락에 동시에 접근할 수 있는 철학자는 **한 명** 뿐이므로 젓가락은 세마포로 만들 수 있다.(number of permit = 1) 한 철학자가 식사를 하려고 하면, 왼쪽 젓가락과 오른쪽 젓가락 순서로 가져가고, 식사가 끝나면 동일하게 왼쪽 젓가락, 오른쪽 젓가락 순서로 내려놓는다.

이를 자바 코드로 구현하면 아래와 같다.

```java
import java.util.concurrent.Semaphore;

class Philosopher extends Thread {
	int id; // philosopher id
	Semaphore lstick, rstick; // left, right chopsticks
	Philosopher(int id, Semaphore lstick, Semaphore rstick) {
		this.id = id;
		this.lstick = lstick;
		this.rstick = rstick;
	}

	public void run() {
		try {
			while (true) {
				lstick.acquire();
				rstick.acquire();
				eating();
				lstick.release();
				rstick.release();
				thinking();
			}
		}catch (InterruptedException e) { }
	}

	void eating() {
		System.out.println("[" + id + "] eating");
	}

	void thinking() {
		System.out.println("[" + id + "] thinking");
	}
}

class Test {
	static final int num = 5; // number of philosphers & chopsticks
	public static void main(String[] args) {
	int i;
	/* chopsticks */
	Semaphore[] stick = new Semaphore[num];
	for (i=0; i<num; i++)
		stick[i] = new Semaphore(1);
	/* philosophers */
	Philosopher[] phil = new Philosopher[num];
	for (i=0; i<num; i++)
		phil[i] = new Philosopher(i, stick[i], stick[(i+1)%num]);
	/* let philosophers eat and think */
	for (i=0; i<num; i++)
		phil[i].start();
	}
}
```

5개의 젓가락 세마포와 5명의 철학자 쓰레드를 생성한다. 각 철학자 쓰레드에는 무한 반복문으로 왼쪽 젓가락과 오른쪽 젓가락을 순서대로 집은 후 식사를 하고(몇 번 철학자가 식사했다는 것을 화면에 출력), 다시 왼쪽 젓가락, 오른쪽 젓가락 순으로 내려놓고 생각을 한다.

단순히 코드를 봐서는 문제점이 없어보인다. 하지만 이를 수행하면 중간에 멈추고 더이상 실행되지 않는다.
이는 대표적인 **starvation** 문제중 하나이다. 모든 철학자가 식사를 하지 못하고 굶어죽는 상황이라고 할 수 있다.

이는 매우 드문 상황으로 모든 철학자가 동시에 식사를 하려고 왼쪽 젓가락을 집었다고 하자. 그러면 5명의 철학자가 5개의 젓가락을 모두 집어든 상황이다. 그 결과, 남아있는 젓가락은 더 이상없고 모든 철학자가 반대편 젓가락을 들기 위해 기다리고 있다. 하지만 식사할 수 있는 철학자는 없으므로 아무도 젓가락은 내려놓지 않고 하염없이 기다리고 있다.

이러한 상황을 **교착상태(deadlock)** 라고 한다.
