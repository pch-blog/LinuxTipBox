### LINUX 배포판 확인
```shell
$ cat /etc/os-release
```
또는
```shell
$ hostnamectl 
```
  <br>
### LINUX 커널 버전 확인
```shell
$ uname -a
```
<br>
### 메모리 확인
```shell
$ cat /proc/meminfo | grep MemTotal
```
또는
```shell
$ free -h
```
<br>
### 디스크 확인 
```shell
$ df -h
```
<br>
### 파일 및 디렉토리 용량 확인
```shell
$ du -h "파일"
$ du -hs "디렉토리" 
```
<br><br/>
