~~~
LINUX의 기타 설정을 위한 페이지 입니다. 
~~~
<br>

## 시스템 전체 암호화 정책을 이전 릴리스와 호환되는 모드로 전환
- XShell 구버전을 사용하여 LINUX 서버에 SSH 로그인시 "key exchange" 오류
- RHEL, CentOS 8 이후부터 변경
```shell
# update-crypto-policies --set LEGACY
```
- https://access.redhat.com/documentation/ko-kr/red_hat_enterprise_linux/8/html/security_hardening/switching-the-system-wide-crypto-policy-to-mode-compatible-with-previous-systems_using-the-system-wide-cryptographic-policies
<br>

## root 파티션 용량 늘리기
- CentOS 7 이상부터 기본 설치시 root와 home 영역에 대해 별도의 파티션으로 root 영역을 50~70G 정도로 잡고 나머지를 home 영역으로 설치함.
- 여러 패키지들이 기본적으로 root 영역에 설치 후 임시파일, 등 쓰거나 읽기를 root 영역에 하기 때문에 용량 부족 현상 발생할 수 있음.
- home 영역의 파티션을 줄이거나 삭제 후 root 영역 확장하는 방식으로 해결
- 참고링크1 : https://nakanara.tistory.com/261
- 참고링크2 : https://joonyon.tistory.com/entry/CentOS7-LVM-home-%ED%81%AC%EA%B8%B0-%EB%8C%80%EC%8B%A0-root-%ED%81%AC%EA%B8%B0-%EB%8A%98%EB%A6%AC%EA%B8%B0
  
