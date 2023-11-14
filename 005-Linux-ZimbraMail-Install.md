# 005-Linux-ZimbraMail-Install.md
~~~
- Zimbra 메일 서버 설치 과정
- OS : Rocky Linux 8.8
- Zimbra Mail Server : Zimbra Collaboration Open Source(zcs-8.8.15)
- 현재 메일 서버에서 새로운 메일 서버로 마이그레이션 진행하지 않고 구축하는 과정정
~~~
<br>

## 설치 조건
- 최소 사양으로 4코어 이상 CPU, 4GB 이상 메모리
- 최소 사양 이하라면 설치 중단 가능성 있음
<br>

## Linux 설치 후 기본 설정
- 기본 설정 : https://github.com/namoo33444/LinuxTipBox/blob/main/000-Linux-Setup-Guide.md
- root 파티션 용량 늘리기 : https://github.com/pch-blog/LinuxTipBox/blob/main/003-Linux-Misc-Settings.md#root-%ED%8C%8C%ED%8B%B0%EC%85%98-%EC%9A%A9%EB%9F%89-%EB%8A%98%EB%A6%AC%EA%B8%B0
- 상황에 따라 /opt 영역에 대한 파티션 생성 및 마운트 
<br>

## 도메인 업체 네임서비스 등록
- 도메인 업체에서 네임서비스에 A타입, MX타입 등록
~~~
HOST                  TYPE   Content
mail.xxxx.co.kr       A      메일 서버 IP주소     => 메일 서버 웹페이지 접속 도메인
                      MX     mail.xxxx.co.kr     => ID@xxxx.co.kr
~~~
- Zimbra 설치시 도메인서버에 A/MX 레코드가 등록되어 있지 않다면설치 중단함.
- 보통 mail.xxxx 는 사용 중 일테니 zmail, 등 다른 주소 사용하여 설치
<br>

## 서버의 HOSTNAME 설정
- HOSTNAME 설정 및 확인
```shell
# hostnamectl set-hostname mail.xxxx.co.kr
# hostname
```
- 호스트 파일 수정
```shell
# vi /etc/hosts
  => IP주소 mail.xxxx.co.kr mail 추가
```
<br>

## 호스트 연결 테스트
- 연결 테스트
```shell
$ host -t a mail.xxxx.co.kr
  => mail.xxxx.co.kr has address xxx.xxx.xxx.xx
$ host -t mx xxxx.co.kr
  => xxxx.co.kr mail is handled by 10 mail.xxxx.co.kr.
```
- "not found: 3(NXDOMAIN)" 오류가 나오면 아직 전파가 안되었거나 DNS 설정 재확인 필요
<br>

## Zimbra Collaboration Open Source 8.8.15 다운로드
```shell
$ wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-8.8.15_GA_4362.RHEL8_64.20220721104405.tgz
$ tar -zxvf zcs-8.8.15_GA_4362.RHEL8_64.20220721104405.tgz
```
<br>

