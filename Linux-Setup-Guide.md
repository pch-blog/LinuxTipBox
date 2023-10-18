## Network 설정
### 1. IP 설정 및 인터페이스 활성화 
- CentOS 또는 RHEL 계열
```shell
# yum install NetworkManager-tui
# nmtui
```
- Debian 또는 Ubuntu 계열
```shell
# apt-get install network-manager
# nmtui
```
- "nmtui" 실행하여 IP 입력 및 인터페이스 활성화 
<br>

## SSH 설정
### 1. 포트 변경 및 root 계정 접속제한
 ```shell
 # vi /etc/ssh/sshd_config
 ```
- root 계정 접속 차단을 위해 "PermitRootLogin"를 no로 설정
- "Port 포트번호"를 원하는 포트로 변경 (주석처리되어 있다면 주석을 풀고 처리)
### 2. SELINUX 및 방화벽 포트 허용
- SELINUX 포트 허용 및 확인
```shell
# semanage port -a -t ssh_port_t -p tcp 1234
# semanage port -l | grep ssh_port_t
```
- 방화벽 포트 허용 및 확인
```shell
# firewall-cmd --permanent --zone=public --add-port=1234/tcp
# fierwall-cmd --reload; firewall-cmd --list-all
```
### 3. 서비스 재시작
```shell
# systemctl restart sshd.service
```
또는
```shell
# service sshd restart
```
<br>

## 사용자 계정 추가
### 1. 계정 추가
```shell
# adduser "계정명"
```
- useradd : 사용자의 홈 디렉토리를 자동으로 생성안함, 모든 설정을 명시
- adduser : 사용자의 홈 디렉토리를 자동으로 생성함
#### 1-1. 계정 삭제
```shell
# userdel -r "계정명"
```
또는
```shell
# deluser --remove-home "계정명"
```
### 2. 계정 패스워드 설정
```shell
# adduser "계정명"
```
### 3. 로그인
```shell
# su - "계정명"
```
- su (Switch User) : 작업 디렉토리 및 환경 변수를 현재 사용자 기준으로 사용자 전환
- su - (Switch User with Environment) : 작업 디렉토리 및 환경 변수를 전환하려는 사용자 기준으로 사용자 전환
<br>
