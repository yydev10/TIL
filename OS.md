# Operating system (운영체제)

## Process

### Process의 5단계 상태
- new : 프로세스가 생성 된 직 후
- running : CPU가 프로세스를 점유하면서 실행되서 명령어가 실행되는 상태
- waiting : 자원을 사용하기 위해서 CPU가 다른 작업을 끝낼 때 까지 기다리는 상태 
- ready : I/O에 대기하며 CPU가 점유되기 전의 상태를 의미
- terminated : 프로세스가 완료되거나 외부로 부터 강제 종료한 상태

### Scheduling Algorithms
#### SJF(Shortest Job First)
- 가장 CPU 작업시간이 작은 프로세스 부터 먼저 수행하는 스케쥴링 기법
- 실행시간이 짧은 작업을 신속하게 실행 하므로 평균 대기시간이 다른 스케쥴링 기법보다 짧다
- 프로세스 시작 전 프로세스의 작업 시간을 파악하기 어렵기 때문에 프로세스의 작업시간을 예측하는 과정을 거쳐야 된다. 현실적으로 적용하기 어려운 알고리즘이다

### Thread 동기화
#### mutex(뮤텍스) - spinlock(스핀락)
- mutex와 spinlock 둘 다 자원에 lock이 걸린 경우 lock이 풀릴 때 까지 대기해야 하는 특성을 가진다,
- mutex는 이미 자원에 lock이 걸린 경우 풀릴 때 까지 context switching을 실행한다. 
- 자원을 짧은 시간 내로 얻는 경우 context switching의 오버헤드가 커져 자원을 낭비하는 문제가 있다
- spinlock은 자원에 lock이 걸려 있는 경우 무한루프를 돌면서 대기한다.
- 자원을 짧은 시간 내에 획득하는 경우에는 context switching 비용이 들지 않기 때문에 효율을 높인다
- 반대의 경우 대기하는 시간이 증가하므로 CPU 효율을 떨어트리는 문제가 발생한다.