## 설치 및 제거 과정
### 1. 설치 및 설정 과정
- 설치 shell 실행
```shell
# cd zcs-8.8.15_GA_4362.RHEL8_64.20220721104405
# ./install.sh
```
- 대부분 Y 혹 Enter로 진행하며 필요에 따라 기능을 골라서 설치
- 설치 중 도메인 이름 변경 관련하여 설정 필요
```
Change domain name? [Yes] Y 
Create domain: [mail.xxxx.co.kr] xxxx.co.kr
```
- 다음 진행시 관리자 계정 비밀번호 설정 필요
```
Port conflicts detected! - Press Enter/Return key to continue [Enter]
Address unconfigured (**) items  (? - help) 7 
Select, or 'r' for previous menu [r] 4
Password for admin@xxxx.pe.kr (min 6 characters): [sjExTRKsa] [비밀번호 입력]
```
- 설치 마무리
```
*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) a  [설정 적용]
Save configuration data to a file? [Yes] Y  [설정 파일 저장]
Save config in file: [/opt/zimbra/config.33703] [Enter]
Saving config in /opt/zimbra/config.33703...done.
The system will be modified - continue? [No] Y [시스템 수정, 설치 진행]
Setting local config values...done.
Initializing core config...Setting up CA...done.
Notify Zimbra of your installation? [Yes] [Enter]
Moving /tmp/zmsetup.20220926-072118.log to /opt/zimbra/log [세팅 로그]
Configuration complete - press return to exit [Enter] [최종 완료]
```
- 상태 확인
```shell
# su - zimbra
$ zmcontrol status  => 상태 확인
$ zmcontrol -v      => 버전 확인
```
- 메일 웹페이지 : https://mail.xxxx.co.kr
- 관리자 웹페이지 : https://mail.xxxx.co.kr:7071
### 2. 제거
- Zimbra 종료
```shell
$ su - zimbra
$ zmcontrol stop
$ exit
```
- Zimbra 프로세스 확인
```shell
# ps -ef | grep -i zimbra
# kill -9 <pid>
```
- 설치 shell에 인자로 "-u" 입력 후 실행 
```shell
# cd zcs-8.8.15_GA_4362.RHEL8_64.20220721104405
# ./install.sh -u
```
- Zimbra 계정 삭제
```shell
# userdel zimbra
```
- 디렉토리 삭제
```shell
# rm -rf /opt/zimbra
# rm -rf /var/log/*zimbra*
# rm -rf /tmp/*zimbra*
# rm -rf /tmp/hsperfdata*
# rm -rf /tmp/install.*
# rm -rf /tmp/*swatch*
# rm -rf /tmp/log*
```
<br>

## 방화벽
- 방화벽 허용 등록
```shell
# firewall-cmd --permanent --add-port=25/tcp --add-port=587/tcp --add-port=465/tcp --add-port=80/tcp --add-port=443/tcp --add-port=8443/tcp --add-port=7071/tcp --add-port=110/tcp --add-port=995/tcp --add-port=143/tcp --add-port=993/tcp
```
- SSH 포트를 변경했다면 zimbraRemoteManagementPort 항목의 포트도 같은 포트로 변경해야한다.
```shell
# su - zimbra
$ zmprov ms mail.xxxx.co.kr zimbraRemoteManagementPort "포트번호"
$ zmprov getServer mail.xxxx.co.kr | grep zimbraRemoteManagementPort
```
<br>

## - 관리자 페이지 설정
### 1. POP3 설정
- 왼쪽 메뉴 기준으로 "홈 -> 구성 -> 전역설정 -> POP" 이동
- 오른쪽 페이지에 "일반 텍스트 로그인 사용" 체크 후 저장
- 기타 외부 메일에서 POP3 설정으로 정상 로그인 확인
### 2. 자체 인증서 설치
- 왼쪽 메뉴 기준으로 "홈 -> 구성 -> 인증서" 이동
- 오른쪽 페이지에서 mail.xxxx.co.kr 오른쪽 클릭 -> 인증서 설치
- 해당 서버 이름 선택 후 다음
- "자체 서명된 인증서 설치" 선택 후 다음
- "기존 CSR 바꾸기"가 있다면 선택 및 내용 입력 후 다음
```
공통 이름 : mail.xxxx.co.kr
국가 이름 : KR
주/도 : N/A
구/군/시 : N/A
회사 이름 : 회사 이름
부서 : 부서 이름
주체 대체 이름 : mail.xxxx.co.kr
```
- 인증서 유효성 확인 날 : 3650 입력 후 설치
<br>

# 계정 백업 및 로드

### 1. 현재 메일 서버에서 계정 정보 백업
- 백업 디렉토리 생성
```shell
# mkdir -p /migration/zimbra
# chmod -R 777 /migration/zimbra
# chown -R zimbra:zimbra /migration/zimbra
# su - zimbra
```
- 계정 정보 저장
```shell
$ mkdir -p  /migration/zimbra/accounts
$ cd /migration/zimbra/accounts
$ zmprov -l gaa | tee -a users.txt
```
- 계정 패스워드 정보 저장
```shell
$ mkdir -p /migration/zimbra/passwords
$ cd /migration/zimbra/passwords
$ for user in `cat ../accounts/users.txt`; do zmprov -l ga $user userPassword | grep userPassword: | awk '{ print $2}' | tee -a $user.shadow; done
```

