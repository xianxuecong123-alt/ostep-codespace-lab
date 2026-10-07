## Q1
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:cpu  READY    1
3    RUN:cpu  READY    1
4    RUN:cpu  READY    1
5    RUN:cpu  READY    1
6    READY    RUN:cpu  1
7    READY    RUN:cpu  1
8    READY    RUN:cpu  1
9    READY    RUN:cpu  1
10   READY    RUN:cpu  1
- Reasoning / 理由:PID0와 PID1 모두 CPU 명령어만 있고 I/O는 없습니다. 스케줄링 정책은 SWITCH_ON_END이므로 프로세스가 명령어를 전부 실행해야 전환됩니다. PID0이 CPU 명령어 5개를 연속으로 다 실행한 뒤에 PID1으로 전환되어 나머지 5개의 CPU 명령어를 실행합니다. CPU는 전체 시간 동안 쉬지 않고 작동하므로 총 소요시간은 10 tick이고 CPU 이용률은 100%입니다.
- Verified result / 验证结果: 
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
- Analysis / 分析:예측과 실행 결과가 같습니다. SWITCH_ON_END 정책은 프로세스가 모든 명령을 끝내기 전에는 CPU를 양보하지 않습니다.

## Q2
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:cpu  READY    1
3    RUN:cpu  READY    1
4    RUN:cpu  READY    1
5    READY    RUN:io   1
6    READY    BLOCKED  0    1
7    READY    BLOCKED  0    1
8    READY    BLOCKED  0    1
9    READY    BLOCKED  0    1
10   READY    BLOCKED  0    1
11   READY    RUN:io_done 1
- Reasoning / 理由:PID0의 CPU 명령어 4개를 먼저 연속 실행합니다. PID0이 끝난 후 PID1로 전환되어 io를 실행합니다. io가 시작되면 PID1은 BLOCKED 상태가 되며 5 tick 동안 대기합니다. 이 기간에는 실행할 프로세스가 없어 CPU가 유휴 상태가 됩니다. io 대기가 끝나면 io_done을 실행합니다. 총 소요시간은 11 tick이고 CPU 이용률은 약 54.55%입니다.
- Verified result / 验证结果:
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
- Analysis / 分析:예측과 실행 결과가 일치합니다. IO를 실행하면 프로세스는 BLOCKED 상태가 되고, 실행할 프로세스가 없을 경우 CPU는 유휴 상태가 됩니다.

## Q3
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1
3    BLOCKED  RUN:cpu  1
4    BLOCKED  RUN:cpu  1
5    BLOCKED  RUN:cpu  1
6    BLOCKED  READY    0
7    RUN:io_done READY 1
- Reasoning / 理由:PID0이 먼저 실행되어 io 명령을 시작합니다. io가 시작되면 PID0은 BLOCKED 상태가 되어 5tick 동안 대기합니다. 이 대기 시간 동안 CPU는 PID1을 스케줄링하여 CPU 명령 4개를 연속 실행합니다. PID1 실행이 끝난 뒤 PID0의 io가 아직 완료되지 않아 CPU가 1tick 유휴 상태가 됩니다. io 대기가 끝나면 io_done을 실행합니다. 총 소요시간은 7tick이고 CPU 이용률은 약 85.71%입니다.
- Verified result / 验证结果:
 1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1              
- Analysis / 分析:예측과 실행 결과가 같습니다. SWITCH_ON_END 환경에서 프로세스가 IO로 대기하는 동안 다른 프로세스를 실행할 수 있습니다.

## Q4
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1
3    BLOCKED  RUN:cpu  1
4    BLOCKED  RUN:cpu  1
5    BLOCKED  RUN:cpu  1
6    RUN:io_done READY 1
- Reasoning / 理由:PID0이 먼저 실행되어 io 명령을 요청합니다. SWITCH_ON_IO 정책에 따라 io 요청이 발생하면 즉시 PID1로 프로세스가 전환됩니다. PID0은 BLOCKED 상태로 5tick 대기하는 동안 PID1의 CPU 명령 4개를 전부 실행합니다. PID0의 io 대기가 끝나면 io_done을 실행합니다. 총 소요시간은 6tick이고 CPU 이용률은 100%입니다.
- Verified result / 验证结果:
 1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          
- Analysis / 分析:예측과 실행 결과가 일치합니다. SWITCH_ON_IO 정책은 IO 요청이 발생하면 즉시 프로세스를 전환하여 CPU 이용률을 높입니다.

## Q5
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1
3    BLOCKED  READY    0    1
4    BLOCKED  READY    0    1
5    BLOCKED  READY    0    1
6    BLOCKED  READY    0    1
7    RUN:io_done READY 1
8    RUN:io   READY    1
9    BLOCKED  READY    0    1
10   BLOCKED  READY    0    1
11   BLOCKED  READY    0    1
12   BLOCKED  READY    0    1
13   BLOCKED  READY    0    1
14   RUN:io_done READY 1
- Reasoning / 理由:PID0은 io 명령 2개, PID1은 cpu 명령 1개입니다. SWITCH_ON_IO 정책으로 io 요청 시 즉시 전환됩니다.
tick1: PID0이 첫 번째 io를 요청하고 BLOCKED 상태가 되어 PID1로 전환합니다.
tick2: PID1이 cpu 명령 1개를 실행하고 종료합니다.
tick3~6: PID1은 이미 종료되었고 PID0은 첫 번째 io 대기 중이므로 CPU가 유휴 상태입니다.
tick7: 첫 번째 io가 완료되어 PID0이 io_done을 실행합니다.
tick8: PID0이 두 번째 io를 요청하고 BLOCKED 상태가 됩니다.
tick9~13: PID0이 두 번째 io 대기 중이므로 CPU가 유휴 상태입니다.
tick14: 두 번째 io가 완료되어 PID0이 io_done을 실행하고 모든 작업이 끝납니다.
총 소요시간은 14tick이고 CPU 이용률은 약 35.71%입니다.
- Verified result / 验证结果:
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED          DONE                           1
  4        BLOCKED          DONE                           1
  5        BLOCKED          DONE                           1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          
  8         RUN:io          DONE             1          
  9        BLOCKED          DONE                           1
 10        BLOCKED          DONE                           1
 11        BLOCKED          DONE                           1
 12        BLOCKED          DONE                           1
 13        BLOCKED          DONE                           1
 14*   RUN:io_done          DONE             1           
- Analysis / 分析:예측 결과와 시뮬레이터 실행 결과가 일치합니다. IO_RUN_LATER 정책은 IO 작업이 완료되어도 프로세스를 즉시 실행하지 않고 대기시킵니다.

## Q6
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1
3    BLOCKED  READY    0    1
4    BLOCKED  READY    0    1
5    BLOCKED  READY    0    1
6    RUN:io_done READY 1
7    RUN:io   READY    1
8    BLOCKED  READY    0    1
9    BLOCKED  READY    0    1
10   BLOCKED  READY    0    1
11   BLOCKED  READY    0    1
12   RUN:io_done READY 1
- Reasoning / 理由:PID0에는 io 명령 2개, PID1에는 cpu 명령 1개가 있습니다. SWITCH_ON_IO 정책으로 io 요청 시 즉시 전환되고, IO_RUN_IMMEDIATE 정책은 io가 완료되면 즉시 해당 프로세스를 실행합니다.
tick1: PID0이 첫 번째 io를 요청하고 BLOCKED 상태가 되며 PID1로 전환합니다.
tick2: PID1이 cpu 명령 1개를 실행하고 종료합니다.
tick3~5: PID0이 io 대기 중이고 실행할 프로세스가 없어 CPU가 유휴 상태입니다.
tick6: 첫 번째 io가 완료되면 즉시 PID0이 io_done을 실행합니다.
tick7: PID0이 두 번째 io를 요청하고 BLOCKED 상태가 됩니다.
tick8~11: PID0이 두 번째 io 대기 중이므로 CPU가 유휴 상태입니다.
tick12: 두 번째 io가 완료되면 즉시 PID0이 io_done을 실행하고 모든 작업이 끝납니다.
총 소요시간은 12tick이고 CPU 이용률은 약 41.67%입니다.
- Verified result / 验证结果:
 1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED          DONE                           1
  4        BLOCKED          DONE                           1
  5        BLOCKED          DONE                           1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          
  8         RUN:io          DONE             1          
  9        BLOCKED          DONE                           1
 10        BLOCKED          DONE                           1
 11        BLOCKED          DONE                           1
 12        BLOCKED          DONE                           1
 13        BLOCKED          DONE                           1
 14*   RUN:io_done          DONE             1          
- Analysis / 分析:예측과 실행 결과가 같습니다. IO_RUN_IMMEDIATE 정책은 IO가 끝나면 즉시 해당 프로세스를 실행합니다.f

## Q7
- Prediction / 预测:
Time PID0      PID1      PID2      PID3      CPU IOs
1    RUN:io    READY     READY     READY     1
2    BLOCKED   RUN:cpu   READY     READY     1
3    BLOCKED   RUN:cpu   READY     READY     1
4    BLOCKED   RUN:cpu   READY     READY     1
5    BLOCKED   RUN:cpu   READY     READY     1
6    BLOCKED   RUN:cpu   READY     READY     1
7    BLOCKED   DONE      RUN:cpu   READY     1
8    BLOCKED   DONE      RUN:cpu   READY     1
9    BLOCKED   DONE      RUN:cpu   READY     1
10   BLOCKED   DONE      RUN:cpu   READY     1
11   BLOCKED   DONE      RUN:cpu   READY     1
12   BLOCKED   DONE      DONE      RUN:cpu   1
13   BLOCKED   DONE      DONE      RUN:cpu   1
14   BLOCKED   DONE      DONE      RUN:cpu   1
15   BLOCKED   DONE      DONE      RUN:cpu   1
16   BLOCKED   DONE      DONE      RUN:cpu   1
17*  RUN:io_done DONE    DONE      DONE      1
18   RUN:io    DONE      DONE      DONE      1
19   BLOCKED   DONE      DONE      DONE
20   BLOCKED   DONE      DONE      DONE
21   BLOCKED   DONE      DONE      DONE
22   BLOCKED   DONE      DONE      DONE
23   BLOCKED   DONE      DONE      DONE
24*  RUN:io_done DONE    DONE      DONE      1
25   RUN:io    DONE      DONE      DONE      1
26   BLOCKED   DONE      DONE      DONE
27   BLOCKED   DONE      DONE      DONE
28   BLOCKED   DONE      DONE      DONE
29   BLOCKED   DONE      DONE      DONE
30   BLOCKED   DONE      DONE      DONE
31*  RUN:io_done DONE    DONE      DONE      1
32   DONE      DONE      DONE      DONE
- Reasoning / 理由:PID0는 I/O 3개를 가집니다. SWITCH_ON_IO는 I/O 시작 시 프로세스를 전환하고, IO_RUN_IMMEDIATE는 I/O 완료 후 즉시 CPU를 선점합니다.
I/O는 RUN:io(1틱) + BLOCKED(5틱) + RUN:io_done(1틱)로 실행됩니다.
다른 프로세스가 종료된 뒤 I/O 대기 구간에서는 CPU가 유휴 상태가 됩니다.
- Verified result / 验证结果:
Time PID0      PID1      PID2      PID3      CPU IOs
1    RUN:io    READY     READY     READY     1
2    BLOCKED   RUN:cpu   READY     READY     1
3    BLOCKED   RUN:cpu   READY     READY     1
4    BLOCKED   RUN:cpu   READY     READY     1
5    BLOCKED   RUN:cpu   READY     READY     1
6    BLOCKED   RUN:cpu   READY     READY     1
7    BLOCKED   DONE      RUN:cpu   READY     1
8    BLOCKED   DONE      RUN:cpu   READY     1
9    BLOCKED   DONE      RUN:cpu   READY     1
10   BLOCKED   DONE      RUN:cpu   READY     1
11   BLOCKED   DONE      RUN:cpu   READY     1
12   BLOCKED   DONE      DONE      RUN:cpu   1
13   BLOCKED   DONE      DONE      RUN:cpu   1
14   BLOCKED   DONE      DONE      RUN:cpu   1
15   BLOCKED   DONE      DONE      RUN:cpu   1
16   BLOCKED   DONE      DONE      RUN:cpu   1
17*  RUN:io_done DONE    DONE      DONE      1
18   RUN:io    DONE      DONE      DONE      1
19   BLOCKED   DONE      DONE      DONE
20   BLOCKED   DONE      DONE      DONE
21   BLOCKED   DONE      DONE      DONE
22   BLOCKED   DONE      DONE      DONE
23   BLOCKED   DONE      DONE      DONE
24*  RUN:io_done DONE    DONE      DONE      1
25   RUN:io    DONE      DONE      DONE      1
26   BLOCKED   DONE      DONE      DONE
27   BLOCKED   DONE      DONE      DONE
28   BLOCKED   DONE      DONE      DONE
29   BLOCKED   DONE      DONE      DONE
30   BLOCKED   DONE      DONE      DONE
31*  RUN:io_done DONE    DONE      DONE      1
32   DONE      DONE      DONE      DONE
Stats: Total Time 32, CPU Busy 14 (43.75%)
- Analysis / 分析:예측 결과와 시뮬레이터 출력이 일치합니다. IO_RUN_IMMEDIATE 정책으로 I/O가 완료되면 프로세스가 즉시 CPU를 선점합니다. 다른 프로세스가 모두 종료된 후 I/O 대기 시간에는 CPU가 유휴 상태가 되어 CPU 이용률이 낮아집니다.

## Q8
- Prediction / 预测:
seed=1
 Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:io   READY    1
3    BLOCKED  RUN:io   1
4    BLOCKED  BLOCKED
5    BLOCKED  BLOCKED
6    BLOCKED  BLOCKED
7    BLOCKED  BLOCKED
8    BLOCKED  BLOCKED
9*   READY    BLOCKED
10   RUN:cpu  BLOCKED  1
11   DONE*    READY    1
12   DONE     RUN:io_done 1
13   DONE     RUN:cpu  1
14   DONE     RUN:io   1
15   DONE     BLOCKED
16   DONE     BLOCKED
17   DONE     BLOCKED
18   DONE     BLOCKED
19   DONE     BLOCKED
20*  DONE     READY
21   DONE     RUN:io_done 1
22   DONE     DONE
seed=2
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1
3    BLOCKED  RUN:cpu  1
4    BLOCKED  RUN:io   1
5    BLOCKED  BLOCKED
6    BLOCKED  BLOCKED
7    BLOCKED  BLOCKED
8    BLOCKED  BLOCKED
9    BLOCKED  BLOCKED
10*  READY    READY
11   RUN:io_done READY 1
12   RUN:cpu  READY    1
13   RUN:io   READY    1
14   BLOCKED  BLOCKED
15   BLOCKED  BLOCKED
16   BLOCKED  BLOCKED
17   BLOCKED  BLOCKED
18   BLOCKED  BLOCKED
19*  READY    READY
20   RUN:io_done READY 1
21   DONE     DONE
seed=3
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:cpu  READY    1
3    RUN:io   READY    1
4    BLOCKED  RUN:io   1
5    BLOCKED  BLOCKED
6    BLOCKED  BLOCKED
7    BLOCKED  BLOCKED
8    BLOCKED  BLOCKED
9    BLOCKED  BLOCKED
10*  READY    READY
11   RUN:io_done READY 1
12   RUN:cpu  READY    1
13   DONE     DONE
- Reasoning / 理由:두 프로세스는 3개의 명령을 가지며 시드에 따라 CPU/I/O 명령이 정해집니다.
기본 정책 SWITCH_ON_IO는 I/O 호출 시 CPU를 전환하고, IO_RUN_LATER는 I/O가 끝나도 바로 실행하지 않고 대기 큐에서 기다립니다.
두 프로세스가 동시에 BLOCKED가 되면 CPU는 유휴 상태가 됩니다.
- Verified result / 验证结果:
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:io   READY    1
3    BLOCKED  RUN:io   1
4    BLOCKED  BLOCKED
5    BLOCKED  BLOCKED
6    BLOCKED  BLOCKED
7    BLOCKED  BLOCKED
8    BLOCKED  BLOCKED
9*   READY    BLOCKED
10   RUN:cpu  BLOCKED  1
11   DONE*    READY    1
12   DONE     RUN:io_done 1
13   DONE     RUN:cpu  1
14   DONE     RUN:io   1
15   DONE     BLOCKED
16   DONE     BLOCKED
17   DONE     BLOCKED
18   DONE     BLOCKED
19   DONE     BLOCKED
20*  DONE     READY
21   DONE     RUN:io_done 1
22   DONE     DONE
Stats: Total Time 22, CPU Busy 10 (45.45%)
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1
3    BLOCKED  RUN:cpu  1
4    BLOCKED  RUN:io   1
5    BLOCKED  BLOCKED
6    BLOCKED  BLOCKED
7    BLOCKED  BLOCKED
8    BLOCKED  BLOCKED
9    BLOCKED  BLOCKED
10*  READY    READY
11   RUN:io_done READY 1
12   RUN:cpu  READY    1
13   RUN:io   READY    1
14   BLOCKED  BLOCKED
15   BLOCKED  BLOCKED
16   BLOCKED  BLOCKED
17   BLOCKED  BLOCKED
18   BLOCKED  BLOCKED
19*  READY    READY
20   RUN:io_done READY 1
21   DONE     DONE
Stats: Total Time 21, CPU Busy 9 (42.86%)
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:cpu  READY    1
3    RUN:io   READY    1
4    BLOCKED  RUN:io   1
5    BLOCKED  BLOCKED
6    BLOCKED  BLOCKED
7    BLOCKED  BLOCKED
8    BLOCKED  BLOCKED
9    BLOCKED  BLOCKED
10*  READY    READY
11   RUN:io_done READY 1
12   RUN:cpu  READY    1
13   DONE     DONE
Stats: Total Time 13, CPU Busy 7 (53.85%)
- Analysis / 分析:시드 값마다 생성되는 명령 시퀀스가 달라 총 실행 시간이 다릅니다. IO_RUN_LATER 정책으로 I/O 완료 후 프로세스는 준비 큐에서 대기합니다. 두 프로세스가 동시에 BLOCKED 상태가 되면 CPU는 유휴 상태가 됩니다.

