
--------------------------------------------------------------------------------------    

## STEP 1. 리눅스 기본 명령어 정리  



### 실습 환경 
- 환경: WSL 2 (microsoft-standard-WSL2) - WSL(Windows Subsystem for Linux)
- 배포판: Ubuntu 26.04.1 LTS (Resolute Raccoon)
- 셸(SHELL): /bin/bash
- 커널(Cornel): Linux 6.18.54.1
- 작업 경로: /mnt/c/Users/user/codyssey/allinone/start_mission1

✅ 1단계: 환경 확인 명령어 (windows : PowerShell 에서 확인)

        wsl --list
  
<img width="505" height="101" alt="image" src="https://github.com/user-attachments/assets/f9432d1a-95f5-45e0-95e8-0c6b4cf7eb10" />


✅ 2단계: 리눅스 터미널에서 환경 정보 확인
  
        uname -a               # 커널 정보 (Linux로 시작하는지)  
        cat /etc/os-release    # 배포판/버전 (Ubuntu 확인)
        echo $SHELL            # 셸 종류 
        pwd                    # 현재 위치
  
<img width="1456" height="482" alt="image" src="https://github.com/user-attachments/assets/40b6e7f6-8708-41a1-a132-4df90b8f3277" />


### 주요 명령어 요약

| 번호 | 명령어 | 의미 | 설명 |
|------|--------|------|------|
| ① | `pwd` | print working directory | 현재 작업 중인 디렉터리 경로 출력 |
| ② | `ls` | list | 현재 폴더의 파일·디렉터리 목록 표시 |
| ③ | `ls -a` | list all | 숨김 파일(`.`으로 시작)까지 모두 표시 |
| ④ | `cd` | change directory | 디렉터리 이동 (`cd ..` 상위, `cd ~` 홈) |
| ⑤ | `mkdir` | make directory | 새 폴더 생성 |
| ⑥ | `touch` | - | 빈 파일 생성 |
| ⑦ | `cat` | concatenate | 파일 내용을 터미널에 출력 |
| ⑧ | `cp` | copy | 파일/폴더 복사 (`cp 원본 사본`) |
| ⑨ | `mv` | move | 이동 또는 이름 변경 (`mv 원본 대상`) |
| ⑩ | `rm` | remove | 파일 삭제 (`rm -r` 폴더 삭제) |

💡 자주 쓰는 옵션 추가 설명  
| 번호 | 명령어 | 
|------|--------|
|ls -al          |  숨김 파일 포함 + 상세 정보(권한, 크기, 날짜)  |
|cd ..           |  상위 폴더로 이동  |
|cd ~            |  홈 디렉토리로 이동  |
|cp -r 폴더 대상 |  폴더를 통째로 복사 (-r: 재귀)  |
|rm -r 폴더명    |  폴더 삭제 (안에 내용이 있을 때)  |




🔍 리눅스 환경, 어떤 걸 쓸 수 있나요?  

| 방법 | 설명 | 확인 방법 |
|------|------|-----------|
| **WSL** | Windows 안에서 리눅스 실행 (추천) | PowerShell에 `wsl --list` |
| **Git Bash** | Git 설치 시 포함, 리눅스 명령어 호환 | `git --version` 됐다면 사용 가능 |
| **가상머신 (VM)** | VirtualBox 등에 우분투 설치 | 이미 설치했다면 실행 |
| **실제 리눅스 PC** | 우분투 등 설치된 컴퓨터 | 바로 터미널 열기 |

🪟 새로운 용어 정리    
WSL이란? 윈도우 안에서 리눅스를 실행할 수 있게 해주는 기능  
셸(Shell)이란? 사용자의 명령을 해석해서 운영체제에 전달하는 프로그램    
커널(Kernel)이란?  운영체제의 핵심(중심부) - uname -a에서 봤던 Linux 6.18...이 바로 커널 버전  



--------------------------------------------------------------------------------------    
##  STEP 2. 권한 실습 및 증거 기록 (요구사항3)


### 과제 목표
- 파일/디렉토리의 권한을 **확인·변경**하는 명령을 수행한다.
- 권한 변경 **전/후를 비교**하여 기술 문서에 기록한다.
- 최소 요구: **파일 1개, 디렉토리 1개**에 대해 권한 변경 실험 수행.

---

#### ✅ 1. 권한(permission) 개념
권한 표기 형식 예시: `-rwxr-xr--`

```
-    rwx    r-x    r--
│     │      │      │
│     │      │      └─ 기타 사용자(other)
│     │      └──────── 그룹(group)
│     └─────────────── 소유자(user)
└───────────────────── 종류 (- 파일 / d 디렉토리)
```

| 기호 | 의미 | 숫자 |
|------|------|------|
| r | read (읽기) | 4 |
| w | write (쓰기) | 2 |
| x | execute (실행) | 1 |

- 숫자 합산으로 표현: `rwx`=7, `r-x`=5, `r--`=4
- **chmod** = **ch**ange **mod**e (권한 변경 명령)

---

