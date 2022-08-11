### synchronous I/O
- CPU에 의해 I/O 요청 후 입출력 작업이 완료된 후에야 제어권이 사용자 프로그램에 넘어가는 것
- 대부분 동기식의 작업이 이루어진다.
- 이때 해당 작업이 완료될 때까지 CPU 수행권을 다른 프로그램에 주어서 다른 프로그램을 실행시킨다. (이때 I/O 처리를 기다리는 줄에 해당 작업도 대기시킨다.)

### asynchronous I/O (비동기식)
-  I/O가 시작된 후 CPU는 입출력 작업이 끝나길 기다리지 않고 제어권이 사용자 프로그램에 즉시 넘어가는 것
- 쓰기 작업의 경우, 바로 파일을 쓰는 것가지 확인해야 하는 것은 아니기 때문에 비동기식으로 진행할 수 있다.

> 두 경우 모두 Interrupt를 이용하여 작업의 완료를 알린다.