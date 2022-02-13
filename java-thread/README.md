# -

## Thread 정리

### Process

- 실행 중인 프로그램, 프로그램을 실행하면 OS에서 자원을 할당받아 프로세스로 동작
- 프로세스는 수행을 위한 데이터, 메모리 그리고 쓰레드로 구성되어 있음 -> 실제 작업은 쓰레드에서 동작
  - 모든 프로세스에는 최소한 하나 이상의 쓰레드가 존재
  - 멀티쓰레드 프로세스 -> 둘 이상의 쓰레드를 가진 프로세스


### 멀티쓰레딩

- 하나의 프로세스 내에서 여러 쓰레드가 동시에 작업을 수행
- CPU 코어는 한 번의 작업만 수행할 수 있으므로 실제 동시에 처리할 수 있는 작업의 개수는 코어의 개수와 일치
  - 프로세스의 성능이 단순히 쓰레드 개수와 비례하지 않음
    - 데이터를 공유해서 context swiching 비용이 상대적으로 낮기는 함
    - 여러 쓰레드가 자원을 공유하기 때문에 동기화, 교착상태를 고려해서 프로그래밍해야 함


### 쓰레드의 구현

- Thread Class를 상속받는 경우
  - 다른 Class를 상속받을 수 없음

- Runnable Interface를 구현하는 경우

### Runnable Interface

- Runnable Interfacfe를 구현한 클래스의 인스턴스를 생성 -> Thread Class의 생성자 매개변수로 제공
- start()를 통해서 쓰레드를 실행시켜야 함
  - 실행대기 상태에서 자신의 차례가 되면서 실행
  - start()는 한 번만 호출 가능함 -> 한 번 실행이 종료되면 재사용 불가능

```java
public class Main {
    public static void main(String[] args) {
        Runnable r = new ThreadTest();
        Thread t1 = new Thread(r);
        Thread t2 = new Thread(r);

        t1.start();
        t2.start();
    }
}

class ThreadTest implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```bash
Thread-0
Thread-1
Thread-0
Thread-1
...
```


### start() vs run()

- override한 run을 호출할 경우 -> 생성된 쓰레드를 실행시키는 것이 아닌, 선언된 메소드를 호출하게 됨
- start 함수를 힝생 -> 작업을 실행하는데 필요한 call stack을 생성 -> run() 호출
  - 모든 쓰레드는 독립적인 작업을 수행하기 위해 자신의 call stack이 필요함
    - 새로운 쓰레드를 생성하고 실행시킬 때마다 새로운 call stack이 생성되고, 종료되면 사라짐


### 멀티쓰레드 프로세스

- 쓰레드 간의 작업 전환 시에 context swiching이 발생하므로, 단순히 쓰레드가 많다고 하여 성능이 향상되지는 않음
- 멀티코어를 사용하는 경우 두 개의 쓰레드의 작업이 동시에 실행될 수 있으므로 성능 향상 가능
  - 자원을 공유하기 때문에 race condition이나 dead lock을 조심해야 함
- 두 쓰레드가 서로 다른 자원을 사용하는 작업의 경우에는 멀티쓰레드 프로세스가 효율적일 수 있음


### 쓰레드 우선순위

- 1~10까지 숫자를 가지고 숫자가 높을수록 우선순위가 높음
- 우선순위는 쓰레드를 생성한 쓰레드로부터 상속받음
- OS마다 다른 방식으로 스케줄링되기 때문에 결과를 예측하기 쉽지 않으므로 우선순위를 변경해서 처리하는 것보다는 로직을 구현해서 처리


### 쓰레드의 실행제어 - Method

- sleep -> 지정된 시간(millis)동안 쓰레드를 일시정지시킴 -> 정지 후 실행대기상태로 변경
- join -> 지정된 시간동안 쓰레드가 실행되도록 함 -> 지정된 시간이 지나거나 작업이 종료되면 join을 호출한 쓰레드로 돌아옴
  - main에서 thread1.join을 호출하면, main thread는 thread1의 작업이 끝날 때까지 기다림
  - 다른 쓰레드의 작업이 먼저 수행되어야할 때 사용
- interrupt -> 일시정지상태인 쓰레드를 깨워서 실행대기 상태로 만듦 -> InterruptedException 발생
- stop -> 쓰레드를 즉시 종료시킴
- suspend -> 쓰레드를 일시정지시킴
- resume -> 쓰레드를 실행대기상태로 변경
- yield -> 자신에게 주어진 실행시간을 양보하고 실행대기상태로 변경

|상태|설명|
|---|---|
|NEW|쓰레드가 생성되었지만 아직 start()가 호출되지는 않음|
|RUNNABLE|실행 중 동기 또는 실행 가능한 상태|
|BLOCKED|동기화블럭에 의해 일시정지 상태(lock으로 잠겨져 있음)|
|WAITING(TIMED_WAITING)|작업이 종료되지는 않았지만 실행가능하지 않음|
|TERMINATED|작업이 종료됨|


### 쓰레드 동작 순서

1. 쓰레드 생성
2. start() 호출
3. 실행대기열에 저장되어 자신의 차례를 기다림(queue)
4. 차례가 되면 실행
5. 실행시간이 다 되거나 yield 호출 시에 실행대기상태로 변경
6. suspend, sleep, join, wait, I/O block 등에 의해 일시정지상태로 변경 가능
7. resume, interrupt 등으로 다시 실행대기열에 저장
8. 작업이 모두 끝나거나 stop이 호출되면 종료


### ing..

쓰레드 동기화,