⚠️ 실습 환경 주의사항
- `/mnt/c/...` (윈도우 디스크 영역)는 권한이 **777로 고정**되어 변경이 반영되지 않음.
- 따라서 **리눅스 홈 디렉토리(`~`)** 에서 실습을 진행함.

---

💻 실습 명령어
```bash
cd ~                     # 리눅스 홈 디렉토리로 이동
mkdir perm_test          # 실습용 폴더 생성
cd perm_test             # 폴더로 이동
touch sample.txt         # 실습용 파일 생성
mkdir sample_dir         # 실습용 디렉토리 생성

ls -l                    # [변경 전] 권한 확인

chmod 744 sample.txt     # 파일 권한 변경 (rwxr--r--) 
chmod 700 sample_dir     # 디렉토리 권한 변경 (rwx------)  

ls -l                    # [변경 후] 권한 확인
```

---

📊 권한 변경 전/후 비교
| 대상 | 변경 전 | 변경 후 | 사용 명령 |
|------|---------|---------|-----------|
| sample.txt (파일) | rw-r--r-- (644) | rwxr--r-- (744) | `chmod 744 sample.txt` |
| sample_dir (디렉토리) | rwxr-xr-x (755) | rwx------ (700) | `chmod 700 sample_dir` |

---

### 📷 증거 캡처
**변경 전**
<img width="687" height="136" alt="image" src="https://github.com/user-attachments/assets/b587d7dc-7eab-421f-a63a-58d599364685" />


**변경 후**
<img width="687" height="142" alt="image" src="https://github.com/user-attachments/assets/9566e7f4-4bb2-410f-be89-f90545d1d2fc" />


---

✅ 정리
- `chmod` 명령으로 파일과 디렉토리의 권한을 변경하고,
  `ls -l` 출력을 통해 변경 전/후 차이를 확인함.
- 윈도우 영역(`/mnt/c`)이 아닌 리눅스 홈(`~`)에서 권한이 정상 적용됨을 확인함.

---

🔢 참고: 자주 쓰는 권한 숫자
| 숫자 | 권한 | 의미 |
|------|------|------|
| 777 | rwxrwxrwx | 모두 모든 권한 (보안 취약) |
| 755 | rwxr-xr-x | 소유자 전부, 나머지 읽기·실행 |
| 744 | rwxr--r-- | 소유자 전부, 나머지 읽기만 |
| 700 | rwx------ | 소유자만 전부 |
| 644 | rw-r--r-- | 소유자 읽기·쓰기, 나머지 읽기 |

--------------------------------------------------------------------------------------    

##  STEP 3. Docker 설치 점검 (요구사항 4)

🐳 Docker 설치 방법
                
                sudo apt update              # 패키지 목록 최신화 (관리자 권한으로, 설치 가능한 패키지 목록을 최신으로 갱신) 
                sudo apt install docker.io   # Docker 설치
                

✅ 1. Docker 버전 확인  

                 docker --version

✅ 2. Docker 데몬 동작 확인 (Docker 전체 시스템 정보 조회)  

                 docker info 

✅ 3. Docker 데몬 동작 확인 

                 sudo service docker start  
        

💻 실행 결과

```bash
$ docker --version
Docker version 29.1.3, build 29.1.3-0ubuntu4.1

$ sudo service docker start

$ sudo docker info
Client:
 Version:    29.1.3
 Context:    default
 Debug Mode: false
```

>   
> <img width="741" height="287" alt="image" src="https://github.com/user-attachments/assets/2cd84ae9-5107-4d24-b4ab-2f57e6d055e1" />

→ 데몬이 켜져 있으면 서버 정보가 쭉 나와요.   
→ 꺼져 있으면 Cannot connect to the Docker daemon... 같은 에러가 나요.   

🔍 개념 정리

- **Docker**: 애플리케이션을 **컨테이너** 단위로 패키징하여 어디서든 동일하게 실행할 수 있게 해주는 플랫폼
- **`docker --version`**: 클라이언트 버전만 간단히 확인
- **`service docker start`**: Docker 데몬(백그라운드 서비스)을 실행하는 명령
  - WSL 환경에서는 부팅 시 자동 실행되지 않아 수동으로 시작해야 할 수 있음
- **`docker info`**: 클라이언트/서버 버전, 플러그인, 컨테이너 수 등 상세 정보 확인

🪟 새로운 용어 정리  
sudo ( Super User DO ) : 잠깐 관리자(root) 권한을 빌려서 명령을 실행할 때 사용.  
apt ( Advanced Package Tool ) : Ubuntu / Debian 계열 리눅스에서 쓰는 패키지 관리 도구, 프로그램을 설치·삭제·업데이트할 때 사용합니다.


--------------------------------------------------------------------------------------    

## STEP 4. Docker 기본 운영 명령 수행 (요구사항 5)   

🔍 Docker 핵심 개념 정리

- Docker   
> 애플리케이션을 **컨테이너**라는 격리된 공간에서 실행하는 가상화 기술입니다.  
> 실행 환경(라이브러리, 설정 등)을 **이미지**로 묶어두기 때문에, "내 PC에서는 되는데..." 문제를 줄여줍니다.  

