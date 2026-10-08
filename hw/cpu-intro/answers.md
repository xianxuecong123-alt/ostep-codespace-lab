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
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2        RUN:cpu         READY             1          
  3        RUN:cpu         READY             1          
  4        RUN:cpu         READY             1          
  5        RUN:cpu         READY             1          
  6           DONE       RUN:cpu             1          
  7           DONE       RUN:cpu             1          
  8           DONE       RUN:cpu             1          
  9           DONE       RUN:cpu             1          
 10           DONE       RUN:cpu             1          

Stats: Total Time 10
Stats: CPU Busy 10 (100.00%)
Stats: IO Busy  0 (0.00%)
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
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2        RUN:cpu         READY             1          
  3        RUN:cpu         READY             1          
  4        RUN:cpu         READY             1          
  5           DONE        RUN:io             1          
  6           DONE       BLOCKED                           1
  7           DONE       BLOCKED                           1
  8           DONE       BLOCKED                           1
  9           DONE       BLOCKED                           1
 10           DONE       BLOCKED                           1
 11*          DONE   RUN:io_done             1          

Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
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
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          

Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
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
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED         READY                           1
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7*   RUN:io_done         READY             1          
  8           DONE       RUN:cpu             1          
  9           DONE       RUN:cpu             1          
 10           DONE       RUN:cpu             1          
 11           DONE       RUN:cpu             1          

Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
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
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          

Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
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
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUN:cpu         READY         READY             1             1
  3        BLOCKED       RUN:cpu         READY         READY             1             1
  4        BLOCKED       RUN:cpu         READY         READY             1             1
  5        BLOCKED       RUN:cpu         READY         READY             1             1
  6        BLOCKED       RUN:cpu         READY         READY             1             1
  7*         READY          DONE       RUN:cpu         READY             1          
  8          READY          DONE       RUN:cpu         READY             1          
  9          READY          DONE       RUN:cpu         READY             1          
 10          READY          DONE       RUN:cpu         READY             1          
 11          READY          DONE       RUN:cpu         READY             1          
 12          READY          DONE          DONE       RUN:cpu             1          
 13          READY          DONE          DONE       RUN:cpu             1          
 14          READY          DONE          DONE       RUN:cpu             1          
 15          READY          DONE          DONE       RUN:cpu             1          
 16          READY          DONE          DONE       RUN:cpu             1          
 17    RUN:io_done          DONE          DONE          DONE             1          
 18         RUN:io          DONE          DONE          DONE             1          
 19        BLOCKED          DONE          DONE          DONE                           1
 20        BLOCKED          DONE          DONE          DONE                           1
 21        BLOCKED          DONE          DONE          DONE                           1
 22        BLOCKED          DONE          DONE          DONE                           1
 23        BLOCKED          DONE          DONE          DONE                           1
 24*   RUN:io_done          DONE          DONE          DONE             1          
 25         RUN:io          DONE          DONE          DONE             1          
 26        BLOCKED          DONE          DONE          DONE                           1
 27        BLOCKED          DONE          DONE          DONE                           1
 28        BLOCKED          DONE          DONE          DONE                           1
 29        BLOCKED          DONE          DONE          DONE                           1
 30        BLOCKED          DONE          DONE          DONE                           1
 31*   RUN:io_done          DONE          DONE          DONE             1          

Stats: Total Time 31
Stats: CPU Busy 21 (67.74%)
Stats: IO Busy  15 (48.39%)
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
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUN:cpu         READY         READY             1             1
  3        BLOCKED       RUN:cpu         READY         READY             1             1
  4        BLOCKED       RUN:cpu         READY         READY             1             1
  5        BLOCKED       RUN:cpu         READY         READY             1             1
  6        BLOCKED       RUN:cpu         READY         READY             1             1
  7*   RUN:io_done          DONE         READY         READY             1          
  8         RUN:io          DONE         READY         READY             1          
  9        BLOCKED          DONE       RUN:cpu         READY             1             1
 10        BLOCKED          DONE       RUN:cpu         READY             1             1
 11        BLOCKED          DONE       RUN:cpu         READY             1             1
 12        BLOCKED          DONE       RUN:cpu         READY             1             1
 13        BLOCKED          DONE       RUN:cpu         READY             1             1
 14*   RUN:io_done          DONE          DONE         READY             1          
 15         RUN:io          DONE          DONE         READY             1          
 16        BLOCKED          DONE          DONE       RUN:cpu             1             1
 17        BLOCKED          DONE          DONE       RUN:cpu             1             1
 18        BLOCKED          DONE          DONE       RUN:cpu             1             1
 19        BLOCKED          DONE          DONE       RUN:cpu             1             1
 20        BLOCKED          DONE          DONE       RUN:cpu             1             1
 21*   RUN:io_done          DONE          DONE          DONE             1          

Stats: Total Time 21
Stats: CPU Busy 21 (100.00%)
Stats: IO Busy  15 (71.43%)
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
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7        BLOCKED          DONE                           1
  8*   RUN:io_done          DONE             1          
  9         RUN:io          DONE             1          
 10        BLOCKED          DONE                           1
 11        BLOCKED          DONE                           1
 12        BLOCKED          DONE                           1
 13        BLOCKED          DONE                           1
 14        BLOCKED          DONE                           1
 15*   RUN:io_done          DONE             1          

Stats: Total Time 15
Stats: CPU Busy 8 (53.33%)
Stats: IO Busy  10 (66.67%)

