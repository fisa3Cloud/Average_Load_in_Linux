# 💣 Average Load in Linux 
"ELK Stack을 통한 시스템 성능 모니터링 및 부하 테스트"

시스템의 안정성과 성능을 유지하기 위해서는 **실시간 모니터링과 로그 분석**이 필수적입니다. 

**ELK 스택(Elasticsearch, Logstash, Kibana)** 을 활용하여 시스템 모니터링을 수행함으로써, 부하 테스트 중 수집된 다양한 로그 데이터를 효과적으로 저장하고 시각화하였습니다.
또한 **부하 테스트를 통해 시스템의 한계와 성능을 평가**하고, 발생한 로그 데이터를 분석하여 시스템 최적화 및 안정성 향상하는 것을 목표로 합니다. 😃


<br>


## 👨‍👨‍👧 개발팀원  

| <img src="https://avatars.githubusercontent.com/u/65991884?v=4" width="80"> | <img src="https://avatars.githubusercontent.com/u/90691610?v=4" width="80"> | <img src="https://avatars.githubusercontent.com/u/79312705?v=4" width="80"> |
|:---:|:---:|:---:|
| [류채현](https://github.com/RyuChaeHyun) | [이석철](https://github.com/SeokCheol-Lee) | [김상민](https://github.com/isshomin) |

<br>

## 📌 프로젝트 개요

이 프로젝트는 **ELK Stack(Elasticsearch, Logstash, Kibana)** 를 사용하여 시스템의 부하 상태를 실시간으로 모니터링하고, **부하 테스트를 통해 성능을 분석**하는 것을 목표로 하였습니다. 이를 통해 시스템의 한계와 성능을 평가하고, 수집된 로그 데이터를 기반으로 성능을 최적화하고 안정성을 향상시켰습니다.

<br>

## 🎖️ 주요 목표
1. **시스템 부하 상태 모니터링**: `uptime` 및 `top` 명령어로 CPU 및 시스템 부하 상태를 확인.
2. **부하 테스트**: 스트레스 도구를 이용하여 시스템에 인위적으로 부하를 가한 후, ELK 스택을 통해 성능을 모니터링.
3. **로그 데이터 수집 및 분석**: 부하 테스트 중 발생한 로그 데이터를 수집하여 성능 지표를 시각화하고, 이를 바탕으로 시스템 최적화.

<br>

## 🛠️ 실습 환경

- **운영 체제**: Ubuntu 22.04.4 LTS
- **CPU**:
    - **모델명**: 11th Gen Intel(R) Core(TM) i5-1135G7 @ 2.40GHz
    - **코어 수**: 2

<br>

## 💡 사용된 기술

- **Elasticsearch**: 실시간 로그 데이터 저장 및 검색
- **Logstash**: 로그 수집 및 필터링
- **Kibana**: 시각화를 통한 실시간 데이터 모니터링
- **Stress**: 시스템에 인위적인 부하를 가해 성능 테스트
- **Linux 명령어**: `uptime`, `top` 등을 이용해 CPU 부하 모니터링

<br>

## 📊 프로젝트 과정

### 1. 시스템 부하 상태 확인
시스템 부하상태를 확인하기 위해서는 `uptime` 또는 `top` 명령어를 사용한다. 

<br>

#### `uptime` 명령어
시스템 부하와 구동 시간을 확인하기 위해 아래 명령어를 입력합니다.

```bash
$ uptime
15:42:30 up 3 days, 2:34,  3 users,  load average: 0.45, 0.30, 0.20

# 현재시간 | 구동시간 | 사용자 | 로드 평균(cpu 작업 큐 에 있는 프로세스 평균 수)
```

#### (추가) `CPU 개수` 확인 <br>

CPU 코어의 개수를 다음 명령어로 확인합니다.
```bash
$ grep 'model name' /proc/cpuinfo | wc -l
```
시스템의 CPU 코어 수를 반환하고, CPU 코어 수에 따라 적정 로드 평균을 판단할 수 있습니다.

<br>

#### `top` 명령어
top 명령어를 사용하여도 시스템의 현재 상태를 실시간으로 확인할 수 있습니다.

```bash
$ top
MiB Swap:   4096.0 total,   4096.0 free,      0.0 used.   7481.6 avail Mem

````

시스템의 스왑 메모리가 0 MiB로 비어 있고, 사용 가능한 메모리가 7481.6 MiB로 충분하며, 주요 프로세스들의 CPU와 메모리 사용률이 모두 낮아 현재 안정적인 상태임을 나타냅니다.

<br>

---

##  2. ELK 활용한 시스템 모니터링 
시스템의 안정성과 성능을 유지하기 위해서는 실시간 모니터링과 로그 분석이 필수적입니다. <br>
ELK 스택(Elasticsearch, Logstash, Kibana)을 활용하여 시스템 모니터링을 수행함으로써, 부하 테스트 중 수집된 다양한 로그 데이터를 효과적으로 저장하고 시각화하였습니다.<br>

부하 테스트를 통해 시스템의 한계와 성능을 평가하고, 발생한 로그 데이터를 분석하여 시스템 최적화 및 안정성 향상하는 것을 목표로 합니다.

<br>

### 2-1. 📜 모니터링 스크립트 (`monitor.sh`)

부하 테스트를 시작하기 위해 **시스템 모니터링을 위한 스크립트**를 작성하였습니다. 이 스크립트는 CPU 사용량, 평균 부하, 프로세스별 CPU 및 메모리 사용량을 주기적으로 기록합니다.

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

<br>

### 2-2. ✍️ logstash.conf 작성
다음은 수집된 로그를 Elasticsearch에 저장하기 위한 **Logstash 구성 파일**입니다.

**logstash.conf**
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
이 구성 파일은 부하 테스트에서 수집된 로그 파일을 읽고, 로그 유형을 구분하여 Elasticsearch에 저장합니다.

<br>

### 2-3. 📈 부하 모니터링 로그 기록

부하를 주었을 때, 다음과 같은 로그를 기록하였습니다.


- **로그 항목**:
    - `cpu_usage`
    - `load_average`
    - `monitor`
    - `pidstat`
<p align="left"><img src="https://github.com/user-attachments/assets/29a2c5f8-91bc-401b-a91e-6917e780bb8d"></p>

각각 cpu_usage, load_average, monitor, pidstat으로 log로 기록하였습니다.
<br>

<br>

### 2-4. 📜 Kibana를 통한 모니터링

**부하 테스트를 통해 시스템의 성능을 평가**하고, 스트레스를 주지 않았을 때와 주었을 때의 시스템 상태를 비교하였습니다. <br> 
ELK 스택을 활용하여 **로그 데이터를 수집하고 시각화하여, 각 상태에서의 성능 차이를 분석**했습니다.

**스트레스를 주지 않았을 때**
<p align="left"><img src="https://github.com/user-attachments/assets/c5427000-630c-487a-9420-22fbb990ed0d"></p>

**스트레스를 줬을 때**
<p align="left"><img src="https://github.com/user-attachments/assets/f1243c9f-924f-45e7-8f60-cdf7adc4b395"></p>

부하 테스트 결과, 스트레스를 주었을 때 **시스템의 CPU 사용률과 메모리 사용량이 급격히 증가**하며 **로드 평균이 높아지는 것**을 확인할 수 있었습니다. 이는 시스템의 성능 저하와 안정성 문제를 야기할 수 있으며, 실시간 모니터링이 중요한 이유를 다시 한 번 확인할 수 있는 계기가 되었습니다. 


## 🤔 기대 효과

1. **효율적인 자원 관리**:  
   실시간 모니터링을 통해 자원 사용 현황을 지속적으로 파악함으로써 불필요한 자원 낭비를 줄일 수 있습니다.

2. **시스템 안정성 향상**:  
   문제가 발생하기 전에 조기에 경고를 받음으로써 시스템의 가용성을 높이고 운영 중단 시간을 최소화할 수 있습니다.

3. **비용 절감**:  
   자원 최적화를 통해 운영 비용을 절감하고, 장애를 사전에 예방하여 발생할 수 있는 손실을 줄일 수 있습니다.

4. **확장성 및 유연성 확보**:  
   향후 시스템 확장 계획 시 필요한 데이터 분석과 리소스 할당이 가능해져 유연한 운영이 가능해집니다.

<br>

## 🌱 결론

이번 프로젝트에서는 Linux 명령어(pidstat, mpstat, uptime 등)를 활용하여 시스템에 부하를 가한 상태와 그렇지 않은 상태에서 로그를 수집하고, ELK 스택(Elasticsearch, Logstash, Kibana)을 통해 이를 분석하고 시각화하였습니다.

1. **실시간 데이터 수집**:  
   Logstash로 성능 데이터를 즉시 수집하고, Elasticsearch에 저장하여 부하 발생 시 시스템 변화 패턴을 신속히 파악할 수 있었습니다.

2. **효율적인 시각화**:  
   Kibana 대시보드를 통해 CPU, 메모리, I/O 성능을 직관적으로 모니터링하고, 잠재적 문제를 쉽게 식별할 수 있었습니다.

3. **문제 해결 시간 단축**:  
   성능 저하를 즉시 감지하고, Kibana를 활용해 원인을 신속하게 분석하여 문제 해결 시간을 단축했습니다.

<br>

## 😂 아쉬운 점

1. **모니터링 한계**:  
   부하 테스트의 범위가 제한적이어서, 다양한 상황에서의 시스템 반응을 충분히 평가하지 못했습니다.

2. **시각화 다양성 부족**:  
   Kibana에서 제공하는 시각화 옵션이 제한적이어서, 더 다양한 분석을 위한 추가적인 커스터마이징이 필요하다고 느꼈습니다.

3. **자동화 미비**:  
   로그 수집 및 분석 과정에서 자동화가 부족해 수동 작업이 필요했으며, 향후 자동화 도구 도입이 필요할 것으로 보입니다.

<br>