- 이미지(Image)   
> 컨테이너를 만들기 위한 **설계도 / 템플릿**입니다.  
> 애플리케이션 코드 + 실행에 필요한 라이브러리·설정이 포함된 **읽기 전용** 파일입니다.  
> 한 개의 이미지로 **여러 개의 컨테이너**를 만들 수 있습니다.  
> 예: `nginx` 이미지 → 웹 서버 컨테이너 여러 개 생성 가능  
> 
> 비유: 이미지는 '붕어빵 틀', 컨테이너는 '찍어낸 붕어빵'  

- 컨테이너(Container)   
> 이미지를 실행한 **실제 동작 중인 인스턴스**입니다.   
> 서로 격리되어 있어 한 컨테이너의 문제가 다른 컨테이너에 영향을 주지 않습니다.   
> 생성 → 실행 → 중지 → 삭제의 **생명주기(Life Cycle)** 를 가집니다.    
---

| 상태 | 설명 |
|------|------|
| Created | 생성됐지만 아직 실행 전 |
| Up (Running) | 실행 중 |
| Exited (Stopped) | 중지됨 |

---

- 모니터링(Monitoring)
> 컨테이너가 **정상 동작하는지, 자원을 얼마나 쓰는지** 확인하는 운영 활동입니다.
> 주요 관찰 대상:
> **로그(logs)**: 애플리케이션의 출력·에러 메시지 → 문제 원인 파악
> **리소스(stats)**: CPU·메모리·네트워크 사용량 → 성능·부하 확인

---




## 1. 이미지(Image) 관련

✅ 1. 이미지 다운로드
```bash
docker pull nginx:latest
```
- `pull`: 레지스트리(기본 Docker Hub)에서 이미지를 받아옵니다.
- `nginx:latest`: `이미지명:태그` 형식. 태그 생략 시 `latest`.

✅ 2. 이미지 목록 확인
```bash
docker images
```
출력 예시:
```
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
nginx        latest    xxxxxxxxxxxx   2 weeks ago   187MB
```
- **IMAGE ID**: 이미지 고유 식별자
- **SIZE**: 이미지 용량


<img width="1447" height="573" alt="image" src="https://github.com/user-attachments/assets/ad06e420-7a1d-4874-bef7-204a0c48b46c" />   


*** 처음 docker pull nginx:latest 실행했을 떄 권한 오류 발생 함.
-> 해결 방법


---

## 2. 컨테이너(Container) 관련

✅ 1. 컨테이너 실행
```bash
docker run -d --name web -p 8080:80 nginx
```
- `-d`: 백그라운드(detached) 실행
- `--name web`: 컨테이너 이름 지정
- `-p 8080:80`: 호스트 8080 → 컨테이너 80 포트 연결


- 오류 발생
- 1. 해결방법 : 포트 충돌 컨테이너 중지후 삭제
> docker ps -a                                #전체 컨테이너 목록 조회
> docker stop stupefied_spence                #실행 중이므로 먼저 중지
> docker rm stupefied_spence                  # 제거

- 2. 해결방법 : 실패로 남은 web 컨테이너 제거
> docker rm web                               # Created 상태라 stop 없이 바로 제거 가능
 
- 3. 컨테이너 확인
> docker ps -a

✅ 2. 실행 중인 컨테이너 목록
```bash
docker ps
```

✅ 3. 전체 컨테이너 목록 (중지된 것 포함)
```bash
docker ps -a
```

<img width="1450" height="167" alt="image" src="https://github.com/user-attachments/assets/d486bc89-7366-4ec6-8483-0af9ce2571b2" />  

✅ 4. 컨테이너 중지
```bash
docker stop web
```

<img width="1448" height="256" alt="image" src="https://github.com/user-attachments/assets/f4d1373d-affe-492f-834d-2232beeeca20" />  



## 3. 운영(모니터링) 관련

### 1) 로그 확인
```bash
docker logs web
docker logs -f web      # 실시간 로그 (follow)
```
- 애플리케이션 표준 출력/에러를 확인할 수 있어 디버깅에 유용합니다.

### 2) 리소스 사용량 확인
```bash
docker stats
docker stats web        # 특정 컨테이너만
docker stats --no-stream   # 한 번만 출력 후 종료 (문서화에 편리)
```
출력 예시:
```
CONTAINER ID   NAME   CPU %   MEM USAGE / LIMIT   NET I/O     BLOCK I/O
xxxxxxxxxxxx   web    0.00%   3.5MiB / 1.9GiB     1.2kB/0B    0B/0B
```
- `stats`는 실시간 갱신됩니다. 종료는 `Ctrl + C`.


## 4. 전체 흐름 한눈에 보기
```
docker pull   →  이미지 다운로드
docker images →  이미지 목록 확인
docker run    →  이미지로 컨테이너 생성·실행
docker ps     →  실행 중 컨테이너 확인
docker logs   →  동작 로그 확인
docker stats  →  리소스 사용량 확인
docker stop   →  컨테이너 중지

```

--------------------------------------------------------------------------------------    