### 2. 새로운 메일 서버에 계정 정보 로드
- 백업 디렉토리 생성
```shell
# mkdir -p /migration/zimbra
# chmod -R 777 /migration/zimbra
# chown -R zimbra:zimbra /migration/zimbra
# su - zimbra
```
- 백업한 현재 메일 서버 계정 정보 복사
```shell
$ cd /migration/zimbra
$ scp -rp zimbra@xxx.xxx.xxx.xxx:/migration/zimbra/* . (-P 포트번호)
```
- 계정 정보 로드 shell 생성
```shell
$ mkdir -p /migration/zimbra/scripts
$ cd /migration/zimbra/scripts
$ vi restore_accounts.sh
==================================================================================
#!/bin/bash
PASSWDS="../passwords"
ACCOUNT_DETAILS="../account_details"
USERS="../accounts/users.txt"
for i in `cat $USERS`
   do
givenName=$(grep givenName: $ACCOUNT_DETAILS/$i.txt | cut -d ":" -f2)
displayName=$(grep displayName: $ACCOUNT_DETAILS/$i.txt | cut -d ":" -f2)
shadowpass=$(cat $PASSWDS/$i.shadow)
zmprov ca $i "TeMpPa55^()" cn "$givenName" displayName "$displayName" givenName "$givenName"
zmprov ma $i userPassword "$shadowpass"
   done
==================================================================================
```
<br>

## 재부팅 후 zimbra 실행 안되는 경우 (완벽한 해결법은 아닌듯)
- "/var/log/messages"나 "/var/log/secure"에서 아래 내용 확인 (grep 등 활용)
```shell
/etc/rc.d/init.d/zimbra: line 41: /usr/bin/su: Permission denied
```
- SELINUX를 disabled 설정 후 재부팅해서 zimbra 정상 실행 확인
```shell
# vi /etc/selinux/config
SELINUX=disabled
```
- SELINUX 다시 활성화 후 재부팅하여 zimbra 정상 실행 확인
```shell
# vi /etc/selinux/config
SELINUX=enforcing
```
<br>

## 참고
- 설치과정 : https://foxydog.tistory.com/132
- 계정백업 과정 : https://blog.renu.ac.ug/index.php/2021/01/24/migrating-from-zimbra-to-zimbra-zcs-to-zcs/
- Zimbra 문서 : https://zimbra.github.io/installguides/8.8.15/single.html#introduction
<br>

## 서버 이관 과정
- 기존 DNS(A Type)를 새로운 메일 서버 IP로 변경
- 구 메일 서버에 접속 및 유지를 위해 DNS(A Type) 추가 (ex. mail2.xxxx.co.kr, 등)
- 새로운 메일 서버와 구 메일 서버에서 zimbra 중지 및 확인
```shell
$ su - zimbra
$ zmcontrol stop
$ ps -ef | grep -i zimbra
$ kill -9 <pid>
```
- 각 서버의 변경된 도메인으로 호스트 파일 수정
```shell
# vi /etc/hosts
  => IP주소 mail.xxxx.co.kr mail
```
- 각 서버의 HOSTNAME 변경
```shell
# hostnamectl set-hostname mail.xxxx.co.kr
# hostname
```
- Zimbra의 HOSTNAME 변경
```shell
# zmhostname mail.xxxx.co.kr
```
- zimbra 시작
```shell
$ su - zimbra
$ zmcontrol start
```
- 웹페이지 접속 확인
- 자체 인증서 관리자 페이지에서 다시 인스톨
<br>

## 각각 사용자의 로컬PC를 통한 백업 및 로드 과정
### 1. 내보내기
- 구 메일서버 웹페이지 접속 
- 상단 메뉴 "기본 설정" -> 왼쪽 메뉴 "가져오기/내보내기"
- 오른쪽 페이지 내보내기에서 "내보내기" 클릭 후 다운로드 및 완료 확인
### 2. 가져오기
- 새로운 메일서버 웹페이지 접속
- 상단 메뉴 "기본 설정" -> 왼쪽 메뉴 "가져오기/내보내기"
- 오른쪽 페이지 가져오기에서 내보내기로 다운로드 받은 파일 선택 후 "가져오기" 클릭
- 완료 팝업 확인

