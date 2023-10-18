### LINUX 배포판 확인
```shell
$ cat /etc/os-release
```
또는
```shell
$ hostnamectl 
```
### LINUX 커널 버전 확인
```shell
$ uname -a
```
### 메모리 확인
```shell
$ cat /proc/meminfo | grep MemTotal
```
또는
```shell
$ free -h
```
### 디스크 확인 
```shell
$ df -h
```
### 파일 및 디렉토리 용량 확인
```shell
$ du -h "파일"
$ du -hs "디렉토리" 
```