### -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7        BLOCKED          DONE                           1
  8*   RUN:io_done          DONE             1          
  9         RUN:io          DONE             1          
 10        BLOCKED          DONE                           1
 11        BLOCKED          DONE                           1
 12        BLOCKED          DONE                           1
 13        BLOCKED          DONE                           1
 14        BLOCKED          DONE                           1
 15*   RUN:io_done          DONE             1          

Stats: Total Time 15
Stats: CPU Busy 8 (53.33%)
Stats: IO Busy  10 (66.67%)

### -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7        BLOCKED         READY                           1
  8*   RUN:io_done         READY             1          
  9         RUN:io         READY             1          
 10        BLOCKED         READY                           1
 11        BLOCKED         READY                           1
 12        BLOCKED         READY                           1
 13        BLOCKED         READY                           1
 14        BLOCKED         READY                           1
 15*   RUN:io_done         READY             1          
 16           DONE       RUN:cpu             1          
 17           DONE       RUN:cpu             1          
 18           DONE       RUN:cpu             1          

Stats: Total Time 18
Stats: CPU Busy 8 (44.44%)
Stats: IO Busy  10 (55.56%)
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7*   RUN:io_done       BLOCKED             1             1
  8         RUN:io       BLOCKED             1             1
  9*       BLOCKED   RUN:io_done             1             1
 10        BLOCKED        RUN:io             1             1
 11        BLOCKED       BLOCKED                           2
 12        BLOCKED       BLOCKED                           2
 13        BLOCKED       BLOCKED                           2
 14*   RUN:io_done       BLOCKED             1             1
 15        RUN:cpu       BLOCKED             1             1
 16*          DONE   RUN:io_done             1          

Stats: Total Time 16
Stats: CPU Busy 10 (62.50%)
Stats: IO Busy  14 (87.50%)

### -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7*   RUN:io_done       BLOCKED             1             1
  8         RUN:io       BLOCKED             1             1
  9*       BLOCKED   RUN:io_done             1             1
 10        BLOCKED        RUN:io             1             1
 11        BLOCKED       BLOCKED                           2
 12        BLOCKED       BLOCKED                           2
 13        BLOCKED       BLOCKED                           2
 14*   RUN:io_done       BLOCKED             1             1
 15        RUN:cpu       BLOCKED             1             1
 16*          DONE   RUN:io_done             1          

Stats: Total Time 16
Stats: CPU Busy 10 (62.50%)
Stats: IO Busy  14 (87.50%)

### -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED         READY                           1
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7*   RUN:io_done         READY             1          
  8         RUN:io         READY             1          
  9        BLOCKED         READY                           1
 10        BLOCKED         READY                           1
 11        BLOCKED         READY                           1
 12        BLOCKED         READY                           1
 13        BLOCKED         READY                           1
 14*   RUN:io_done         READY             1          
 15        RUN:cpu         READY             1          
 16           DONE       RUN:cpu             1          
 17           DONE        RUN:io             1          
 18           DONE       BLOCKED                           1
 19           DONE       BLOCKED                           1
 20           DONE       BLOCKED                           1
 21           DONE       BLOCKED                           1
 22           DONE       BLOCKED                           1
 23*          DONE   RUN:io_done             1          
 24           DONE        RUN:io             1          
 25           DONE       BLOCKED                           1
 26           DONE       BLOCKED                           1
 27           DONE       BLOCKED                           1
 28           DONE       BLOCKED                           1
 29           DONE       BLOCKED                           1
 30*          DONE   RUN:io_done             1          

Stats: Total Time 30
Stats: CPU Busy 10 (33.33%)
Stats: IO Busy  20 (66.67%)
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7        BLOCKED       BLOCKED                           2
  8*   RUN:io_done       BLOCKED             1             1
  9*       RUN:cpu         READY             1          
 10           DONE   RUN:io_done             1          
 11           DONE        RUN:io             1          
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16           DONE       BLOCKED                           1
 17*          DONE   RUN:io_done             1          
 18           DONE       RUN:cpu             1          

Stats: Total Time 18
Stats: CPU Busy 9 (50.00%)
Stats: IO Busy  11 (61.11%)

### -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7        BLOCKED       BLOCKED                           2
  8*   RUN:io_done       BLOCKED             1             1
  9*         READY   RUN:io_done             1          
 10          READY        RUN:io             1          
 11        RUN:cpu       BLOCKED             1             1
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16*          DONE   RUN:io_done             1          
 17           DONE       RUN:cpu             1          

Stats: Total Time 17
Stats: CPU Busy 9 (52.94%)
Stats: IO Busy  11 (64.71%)

### -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7        BLOCKED         READY                           1
  8*   RUN:io_done         READY             1          
  9        RUN:cpu         READY             1          
 10           DONE        RUN:io             1          
 11           DONE       BLOCKED                           1
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16*          DONE   RUN:io_done             1          
 17           DONE        RUN:io             1          
 18           DONE       BLOCKED                           1
 19           DONE       BLOCKED                           1
 20           DONE       BLOCKED                           1
 21           DONE       BLOCKED                           1
 22           DONE       BLOCKED                           1
 23*          DONE   RUN:io_done             1          
 24           DONE       RUN:cpu             1          

Stats: Total Time 24
Stats: CPU Busy 9 (37.50%)
Stats: IO Busy  15 (62.50%)
- Analysis / 分析:

