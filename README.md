# <div> Average Load in Linux 💣 </div>
<div> 시스템의 안정성과 성능을 유지하기 위해서는 실시간 모니터링과 로그 분석이 필수적입니다. ELK 스택(Elasticsearch, Logstash, Kibana)을 활용하여 시스템 모니터링을 수행함으로써, 부하 테스트 중 수집된 다양한 로그 데이터를 효과적으로 저장하고 시각화하였습니다.
또한 부하 테스트를 통해 시스템의 한계와 성능을 평가하고, 발생한 로그 데이터를 분석하여 시스템 최적화 및 안정성 향상하는 것을 목표로 합니다. </div>

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

<br>

##  ELK 활용한 시스템 모니터링
시스템의 안정성과 성능을 유지하기 위해서는 실시간 모니터링과 로그 분석이 필수적입니다. <br>
ELK 스택(Elasticsearch, Logstash, Kibana)을 활용하여 시스템 모니터링을 수행함으로써, 부하 테스트 중 수집된 다양한 로그 데이터를 효과적으로 저장하고 시각화하였습니다.<br>

부하 테스트를 통해 시스템의 한계와 성능을 평가하고, 발생한 로그 데이터를 분석하여 시스템 최적화 및 안정성 향상하는 것을 목표로 합니다.

<br>

### 🛠️ 실습 환경

- **운영 체제**: Ubuntu 22.04.4 LTS
- **CPU**:
    - **모델명**: 11th Gen Intel(R) Core(TM) i5-1135G7 @ 2.40GHz
    - **코어 수**: 2
<br>

### logstash.conf

```
input {
  file {
    path => "/home/user/log/cpu_usage.log"
    start_position => "beginning"
  }
  file {
    path => "/home/user/log/load_average.log"
    start_position => "beginning"
  }
  file {
    path => "/home/user/log/monitor.log"
    start_position => "beginning"
  }
  file {
    path => "/home/user/log/pidstat.log"
    start_position => "beginning"
  }
}

filter {
  if [path] =~ "pidstat" {
    grok {
      match => {
        "message" => "%{TIMESTAMP_ISO8601:timestamp} %{NUMBER:pid:int} %{NUMBER:cpu_usage:float} %{NUMBER:mem_usage:float} %{NUMBER:io_usage:float}"
      }
    }
    mutate {
      add_field => { "log_type" => "pidstat" }
    }
  } else if [path] =~ "cpu_usage" {
    grok {
      match => {
        "message" => "%{TIMESTAMP_ISO8601:timestamp} %{NUMBER:cpu_core:int} %{NUMBER:usr:float} %{NUMBER:sys:float} %{NUMBER:idle:float}"
      }
    }
    mutate {
      add_field => { "log_type" => "cpu_usage" }
    }
  } else if [path] =~ "load_average" {
    grok {
      match => {
        "message" => "%{TIMESTAMP_ISO8601:timestamp} load average: %{NUMBER:load_avg_1min:float}, %{NUMBER:load_avg_5min:float}, %{NUMBER:load_avg_15min:float}"
      }
    }
    mutate {
      add_field => { "log_type" => "load_average" }
    }
  }
}


output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "system_monitor"
  }
}
```


### 📈 부하 모니터링 로그 기록

부하를 주었을 때, 다음과 같은 로그를 기록하였습니다.


- **로그 항목**:
    - `cpu_usage`
    - `load_average`
    - `monitor`
    - `pidstat`
<p align="left"><img src="https://github.com/user-attachments/assets/29a2c5f8-91bc-401b-a91e-6917e780bb8d"></p>

각각 cpu_usage, load_average, monitor, pidstat으로 log로 기록하였음
<br>

### 📜 모니터링 스크립트 (`monitor.sh`)
**monitor.sh**
```bash
#!/bin/bash

# 로그 파일 저장 경로
LOG_DIR="/home/user/log"
sudo mkdir -p $LOG_DIR

# 모니터링 주기 (초)
INTERVAL=5

# 모니터링 시간 (분)
DURATION=10
END_TIME=$((DURATION * 60))

# 스크립트 시작 시간 기록
sudo echo "Monitoring started at $(date)" > $LOG_DIR/monitor.log

# 평균 부하와 CPU 코어 사용률 모니터링
sudo echo "=== CPU Usage & Load Average ===" >> $LOG_DIR/monitor.log
for (( i=0; i<$END_TIME; i+=$INTERVAL ))
do
  # 평균 부하 측정 (1, 5, 15분 평균)
  sudo uptime >> $LOG_DIR/load_average.log
  
  # CPU 코어별 사용량 측정
  sudo mpstat -P ALL $INTERVAL 1 >> $LOG_DIR/cpu_usage.log

  # 프로세스별 CPU, 메모리, I/O 사용량 측정
  sudo pidstat -d -r -u $INTERVAL 1 >> $LOG_DIR/pidstat.log

  sleep $INTERVAL
  # 스크립트 종료 시간 기록
  echo "Monitoring ended at $(date)" >> $LOG_DIR/monitor.log
done
```



