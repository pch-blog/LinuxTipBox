# 004-Linux-Sys-Parameter-Setup.md
<br><br>

## ulimit : Open Files 설정
- unlimited로 설정하면 부팅 불가, SSH 연결 불가, 연결 상태의 터미널에서 다른 계정 로그인 불가(root 포함)
- 즉시 설정
```
# ulimit -SHn 102400
```
- 영구 설정
```
# /etc/security/limits.conf
```
- nofile에 대한 값 추가
```
#<domain>       <type>  <item>         <value>
*                soft    nofile          102400
*                hard    nofile          102400
```
- 저장 후 터미널 재접속
<br>

## ulimit : Max User Processes 설정1
- 즉시 설정
```
# ulimit -SHu unlimited
```
- 영구 설정
```
# /etc/security/limits.conf
```
- nproc에 대한 값 추가
```
#<domain>       <type>  <item>         <value>
*                soft    nproc         unlimited
*                hard    nproc         unlimited
```
- 저장 후 터미널 재접속
<br>

## ulimit : Max User Processes 설정2
- 설정1로 변경이 안될 경우
```
# /etc/security/limits.d/20-nproc.conf
```
- nproc에 대한 값 변경
```
*          soft    nproc     unlimited
root       soft    nproc     unlimited
```
- 저장 후 터미널 재접속
<br>
