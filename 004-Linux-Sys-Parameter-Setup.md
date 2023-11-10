# 004-Linux-Sys-Parameter-Setup.md
<br>

## Open Files 설정 
- 즉시 설정
```shell
# ulimit -SHn 65535
```
- 영구 설정
```shell
# /etc/security/limits.conf
```
- 내용 추가
```shell
*                soft    nofile          102400
*                hard    nofile          102400
```
<br>

