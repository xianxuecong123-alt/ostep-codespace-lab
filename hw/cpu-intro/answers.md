## Q1
- Prediction / 预测:
Time	PID0	PID1	CPU	IOs
1	RUN:cpu	READY	1	
2	RUN:cpu	READY	1	
3	RUN:cpu	READY	1	
4	RUN:cpu	READY	1	
5	RUN:cpu	READY	1	
6	READY	RUN:cpu	1	
7	READY	RUN:cpu	1	
8	READY	RUN:cpu	1	
9	READY	RUN:cpu	1	
10	READY	RUN:cpu	1	
Total Time: 10 ticks，CPU utilization: 100%
- Reasoning / 理由:두 프로세스 모두 CPU 작업만 수행한다. 프로세스가 끝나야 문맥 전환이 일어나므로, PID0을 전부 실행한 뒤 PID1을 실행한다. CPU는 쉬지 않고 계속 사용된다.
- Verified result / 验证结果:
- Analysis / 分析:

## Q2
- Prediction / 预测:
Time	PID0	PID1	CPU	IOs
1	RUN:cpu	READY	1	
2	RUN:cpu	READY	1	
3	RUN:cpu	READY	1	
4	RUN:cpu	READY	1	
5	IO	READY	0	1
6	IO	READY	0	1
7	IO	READY	0	1
8	IO	READY	0	1
9	IO	READY	0	1
10	READY	RUN:cpu	1	
Total Time: 10 ticks，CPU utilization: 50%
- Reasoning / 理由:PID0이 IO를 실행하면 문맥 전환이 일어난다. IO 대기 중 CPU는 유휴 상태가 되고, IO 완료 후 PID1을 실행한다.
- Verified result / 验证结果:
- Analysis / 分析:

## Q3
- Prediction / 预测:
Time	PID0	PID1	CPU	IOs
1	RUN:io	READY	0	1
2	IO	READY	0	1
3	IO	READY	0	1
4	IO	READY	0	1
5	IO	READY	0	1
6	READY	RUN:cpu	1
7	READY	RUN:cpu	1
8	READY	RUN:cpu	1
9	READY	RUN:cpu	1
Total Time:9 ticks，CPU utilization: 4/9 ≈44.44%
- Reasoning / 理由:PID0이 IO를 요청하자 문맥 전환이 발생한다. IO가 끝날 때까지 PID1의 CPU 작업을 실행한다.
- Verified result / 验证结果:
- Analysis / 分析:

## Q4
- Prediction / 预测:
Time	PID0	PID1	CPU	IOs
1	RUN:io	READY	0	1
2	IO	READY	0	1
3	IO	READY	0	1
4	IO	READY	0	1
5	IO	READY	0	1
6	IO	READY	0	1
7	IO	READY	0	1
8	IO	READY	0	1
9	IO	READY	0	1
10	IO	READY	0	1
Total Time:10 ticks，CPU utilization: 0%
- Reasoning / 理由:SWITCH_ON_END 설정으로 IO가 발생해도 문맥 전환이 일어나지 않는다. PID0의 IO가 끝나기 전까지 PID1은 실행되지 못한다.
- Verified result / 验证结果:
- Analysis / 分析:

## Q5
- Prediction / 预测:
Time	PID0	PID1	CPU	IOs
1	RUN:io	READY	0	1
2	IO	RUN:cpu	1	1
3	IO	RUN:cpu	1	1
4	IO	RUN:cpu	1	1
5	IO	RUN:cpu	1	1
Total Time:5 ticks，CPU utilization: 4/5 = 80%
- Reasoning / 理由:PID0이 IO를 시작하면 즉시 문맥 전환된다. IO 대기동안 PID1의 CPU 작업을 병행해서 실행한다.
- Verified result / 验证结果:
- Analysis / 分析:

## Q6
- Prediction / 预测:
Time	PID0	PID1	PID2	PID3	CPU	IOs
1	RUN:io	READY	READY	READY	1	1
2	BLOCKED	RUN:cpu	READY	READY	1	1
3	BLOCKED	RUN:cpu	READY	READY	1	1
4	BLOCKED	RUN:cpu	READY	READY	1	1
5	BLOCKED	RUN:cpu	READY	READY	1	1
6	BLOCKED	RUN:cpu	READY	READY	1	1
7	RUN:io_done	READY	READY	READY	1	
8	RUN:io	READY	READY	READY	1	1
9	BLOCKED	READY	RUN:cpu	READY	1	1
10	BLOCKED	READY	RUN:cpu	READY	1	1
11	BLOCKED	READY	RUN:cpu	READY	1	1
12	BLOCKED	READY	RUN:cpu	READY	1	1
13	BLOCKED	READY	RUN:cpu	READY	1	1
14	RUN:io_done	READY	READY	READY	1	
15	RUN:io	READY	READY	READY	1	1
16	BLOCKED	READY	READY	RUN:cpu	1	1
17	BLOCKED	READY	READY	RUN:cpu	1	1
18	BLOCKED	READY	READY	RUN:cpu	1	1
19	BLOCKED	READY	READY	RUN:cpu	1	1
20	BLOCKED	READY	READY	RUN:cpu	1	1
21	RUN:io_done	READY	READY	READY	1	
Total Time:21 ticks，CPU utilization: 21/21 =100%
- Reasoning / 理由:IO_RUN_LATER는 I/O가 끝나도 프로세스를 바로 실행하지 않는다. PID0은 I/O 완료 후 준비 큐의 맨 뒤로 가고, 다른 CPU 프로세스가 먼저 실행된다.
- Verified result / 验证结果:
- Analysis / 分析:

## Q7
- Prediction / 预测:
Time	PID0	    PID1	    PID2	    PID3	    CPU	IOs
1	RUN:io	    READY	    READY	    READY	    1	1
2	BLOCKED	    RUN:cpu	    READY	    READY	    1	1
3	BLOCKED	    RUN:cpu	    READY	    READY	    1	1
4	BLOCKED	    RUN:cpu	    READY	    READY	    1	1
5	BLOCKED	    RUN:cpu	    READY	    READY	    1	1
6	BLOCKED	    RUN:cpu	    READY	    READY	    1	1
7*	RUN:io_done	READY	    READY	    READY	    1	
8	RUN:io	    READY	    READY	    READY	    1	1
9	BLOCKED	    READY	    RUN:cpu	    READY	    1	1
10	BLOCKED	    READY	    RUN:cpu	    READY	    1	1
11	BLOCKED	    READY	    RUN:cpu	    READY	    1	1
12	BLOCKED	    READY	    RUN:cpu	    READY	    1	1
13	BLOCKED	    READY	    RUN:cpu	    READY	    1	1
14*	RUN:io_done	READY	    READY	    READY	    1	
15	RUN:io	    READY	    READY	    READY	    1	1
16	BLOCKED	    READY	    READY	    RUN:cpu	    1	1
17	BLOCKED	    READY	    READY	    RUN:cpu	    1	1
18	BLOCKED	    READY	    READY	    RUN:cpu	    1	1
19	BLOCKED	    READY	    READY	    RUN:cpu	    1	1
20	BLOCKED	    READY	    READY	    RUN:cpu	    1	1
21*	RUN:io_done	READY	    READY	    READY	    1	
Total Time:21 ticks，CPU utilization: 100%
- Reasoning / 理由:IO_RUN_IMMEDIATE는 I/O 완료 시 PID0을 즉시 실행한다. I/O‑처리 작업을 바로 수행한 뒤 다시 다른 프로세스로 넘어간다.

- Verified result / 验证结果:
- Analysis / 分析:

## Q8
- Prediction / 预测:
Time	PID0	    PID1	    CPU	IOs
1	RUN:cpu	    READY	    1	
2	RUN:io	    READY	    1	1
3	BLOCKED	    RUN:cpu	    1	1
4	BLOCKED	    BLOCKED	    0	2
5	BLOCKED	    BLOCKED	    0	2
6	BLOCKED	    BLOCKED	    0	2
7	BLOCKED	    BLOCKED	    0	2
8	BLOCKED	    BLOCKED	    0	2
9*	RUN:io_done	RUN:io	    1	1
10	RUN:cpu	    BLOCKED	    1	1
11	DONE	    BLOCKED	    0	1
12	DONE	    BLOCKED	    0	1
13	DONE	    BLOCKED	    0	1
14	DONE	    BLOCKED	    0	1
15	DONE	    BLOCKED	    0	1
16	DONE	    RUN:io_done	1	
Total Time:16 ticks，CPU utilization: 6/16 = 37.5%
- Reasoning / 理由:랜덤 시드에 따라 CPU, IO 명령 순서가 정해진다. IO가 겹치면 I/O 장치는 병렬로 동작하지만 CPU는 유휴 상태가 된다. 설정에 따라 문맥 전환 타이밍이 바뀌고 총 소요 시간도 달라진다.
- Verified result / 验证结果:
- Analysis / 分析:

