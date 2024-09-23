# <div align="center"> Average_Load_in_Linux 💣 </div>
<div align="center"> "알쏭달쏭 평균부하" </div>

<br>

---

## 👨‍👨‍👧 개발팀원  

| <img src="https://avatars.githubusercontent.com/u/65991884?v=4" width="80"> | <img src="https://avatars.githubusercontent.com/u/90691610?v=4" width="80"> | <img src="https://avatars.githubusercontent.com/u/79312705?v=4" width="80"> |
|:---:|:---:|:---:|
| [류채현](https://github.com/RyuChaeHyun) | [이석철](https://github.com/SeokCheol-Lee) | [김상민](https://github.com/isshomin) |


<br>

## 📌 개요
시스템의 속도가 느려지는 문제는 다양한 원인에 의해 발생할 수 있으며, 이를 해결하기 위해서는 우선 주요 병목 원인을 조사하고, 시스템의 현재 상태를 정확하게 파악하는 것이 중요합니다.

## 🛠️ 시스템 부하 상태 확인

#### `uptime` 명령어
시스템 부하와 구동 시간을 확인하기 위해 아래 명령어를 입력합니다.

```bash
$ uptime
15:42:30 up 3 days, 2:34,  3 users,  load average: 0.45, 0.30, 0.20
# 현재시간 | 구동시간 | 사용자 | 로드 평균(cpu 작업 큐 에있는 프로세스 평균 수)

```

#### `CPU 개수` 확인 <br>

CPU 코어의 개수를 다음 명령어로 확인합니다.
```bash
$ grep 'model name' /proc/cpuinfo | wc -l
```
시스템의 CPU 코어 수를 반환하고, CPU 코어 수에 따라 적정 로드 평균을 판단할 수 있다.

#### `top` 명령어
top 명령어를 사용하여도 시스템의 현재 상태를 실시간으로 확인할 수 있습니다.

```bash
$ top
MiB Swap:   4096.0 total,   4096.0 free,      0.0 used.   7481.6 avail Mem

````

시스템의 스왑 메모리가 0 MiB로 비어 있고, 사용 가능한 메모리가 7481.6 MiB로 충분하며, 주요 프로세스들의 CPU와 메모리 사용률이 모두 낮아 현재 안정적인 상태임을 나타냅니다.


---

1번 실습환경
<p align="left"><img src="https://github.com/user-attachments/assets/74a35601-656f-4ad8-9f36-5368095437d7"></p>

2번 파일구조
<p align="left"><img src="https://github.com/user-attachments/assets/29a2c5f8-91bc-401b-a91e-6917e780bb8d"></p>

3번 키바나1
<p align="left"><img src="https://github.com/user-attachments/assets/3430f9b4-36bf-42e7-9257-83f015652229"></p>

4번 키바나2
<p align="left"><img src="https://github.com/user-attachments/assets/c5427000-630c-487a-9420-22fbb990ed0d"></p>

5번 키바나3
<p align="left"><img src="https://github.com/user-attachments/assets/f1243c9f-924f-45e7-8f60-cdf7adc4b395"></p>
