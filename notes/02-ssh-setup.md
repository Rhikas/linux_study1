# 02. SSH 접속

## 목표
Windows PowerShell에서 VirtualBox의 Rocky Linux VM으로 SSH 원격 접속한다.

## 개념
- **SSH**: 네트워크를 통해 서버에 원격으로 접속해 명령어를 실행하는 방식
- **포트 포워딩**: Windows의 2222번 포트로 들어온 요청을 VM의 22번(SSH) 포트로 전달해 주는 설정
- VM이 NAT 네트워크라서 VM의 실제 IP(10.0.2.15)가 아니라 `127.0.0.1`의 `2222` 포트로 접속한다.

## 설정 과정
1. VirtualBox 설정 > 네트워크 > 어댑터 1 > 고급 > 포트 포워딩에 규칙 추가 (TCP, 호스트 포트 2222, 게스트 포트 22)
2. VM 안에서 sshd 상태 확인: `sudo systemctl status sshd` (active (running), enabled)
3. Windows PowerShell에서 `ssh -p 2222 rhikas@127.0.0.1` 입력
4. 첫 접속 시 호스트 키 확인 질문에 `yes`, 계정 비밀번호 입력
5. 접속 후 `whoami`, `hostname`, `cat /etc/os-release`로 확인하고 `exit`로 종료

## Windows와 VM 위치 구분
| 구분 | Windows | VM (리눅스) |
|---|---|---|
| 프롬프트 | `PS D:\dev\linux_study1>` | `[rhikas@localhost ~]$` |
| 경로 형태 | `D:\dev` (역슬래시) | `/home/rhikas` (슬래시) |
| 하는 일 | git, 노트 작성, GitHub 업로드 | 리눅스 명령어 실습 |
| 이동 | `ssh`로 VM에 접속 | `exit`로 Windows로 복귀 |

## 오류와 해결
### 1. Connection refused
- **증상**: `ssh: connect to host 127.0.0.1 port 2222: Connection refused`
- **원인**: VM 안에서 `ssh -p 2222 rhikas@127.0.0.1`을 입력함. VM 안의 127.0.0.1은 VM 자신이라 2222번 포트가 열려 있지 않음
- **해결**: Windows PowerShell에서 입력

### 2. VM 안에서 Windows 경로 입력
- **증상**: `-bash: cd: D:devlinux_study1: 그런 파일이나 디렉터리가 없습니다`
- **원인**: SSH 접속 상태(리눅스)에서 Windows 경로를 입력함. 리눅스에는 드라이브 문자가 없고 `\`는 이스케이프 문자로 해석됨
- **해결**: `exit`로 Windows로 돌아온 뒤 입력

## 정리
- 프롬프트가 `PS`로 시작하면 Windows, `[rhikas@localhost ~]$`이면 VM이다.
- git 작업은 Windows에서, 리눅스 실습은 VM에서 한다.
- 명령어를 입력하기 전에 프롬프트를 먼저 확인한다.