### 📜 Kibana를 통한 모니터링

부하 테스트를 통해 시스템의 성능을 평가하고, 스트레스를 주지 않았을 때와 주었을 때의 시스템 상태를 비교하였습니다. <br> 
ELK 스택을 활용하여 로그 데이터를 수집하고 시각화하여, 각 상태에서의 성능 차이를 분석했습니다.

**스트레스를 주지 않았을 때**
<p align="left"><img src="https://github.com/user-attachments/assets/c5427000-630c-487a-9420-22fbb990ed0d"></p>
-**CPU 사용률**: 낮은 수준을 유지하며 시스템이 안정적임을 나타냄.
- **메모리 사용량**: 여유가 있으며, 프로세스들이 정상적으로 작동 중임.
- **로드 평균**: 평균 부하가 CPU 코어 수에 비해 적정 수준을 유지.

**스트레스를 줬을 때**
<p align="left"><img src="https://github.com/user-attachments/assets/f1243c9f-924f-45e7-8f60-cdf7adc4b395"></p>
- **CPU 사용률**: 급격히 증가하며 과부하 상태를 나타냄.
- **메모리 사용량**: 사용량이 증가하여 시스템의 리소스가 제한적임을 시사.
- **로드 평균**: 평균 부하가 CPU 코어 수를 초과하여 시스템의 응답 속도가 느려짐.

부하 테스트 결과, 스트레스를 주었을 때 시스템의 CPU 사용률과 메모리 사용량이 급격히 증가하며 로드 평균이 높아지는 것을 확인할 수 있었습니다. 이는 시스템의 성능 저하와 안정성 문제를 야기할 수 있으며, 실시간 모니터링이 중요한 이유를 다시 한 번 확인할 수 있는 계기가 되었습니다. 

## 결론
이번 프로젝트에서는 ELK(Elasticsearch, Logstash, Kibana) 스택을 활용하여 시스템에 부하를 가한 상태와 그렇지 않은 상태에서의 성능을 모니터링하였습니다. 이를 통해 ELK 스택이 제공하는 실시간 모니터링과 데이터 분석의 강력함을 체감할 수 있었습니다. 프로젝트의 주요 결론은 다음과 같습니다. <br>

실시간 데이터 수집 및 분석: Logstash를 통해 시스템의 성능 데이터를 실시간으로 수집하고, Elasticsearch에 저장함으로써 즉각적인 데이터 검색과 분석이 가능해졌습니다. 이를 통해 부하가 발생하는 시점에서 시스템의 변화 패턴을 신속하게 파악하고 대응할 수 있었습니다.
효율적인 데이터 시각화: Kibana 대시보드를 통해 CPU 사용량, 메모리 사용량, I/O 성능, 시스템 부하(Load Average) 등의 데이터를 한눈에 시각적으로 모니터링할 수 있었습니다. 이를 통해 부하 상태와 정상 상태의 시스템 성능을 직관적으로 비교할 수 있었으며, 잠재적인 문제 영역을 명확하게 식별할 수 있었습니다.
이상 탐지 및 문제 해결 시간 단축: ELK 스택의 강력한 로그 분석 기능 덕분에 시스템 성능 저하나 병목 현상이 발생하는 시점을 즉시 감지할 수 있었습니다. Kibana의 시각화 도구를 사용해 특정 시점의 성능 이상을 분석하고, 빠르게 원인을 파악함으로써 문제 해결 시간을 크게 단축할 수 있었습니다.
확장성 및 미래 대비: 부하 테스트를 통해 시스템 자원의 한계를 확인하고, 자원의 필요성을 예측할 수 있었습니다. ELK를 활용한 모니터링 덕분에 시스템 확장 계획을 효율적으로 세울 수 있었으며, 부하 없는 상태에서 자원 사용을 최적화하는 방안도 도출할 수 있었습니다.
운영 비용 절감: ELK의 로그 분석을 통해 불필요하게 사용되는 자원들을 식별할 수 있었고, 자원 할당을 최적화함으로써 비용 효율적인 운영이 가능해졌습니다. 또한, 잠재적인 시스템 장애를 조기에 발견하여 운영 중단을 방지함으로써 장기적인 비용 절감 효과도 얻을 수 있었습니다.
ELK 스택을 사용한 이번 모니터링 프로젝트는 시스템의 성능을 실시간으로 추적하고 분석하는 데 있어 매우 유용한 도구임을 확인할 수 있었으며, 이를 통해 시스템 안정성을 높이고 최적화할 수 있었습니다.
