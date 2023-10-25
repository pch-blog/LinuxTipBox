~~~
- Zimbra 메일 서버 설치 과정
- OS : Rocky Linux 8.8
- Zimbra Mail Server : Zimbra Collaboration Open Source(zcs-8.8.15)
- 설치과정 참고 : https://foxydog.tistory.com/132
- 계정백업 과정 : https://blog.renu.ac.ug/index.php/2021/01/24/migrating-from-zimbra-to-zimbra-zcs-to-zcs/
- Zimbra 문서 : https://zimbra.github.io/installguides/8.8.15/single.html#introduction
~~~
<br>

## 설치 조건
- 최소 사양으로 4코어 이상 CPU, 4GB 이상 메모리
- 최소 사양 이하라면 설치 중단 가능성 있음

## Linux 설치 후 기본 설정
- https://github.com/namoo33444/LinuxTipBox/blob/main/000-Linux-Setup-Guide.md

## root 파티션 용량 늘리기
- https://github.com/namoo33444/LinuxTipBox/blob/main/003-Linux-Misc-Settings.md#root-%ED%8C%8C%ED%8B%B0%EC%85%98-%EC%9A%A9%EB%9F%89-%EB%8A%98%EB%A6%AC%EA%B8%B0

## 도메인 업체 네임서비스 등록 및 확인
- 사용 중 혹은 구매하는 도메인 업체에서 네임서비스에 A타입, MX타입 등록
~~~
HOST                  TYPE   Content
mail.xxxx.co.kr       A      메일 서버 IP주소     => 메일 서버 웹페이지 접속 도메인
                      MX     mail.xxxx.co.kr     => ID@xxxx.co.kr
~~~
- Zimbra 설치시 도메인서버에 A/MX 레코드가 등록되어 있지 않다면설치 중단함.
- 도메인 확인
```shell
$ host -t a mail.xxxx.co.kr
  => mail.xxxx.co.kr has address xxx.xxx.xxx.xx
$ host -t mx xxxx.co.kr
  => xxxx.co.kr mail is handled by 10 mail.xxxx.co.kr.
```

