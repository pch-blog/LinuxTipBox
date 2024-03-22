# NATS.io Summary
- [Official NATS Documentation](https://docs.nats.io)<br>
- [Legacy NATS Documentation](https://nats-io.gitbook.io/legacy-nats-docs/nats-account-server)<br>
- [NATS.io 서버 설치 및 환경설정](#natsio-서버-설치-및-환경설정)<br>
- [NATS.io C클라이언트 설치 및 개발 관련 설명](#natsio-c클라이언트-설치-및-개발-관련-설명)<br>
- [NATS.io 클러스터링](#natsio-클러스터링)<br>
<br><br>

## NATS.io 서버 설치 및 환경설정
### NATS.io 필요한 버전 RPM 다운로드 및 설치
```shell
# RPM 다운로드 및 설치
$ wget https://github.com/nats-io/nats-server/releases/download/v2.10.7/nats-server-v2.10.7-amd64.rpm
$ rpm -i nats-server-v2.10.7-amd64.rpm
# 위치 확인
$ which nats-server
/usr/local/bin/nats-server
```
### NATS.io 서버 환경설정
- [Configuring NATS Server](https://docs.nats.io/running-a-nats-service/configuration)<br>
- 필요한 설정값을 추가하여 환경설정 파일을 만든다.
- PORT 설정
```shell
# nats-server의 기본 포트
port: 4222
# nats-server의 모니터링 포트
http_port: 8222
```
- LOG 설정
```shell
# 로그파일 위치
log_file: "/home/xt_ebest_linux/log/nats/nats-server.log"
# 로그 시간 표시 여부
logtime: true | false
# 로그 파일 사이즈 설정
logfile_size_limit: 1G
```
- DEBUG 설정
```shell
# DEBUG 수준의 로그 설정
debug: true | false
```
- 환경설정 파일 사용
```shell
# 환경설정 파일 테스트
$ nats-server -t "환경설정 파일"
# 실행
$ nats-server -c "환경설정 파일"
```
<br>

## NATS.io C클라이언트 설치 및 개발 관련 설명
### nats.c 라이브러리 설치
- 간단하게 사용하기 위해 설치한 과정이며 추가 설정, 설명이 필요한 경우 [nats.c README](https://github.com/nats-io/nats.c/blob/main/README.md) 확인
- 상황에 따라 cmake에 옵션 추가
```shell
# TLS 사용여부 (default - ON)
-DNATS_BUILD_WITH_TLS=OFF | ON

# Streaming 사용여부
-DNATS_BUILD_STREAMING=OFF | ON
# 기본 라이브러리 경로에서 찾지 못하면 libprotobuf-c 라이브러리 디렉토리 경로 지정
-DNATS_PROTOBUF_DIR=
# 정적 라이브러리를 찾지 못하면 libprotobuf-c .so 라이브러리 경로 지정
-DNATS_PROTOBUF_LIBRARY=
# protobuf-c의 헤더파일 디렉토리 경로 추가
-DNATS_PROTOBUF_INCLUDE_DIR=
```
### TLS OFF, Streaming OFF 상태 설치
- nats.io 설치
```shell
$ git clone https://github.com/nats-io/nats.c.git
$ cd nats.c
$ mkdir build; cd build
$ cmake .. -DNATS_BUILD_WITH_TLS=OFF -DNATS_BUILD_STREAMING=OFF
$ sudo make install
```
### TLS ON, Streaming ON  상태 설치
- CentOS 8, RHEL 8.9 설치
- <span style="color:red"><b>CentOS 7 기준 protobuf-c 버전이 낮아서 nats.c 컴파일에 문제가 있음</b></span>
- <span style="color:red"><b>CentOS 7 기준 nats가 요구하는 protobuf-c 최소 요구 버전 및 최신 버전 컴파일에 문제가 있음 </b></span>
- 필요한 패키지 설치 및 업데이트
```shell
$ sudo yum -y install protobuf protobuf-c protobuf-c-devel
# OR
$ sudo yum -y update protobuf protobuf-c protobuf-c-devel
```
- Rocky 9.2 기준 "protobuf-c-devel" 패키지는 안나옴 ([오류2](#오류2-protobuf-c-헤더를-못-찾는-경우))
- nats.io 설치
```shell
$ git clone https://github.com/nats-io/nats.c.git
$ cd nats.c
$ mkdir build; cd build
$ cmake .. -DNATS_BUILD_STREAMING=ON
$ sudo make install
```
- 컴파일 오류 내용에 따라 cmake 옵션 추가
```shell
$ cmake .. -DNATS_BUILD_STREAMING=ON \
-DNATS_PROTOBUF_DIR=/usr/lib64 \
-DNATS_PROTOBUF_LIBRARY=/usr/lib64/libprotobuf-c.so \
-DNATS_PROTOBUF_INCLUDE_DIR=/usr/include/protobuf-c
```
### 오류1. CMAKE(Cross-platform make system) 버전이 낮으면 버전 업데이트 진행
- CentOS 7, RHEL 7.9
```shell
$ wget https://github.com/Kitware/CMake/releases/download/v3.26.5/cmake-3.26.5.tar.gz
$ tar -zxvf cmake-3.26.5.tar.gz
$ cd cmake-3.26.5
$ ./bootstrap
$ gmake
$ sudo make install
```
- CentOS 8, RHEL 8.9
```shell
$ sudo yum update cmake
```
### 오류2. protobuf-c 헤더를 못 찾는 경우
- Rocky 9.2에서 yum search를 하면 protobuf-c-devel 패키지를 찾을 수 없음.
- github에서 clone 후 cmake로 해당 경로를 지정 해줌
```shell
$ git clone https://github.com/protobuf-c/protobuf-c.git
```
```shell
$ cmake ..  -D....... -DNATS_PROTOBUF_INCLUDE_DIR="github에서 protobuf-c를 clone한 경로"
```
<br>

### 클라이언트 컴파일 Makefile에 추가
```make
# 동적 라이브러리
NATSLIBS = -lnats
# 정적 라이브러리
NATSLIBS = -lnats_static
# Streaming ON
NATSLIBS = -lnats_static -lprotobuf-c
# TLS ON
NATSLIBS = -lnats_static  -lssl -lcrypto
```

## NATS.io C클라이언트 개발 관련 설명
### 클라이언트 개발 기타 설명
- [NATS C Client with JetStream and Streaming support](https://github.com/nats-io/nats.c/blob/main/README.md)
- 전반적으로 스레드가 처리하는 것으로 보이며 "ps -efL | grep 프로세스명" 명령으로 확인하면 총 6개(메인1, 스레드5개) 확인.
- nats-server를 다운 시킨 후 확인하면 스레드 4개로 확인.
- nats-server가 다운된 경우 클라이언트에서는 재연결 시도함. (스레드)
- 재연결 시도하는 동안 발생한 데이터는 버려지나 메세지 발행 함수는 정상을 리턴함.
- 설정된 연결 시도 횟수가 끝나면 메세지 발행 함수에서 에러 리턴.
- natsOptions_SetAllowReconnect 함수를통해 재연결허용을 해제하면 메세지 발행 함수가 바로 에러 리턴
- 재연결 기능을 옵션 설정을 통해 끄고 별도의 재연결 시도 로직을 만들어서 처리

### pthread 관련 처리 (오픈 소스 수정 영역)
- NATS C클라이언트 코드 내에서 pthread_create, 등 phtread 함수 확인
- 메인 스레드만 특정 시그널을 받기 위해 다른 스레드에서 특정 시그널 블럭 시킬 방법 필요

### 연결 함수 차이
- natsConnection_ConnectTo : 기본 옵션 사용
- natsConnection_Connect : 옵션 객체가 NULL이면 natsConnection_ConnectTo 호출 후 옵션 clone

### 기본 옵션 (nats.c/src/opts.h)
```c
#define NATS_OPTS_DEFAULT_MAX_RECONNECT         (60)
#define NATS_OPTS_DEFAULT_TIMEOUT               (2 * 1000)          // 2 seconds
#define NATS_OPTS_DEFAULT_RECONNECT_WAIT        (2 * 1000)          // 2 seconds
#define NATS_OPTS_DEFAULT_PING_INTERVAL         (2 * 60 * 1000)     // 2 minutes
#define NATS_OPTS_DEFAULT_MAX_PING_OUT          (2)
#define NATS_OPTS_DEFAULT_IO_BUF_SIZE           (32 * 1024)         // 32 KB
#define NATS_OPTS_DEFAULT_MAX_PENDING_MSGS      (65536)
#define NATS_OPTS_DEFAULT_RECONNECT_BUF_SIZE    (8 * 1024 * 1024)   // 8 MB
#define NATS_OPTS_DEFAULT_RECONNECT_JITTER      (100)               // 100 ms
#define NATS_OPTS_DEFAULT_RECONNECT_JITTER_TLS  (1000)              // 1 second
```

### 연결 상태 확인 (nats.c/src/status.h)
- natsConnStatus natsConnection_Status(natsConnection *nc)
```c
typedef enum
{
    NATS_CONN_STATUS_DISCONNECTED = 0, ///< The connection has been disconnected
    NATS_CONN_STATUS_CONNECTING,       ///< The connection is in the process or connecting
    NATS_CONN_STATUS_CONNECTED,        ///< The connection is connected
    NATS_CONN_STATUS_CLOSED,           ///< The connection is closed
    NATS_CONN_STATUS_RECONNECTING,     ///< The connection is in the process or reconnecting
    NATS_CONN_STATUS_DRAINING_SUBS,    ///< The connection is draining subscriptions
    NATS_CONN_STATUS_DRAINING_PUBS,    ///< The connection is draining publishers
} natsConnStatus;
```
<br>

## NATS.io 클러스터링
- [NATS Server Clustering](https://docs.nats.io/running-a-nats-service/configuration/clustering)
- seed(main) 환경설정 파일에 내용 추가
```shell
cluster {
  port:클러스터의 포트
}
```
- cluster 환경설정 파일에 내용 추가
```shell
cluster {
  port:클러스터의 포트
  routes = [
    nats://시드의 IP 주소:시드의 클러스터 포트
  ]
}
```