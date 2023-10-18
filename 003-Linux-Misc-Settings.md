~~~
LINUX의 기타 설정을 위한 페이지 입니다. 
~~~

## 시스템 전체 암호화 정책을 이전 릴리스와 호환되는 모드로 전환
- XShell 구버전을 사용하여 LINUX 서버에 SSH 로그인시 "key exchange" 오류
- RHEL, CentOS 8 이후부터 변경
```shell
# update-crypto-policies --set LEGACY
```
- https://access.redhat.com/documentation/ko-kr/red_hat_enterprise_linux/8/html/security_hardening/switching-the-system-wide-crypto-policy-to-mode-compatible-with-previous-systems_using-the-system-wide-cryptographic-policies
<br>
