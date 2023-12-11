# 001-Linux-Check-Guide.md
~~~
LINUX 서버의 정보 및 상태를 확인하기위해 필요한 명령어 리스트
~~~
<br>

## LINUX 제한 설정값 확인 ulimit (user limit)
- soft ulimit
```shell
$ ulimit -a
```
- hard ulimit
```shell
$ ulimit -aH
```
- core file size : 코어파일의 최대크기
- data seg size : 프로세스의 데이터 세그먼트 최대크기
- scheduling priority : 쉘에서 생성되는 파일의 최대 크기
- max memory size : resident set size의 최대 크기(메모리 최대크기)
- open files : 한 프로세스에서 열 수 있는 open file descriptor의 최대 숫자(열수 있는 최대 파일 수)
- pipe size : 512-바이트 블럭의 파이프 크기
- cpu time : 총 누적된 CPU 시간(초)
- max user processes : 단일 유저가 사용가능한 프로세스의 최대 갯수
- virtual memory : 쉘에서 사용가능 한 가상 메모리의 최대 용량
<br>

## LINUX 배포판 확인
```shell
$ cat /etc/os-release
$ cat /etc/redhat-release
```
또는
```shell
$ hostnamectl 
```
<br>

## LINUX 커널 버전 확인
```shell
$ uname -a
```
<br>

## 메모리 확인
```shell
$ cat /proc/meminfo | grep MemTotal
```
또는
```shell
$ free -h
```
- [total] : 설치된 총 메모리 크기 / 설정된 스왑 총 크기
- [used] : total에서 free, buff/cache를 뺀 사용중인 메모리. / 사용중인 스왑 크기
- [free] : total에서 used와 buff/cahce를 뺀 실제 사용 가능한 여유 있는 메모리량 / 사용되지 않은 스왑 크기
- [shared] : tmpfs(메모리 파일 시스템), ramfs 등으로 사용되는 메모리. 여러 프로세스에서 사용할 수 있는 공유 메모리
- [buffers] : 커널 버퍼로 사용중인 메모리
- [cache] : 페이지 캐시와 slab으로 사용중인 메모리
- [buff/cache] : 버퍼와 캐시를 더한 사용중인 메모리
- [available] : swapping 없이 새로운 프로세스에서 할당 가능한 메모리의 예상 크기. (예전의 -/+ buffers/cache이 사라지고 새로 생긴 컬럼)
<br>

## 디스크 확인 
```shell
$ df -h
```
<br>

## 파일 및 디렉토리 용량 확인
```shell
$ du -h "파일"
$ du -hs "디렉토리" 
```
<br><br>
