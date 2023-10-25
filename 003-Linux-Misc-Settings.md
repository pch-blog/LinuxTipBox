~~~
기타 설정
~~~
<br>

## 시스템 전체 암호화 정책을 이전 릴리스와 호환되는 모드로 전환
- XShell 구버전을 사용하여 LINUX 서버에 SSH 로그인시 "key exchange" 오류
- RHEL 8, CentOS 8 이후부터 변경
- 터미널 프로그램을 최신 버전 혹은 대응되는 버전으로 업그레이드 권장
```shell
# update-crypto-policies --set LEGACY
```
- https://access.redhat.com/documentation/ko-kr/red_hat_enterprise_linux/8/html/security_hardening/switching-the-system-wide-crypto-policy-to-mode-compatible-with-previous-systems_using-the-system-wide-cryptographic-policies
<br>

## root 파티션 용량 늘리기
- RHEL 7이나 CentOS 7 이상부터 기본 설치시 root와 home 영역에 대해 별도의 파티션으로 나누어 설치함.
- 여러 패키지들이 설치 위치를 지정할 수 있지만 기본 설치 위치가 root 영역에 설치 후 임시파일, 로그, 등을 저장하기 때문에 용량 부족 현상 발생
- home 영역의 파티션을 줄이거나 삭제 후 root 영역 확장하는 방식으로 해결
- 참고링크1 : https://nakanara.tistory.com/261
- LVM 상태 확인
```shell
# lvdisplay
```
- 작업 전 홈 디렉토리 위치가 /home 사용하는 계정 모두 로그아웃 및 재부팅이 필요할 수 있음
### 1. 축소 및 확장
- XFS 파일시스템이면 불가능
```shell
# umount /home
# lvreduce -r -L 100G /dev/mapper/rl-home 
# lvresize -r -l+100%FREE /dev/mapper/rl-root
# mount /dev/mapper/rl-home /home
# df -h
```
### 2. 불륨 삭제 후 새로 생성
- 필요한 경우 홈 디렉토리 백업
```shell
# tar -zcvf /home.tar.gz -C /home 
```
- 불륨 삭제
```shell
# umount /dev/mapper/rl-home
# lvremove /dev/mapper/rl-home
```
- 불륨 생성, 포맷, 마운
```shell
# lvcreate -L 100G
# mkfs.xfs /dev/mapper/rl-home
# mount /dev/mapper/rl-home /home
```
- 남은 용량 root 파티션 확장
```shell
# lvextend -r -l +100%FREE /dev/mapper/rl-root
# df -h
```
- 백업시 /home 디렉토리 복구
```shell
#  tar -zxvf /home.tar.gz -C /home
```
<br>

## 리눅스 서버 웹 콘솔 사용
```shell
# systemctl enable --now cockpit.socket
# systemctl disable --now cockpit.socket
```
- https://localhost:9090 또는 https://"IP":9090 로 접속
- 참고링크1 : https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_systems_using_the_rhel_8_web_console/getting-started-with-the-rhel-8-web-console_system-management-using-the-rhel-8-web-console#logging-in-to-the-web-console_getting-started-with-the-rhel-8-web-console
<br>
