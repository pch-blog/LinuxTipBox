# 000-Linux-Setup-Guide.md
- CentOS 또는 RHEL 계열의 LINUX 배포판 설치 후 기본 설정 리스트
- Rocky Linux 8.8, Rocky Linux 9.2 설치 후 설정 과정을 정리한 내용임
<br><br>

## Network 설정
- "nmtui"로 IP 설정 및 인터페이스 활성화
```shell
$ sudo nmtui
```
- "nmtui"가 없을 경우 설치
```shell
$ sudo yum install NetworkManager-tui
# OR
$ sudo dnf install NetworkManager-tui
```
<br>

## 설치된 소프트웨어 업데이트
```shell
$ sudo yum update
# OR
$ sudo dnf update
```
<br>

## 언어 인코딩 설정
### 1. 한글 언어 패키지 설치
```shell
$ sudo yum install glibc-langpack-ko
```
### 2. ko_KR.euckr 언어팩 확인
```shell
$ locale -a | gpre ko
$ localectl list-locales | grep ko
```
### 3. 인코딩 설정
```shell
$ sudo localectl set-locale "LANG=ko_KR.euckr"
# 직접 수정
$ sudo vi /etc/locale.conf
```
- 설정 확인
```shell
$ locale
```
### 4. 오류 - Rocky Linux 9, RHEL 9 에서 한글 깨짐 현상
- 특별한 문제가 아니라면 Rocky Linux 8, RHEL 8 사용
- "localectl list-locales | grep ko" 명령어에서 ko_KR.euckr이 안나옴
- gcc로 아래 내용이 포함된 코드를 컴파일하면 오류 발생
```c
#if 0
테이블
#endif
```
- 컴파일 옵션 추가로 해결 가능
```make
-finput-charset=EUC-KR
-fexec-charset=EUC-KR
```
- <b>컴파일 옵션 추가하면 postgresql의 ecpg 컴파일에 인코딩 변환 문제 발생</b>

<br>

## 시간 동기화 (rdate 사용X)
### 1. TIMEZONE 확인 및 변경
```shell
$ sudo timedatectl
$ sudo timedatectl set-timezone Asia/Seoul
```
### 2. NTP 서버 변경
```shell
$ sudo vi /etc/chrony.conf
```
- "pool kr.pool.ntp.org iburst" 추가 후 저장
### 3. 서비스 재시작
```shell
// chrondy 허용
$ sudo systemctl enable chronyd

// chrondy 재시작
$ sudo systemctl stop chronyd
$ sudo systemctl start chronyd
```
### 4. 메인보드 시간 동기화
```shell
$ sudo hwclock -w
$ sudo hwclock -v
```
<br>

## SSH 설정
### 1. 포트 변경 및 root 계정 접속제한
 ```shell
 $ sudo vi /etc/ssh/sshd_config
 ```
- root 계정 접속 차단을 위해 "PermitRootLogin"를 no로 설정
- "Port 포트번호"를 원하는 포트로 변경 (주석처리되어 있다면 주석을 풀고 처리)
### 2. SELINUX 및 방화벽 포트 허용
- SELINUX 포트 허용 및 확인
```shell
$ sudo semanage port -a -t ssh_port_t -p tcp 1234
$ sudo semanage port -l | grep ssh_port_t
```
- 방화벽 포트 허용 및 확인
```shell
$ sudo firewall-cmd --permanent --zone=public --add-port=1234/tcp
$ sudo firewall-cmd --reload; firewall-cmd --list-all
```
### 3. 서비스 재시작
```shell
$ sudo systemctl restart sshd.service
# OR
$ sudo service sshd restart
```
<br>

## 사용자 계정 추가
### 1. 계정 추가
```shell
$ sudo adduser "계정명"
```
- useradd : 사용자의 홈 디렉토리를 자동으로 생성안함, 모든 설정을 명시
- adduser : 사용자의 홈 디렉토리를 자동으로 생성함
### 1-1. 계정 삭제
```shell
$ sudo userdel -r "계정명"
```
또는
```shell
$ sudo deluser --remove-home "계정명"
```
### 2. 계정 패스워드 설정
```shell
$ sudo passwd "계정명"
```
### 3. 로그인
```shell
$ su - "계정명"
```
- su (Switch User) : 작업 디렉토리 및 환경 변수를 현재 사용자 기준으로 사용자 전환
- su - (Switch User with Environment) : 작업 디렉토리 및 환경 변수를 전환하려는 사용자 기준으로 사용자 전환
<br>

## 호스트명 변경 및 확인
- 기준 및 필요에 따라 호스트명 지정
```shell
$ sudo hostnamectl set-hostname "호스트명"
$ hostname
```
<br>
