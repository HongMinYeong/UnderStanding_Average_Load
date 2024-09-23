# ⚙️ 리눅스 시스템의 평균 부하 이해 및 실습

## 💡 개요

이 프로젝트는 "Deep Understanding of Average Load in Linux" 블로그 포스트를 참고하여, 리눅스 시스템의 평균 부하(Average Load)를 이해하고 테스트하는 방법을 다룹니다.

서버 부하 테스트를 통해 예상되는 트래픽이나 작업량을 서버가 충분히 처리할 수 있는지 사전에 확인하고, 실제 운영 중 발생할 수 있는 문제를 예방하려고 했습니다.

또한, 평균 부하를 모니터링하고 분석할 수 있는 실습 예제를 포함하고 있어, 시스템 관리와 확장 계획 수립에 유용한 자료가 될 것입니다.

<br>

## 🔎 탐구 인원

| <img src="https://avatars.githubusercontent.com/u/65701100?v=4" width="150" height="150"/> | <img src="https://avatars.githubusercontent.com/u/86272865?v=4" width="150" height="150"/> |
| :----------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------: |
|              **홍민영** <br/>[@HongMinYeong](https://github.com/HongMinYeong)              |                  **최수연**<br/>[@lotuxsoo](https://github.com/lotuxsoo)                   |

---

리눅스 시스템의 성능을 모니터링할 때 `uptime` 명령어를 통해 표시되는 "평균 부하(Average Load)"는 매우 중요한 지표입니다. 평균 부하는 단위 시간(1분, 5분, 15분) 동안 CPU가 처리해야 하는 활성 프로세스의 평균 수를 나타냅니다. 이를 통해 시스템의 상태를 파악하고, 필요한 경우 성능 개선 조치를 취할 수 있습니다.


## 1. 평균 부하(Average Load)란?

평균 부하(Average Load)는 단위 시간 동안 CPU에서 처리 중이거나 처리 대기 중인 프로세스의 평균 수를 의미합니다. 이는 CPU 사용률과는 직접적으로 연관되지 않으며, 시스템의 전체적인 부하 상태를 평가하는 지표로 사용됩니다.

- **CPU 개수와 비교**: 평균 부하 값이 시스템의 CPU 개수와 비슷하면 정상적인 상태이며, 이를 초과하면 시스템이 과부하 상태임을 의미합니다.

예를 들어, 평균 부하 값이 2일 때의 의미는 다음과 같습니다:

- **CPU 2개**: 모든 CPU가 완전히 사용 중임을 의미.
- **CPU 4개**: 50%의 유휴 CPU 용량이 존재.
- **CPU 1개**: 프로세스의 절반이 CPU 시간을 놓고 경쟁 중임을 의미.

## 2. 평균 부하 해석

- **부하 안정성**: 1분, 5분, 15분의 평균 부하 값이 유사하면 시스템 부하가 안정적임을 나타냅니다.
- **부하 변화**: 1분 값이 15분 값보다 훨씬 낮다면, 최근 부하가 감소한 것이며, 반대로 높다면 최근 부하가 증가한 것입니다.
- **과부하 확인**: 1분 평균 부하가 CPU 수에 근접하거나 초과할 경우 과부하 상태를 의미하며, 이때는 원인 분석 및 최적화가 필요합니다.

## 3. 실습: 리눅스 시스템의 평균 부하 테스트

### 1. 실습 준비

실습을 위해 필요한 패키지를 설치합니다.

```bash
sudo apt-get install stress sysstat
```

### 2. 현재 시스템 상태 확인

먼저, 현재 시스템의 평균 부하와 CPU 개수를 확인합니다.

```bash
uptime
grep 'model name' /proc/cpuinfo | wc -l
```

### 3. CPU 및 I/O 부하 테스트

CPU와 I/O에 부하를 주면서 평균 부하가 어떻게 변화하는지 확인합니다.

#### **CPU 부하 테스트**

CPU에 부하를 주고, 평균 부하가 어떻게 변화하는지 관찰합니다.

```bash
stress --cpu 2 --timeout 60
watch -n 1 uptime
```

- 2개의 CPU를 60초 동안 100% 사용하도록 부하를 줍니다.
- 평균 부하 모니터링: 실시간으로 평균 부하를 확인합니다.

#### **I/O 부하 테스트**

I/O 작업을 통해 디스크에 부하를 주고, 평균 부하의 변화를 확인합니다.

```bash
stress --io 4 --timeout 60
watch -n 1 uptime
```

- I/O 작업을 통해 디스크에 부하를 줍니다.
- 평균 부하 모니터링: 실시간으로 평균 부하와 I/O 대기 시간을 확인합니다.

### 4. 실시간 성능 분석

mpstat`와 `pidstat`를 사용하여 CPU 사용률과 프로세스 성능을 실시간으로 분석합니다.

```bash
mpstat -P ALL 1
pidstat -u 1
```

### 5. 자동화 테스트 스크립트 작성

부하 테스트 과정을 자동화하기 위한 간단한 Bash 스크립트를 작성할 수 있습니다.

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

이 스크립트를 실행하면 평균 부하가 실시간으로 어떻게 변화하는지 한눈에 확인할 수 있습니다.
