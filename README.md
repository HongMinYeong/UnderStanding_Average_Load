# Load Average 및 시스템 부하 관리

**Load Average**는 리눅스 시스템의 CPU와 디스크 I/O 부하를 나타내는 지표로, 1분, 5분, 15분의 평균값으로 표현됩니다. 이는 시스템에 얼마나 많은 활성 프로세스가 있는지를 보여줍니다.

## 1. 정의
Load Average는 단위 시간 동안 실행 가능 또는 중단 불가능한 상태의 프로세스 평균 수를 의미하며, CPU 사용률과는 직접적으로 연관이 없습니다.

예를 들어, Load Average 0.63은 CPU 사용률이 63%라는 뜻이라고 생각할 수 있지만, 사실 그렇지 않습니다. 
간단히 말해서,  Load Average는 단위 시간 동안 실행 가능 또는 중단 불가능한 상태의 프로세스의 평균 수를 말합니다. 

이는 활성 프로세스의 평균 수라고도 하며 CPU 사용률과 직접 관련이 없습니다.

## 2. 프로세스 상태
- **실행 가능한 프로세스**: CPU를 사용 중이거나 기다리는 프로세스.
- **중단 불가능 프로세스**: 중요한 커널 프로세스 등.

## 3. Load Average 해석
- Load Average가 1이면, CPU 코어 1개가 100% 사용 중임을 의미합니다.
- Load Average가 4라면, CPU 코어 4개가 100% 사용 중이거나, CPU 코어 1개가 400% 사용 중일 수 있습니다.

## 4. 부하 관리
- Load Average가 지속적으로 높다면, 시스템 성능을 향상시키기 위해 리소스 추가 또는 프로세스 최적화가 필요합니다.
- 부하가 높은 프로세스를 찾아 중지하거나, 하드웨어 리소스를 추가하는 등의 대응 방법을 고려할 수 있습니다.

이러한 이해를 바탕으로 Load Average를 통해 시스템의 부하를 효과적으로 관리할 수 있습니다.

---

## Load Average 및 시스템 부하 관리

**Load Average**는 활성 프로세스의 평균 수를 나타내며, 이를 통해 시스템 부하를 평가할 수 있습니다. 평균 부하 값이 2일 때의 의미는 다음과 같습니다:

- **CPU 2개**: 모든 CPU가 완전히 사용 중임.
- **CPU 4개**: 50%의 유휴 CPU 용량이 존재.
- **CPU 1개**: 프로세스의 절반이 CPU 시간을 놓고 경쟁 중임.

### 부하 평가

부하를 평가할 때 가장 먼저 알아야 할 점은 시스템의 CPU 개수입니다. 이를 위해 `top` 명령이나 `/proc/cpuinfo` 파일을 확인할 수 있습니다. 평균 부하가 CPU 개수를 초과할 경우 시스템은 과부하 상태임을 알 수 있습니다.

1. **부하 안정성**:
   - 1분, 5분, 15분의 Load Average 값이 유사하면 시스템 부하가 안정적임을 나타냅니다.
   - 1분 값이 15분 값보다 훨씬 낮다면, 최근 1분 동안 부하가 감소한 것을 의미하며, 반대로 높다면 최근 부하가 증가한 것입니다. 이 경우, 지속적인 관찰이 필요합니다.

2. **과부하 확인**:
   - 1분 평균 부하가 CPU 수에 근접하거나 초과할 경우, 과부하 상태임을 나타냅니다. 이럴 때는 원인을 분석하고 최적화를 고려해야 합니다.

### 평균 부하 vs CPU 사용률

**평균 부하**는 활성 프로세스 수를 나타내지만, CPU 사용률과 혼동할 수 있습니다. 평균 부하는 실행 가능하거나 I/O를 기다리는 프로세스 수를 포함합니다. 반면, CPU 사용률은 CPU가 얼마나 바쁜지를 나타내는 통계로, 반드시 평균 부하와 일치하지 않습니다.

부하 증가의 근본 원인을 파악하기 위해 `iostat`, `mpstat`, `pidstat` 등의 도구를 사용할 수 있습니다.

### 예시 환경

- **기본 시스템**: Ubuntu 18.04
- **하드웨어 구성**: CPU 2개, RAM 8GB
- **필요 패키지**: `stress`, `sysstat` (설치 명령: `apt install stress sysstat`)

이러한 도구와 정보를 통해 Load Average를 효과적으로 모니터링하고 시스템 성능을 최적화할 수 있습니다.


## 실습 준비

실습을 위해 필요한 패키지를 설치합니다.
```bash
sudo apt-get install stress sysstat
```

##  실습 시나리오: 간단하고 직관적인 테스트

### 1. 현재 시스템 상태 확인

먼저, 현재 시스템의 평균 부하와 CPU 개수를 확인합니다.
```bash
uptime
grep 'model name' /proc/cpuinfo | wc -l
```

### 2. CPU 및 I/O 부하 테스트
CPU와 I/O에 부하를 주면서 평균 부하가 어떻게 변화하는지 확인합니다.

1. **CPU 부하 테스트**
```bash
stress --cpu 2 --timeout 60
```

- 2개의 CPU를 60초 동안 100% 사용하도록 부하를 줍니다.
- 평균 부하 모니터링: 실시간으로 평균 부하를 확인합니다.
```bash
watch -n 1 uptime
```

2. **I/O 부하 테스트**
```bash
stress --io 4 --timeout 60
```
- I/O 작업을 통해 디스크에 부하를 줍니다.
- 평균 부하 모니터링: 실시간으로 평균 부하와 I/O 대기 시간을 확인합니다.
```bash
watch -n 1 uptime
```

### 3. 실시간 성능 분석

`mpstat`와 `pidstat`를 사용하여, CPU 사용률 및 프로세스 성능을 실시간으로 분석합니다.

1. **CPU 사용률 확인**
```bash
mpstat -P ALL 1
```
2. **프로세스 분석**
```bash
pidstat -u 1
```

### 4. 간단한 자동화 테스트 스크립트 작성

이 과정을 자동화하기 위한 간단한 Bash 스크립트를 작성해볼 수 있습니다. 아래 스크립트는 CPU와 I/O에 부하를 주고, 평균 부하와 성능을 모니터링합니다.
```bash
#!/bin/bash

echo "Starting CPU load test..."
stress --cpu 2 --timeout 60 &

echo "Monitoring load average..."
watch -n 1 uptime &

sleep 60

echo "Starting I/O load test..."
stress --io 4 --timeout 60 &

sleep 60

echo "Monitoring CPU and process performance..."
mpstat -P ALL 1 &
pidstat -u 1
```
이 스크립트를 실행하면, 평균 부하가 실시간으로 어떻게 변화하는지 한눈에 확인할 수 있습니다.
