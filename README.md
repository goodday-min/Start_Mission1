
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


### 트러블슈팅 1: docker pull 시 권한 오류

- **문제**: `docker pull nginx:latest` 실행 시 아래 오류 발생
  permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
- **원인 가설**: 현재 사용자가 `docker` 그룹에 속하지 않아 docker.sock 접근이 거부됨
- **확인**: `groups` 또는 `id` 출력에 `docker`가 없음을 확인
- **해결**:
  sudo usermod -aG docker $USER
  (터미널 재접속 후) `groups`에 docker 표시 확인 → `docker images` 정상 동작



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

✅ 1. 로그 확인
```bash
docker logs web
docker logs -f web      # 실시간 로그 (follow)
```
- 애플리케이션 표준 출력/에러를 확인할 수 있어 디버깅에 유용합니다.

> docker logs web 실행 화면  
<img width="1172" height="350" alt="image" src="https://github.com/user-attachments/assets/50cd31ed-dd25-4116-81d3-d1801aad2dae" />


✅ 2. 리소스 사용량 확인
```bash
docker stats
docker stats web        # 특정 컨테이너만
docker stats --no-stream   # 한 번만 출력 후 종료 (문서화에 편리)
```
> docker stats web 실행 화면  
<img width="1107" height="61" alt="image" src="https://github.com/user-attachments/assets/77426bdb-bac5-475f-91da-e7a3c62017f0" />


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
## STEP 5. 컨테이너 실행 실습
--------------------------------------------------------------------------------------   

✅ 1. hello-world 실행 : `hello-world`: Docker가 정상 설치됐는지 확인하는 테스트용 이미지

```bash
docker run hello-world
```
> docker run hello-world 실행 화면 
<img height="300" alt="image" src="https://github.com/user-attachments/assets/2ed61b8f-c171-463e-8aaf-b557334af4b6" />  


💡Docker 동작 과정 : client → daemon → 이미지 pull → 컨테이너 생성 → 출력 전달   

| 단계 | 내용 | 의미 |
|:---:|------|------|
| 1 | Docker client가 Docker daemon에 연결 | 입력한 명령을 **클라이언트**가 받아 실제 일꾼인 **데몬**에게 전달 |
| 2 | daemon이 Docker Hub에서 `hello-world` 이미지를 pull (amd64) | 로컬에 이미지가 없어 **원격 저장소(Docker Hub)**에서 내려받음 |
| 3 | 이미지로부터 새 컨테이너 생성 | 이미지(설계도) → 컨테이너(실행체)로 만들어 실행 |
| 4 | 출력을 client로 스트리밍 → 터미널 표시 | 그 결과가 화면에 보이는 메시지 |


> 💡핵심 개념  
> - **이미지**: 실행에 필요한 파일·설정이 담긴 템플릿 (설계도)
> - **컨테이너**: 이미지를 실제로 실행한 인스턴스 (실행체)
> - **client → daemon** 구조: 명령 접수(client)와 실제 작업(daemon)이 분리됨

✅ 2. ubuntu 컨테이너 내부 진입  

```bash
docker run -it ubuntu
```

- `-i` (interactive): 표준 입력(STDIN)을 유지 → 입력을 받을 수 있음
- `-t` (tty): 가상 터미널 할당 → 터미널처럼 보이게 함
- `-it`를 함께 쓰면 컨테이너 안에서 직접 명령을 입력할 수 있음

> docker run -it ubuntu , echo "hollo container", echo ls 실행 화면
 <img height="250" alt="image" src="https://github.com/user-attachments/assets/48b771d3-c38a-4d78-b3cc-d9f7a480fa68" />
 
> 종료는 `exit` 입력 (컨테이너도 함께 정지됨)  
---

✅ 3. attach vs exec 비교  

| 구분 | attach | exec |
|------|--------|------|
| 동작 | 실행 중인 컨테이너의 **기존 프로세스(PID 1)** 화면에 연결 | 실행 중인 컨테이너에 **새 명령/프로세스**를 투입 |
| 세션 | 메인 프로세스와 입출력을 공유 | 독립된 새 세션 |
| 종료 영향 | `exit` 시 컨테이너가 **컨테이너 정지** | `exit` 해도 새 프로세스만 종료, 컨테이너는 **계속 실행** |
| 주 용도 | 메인 프로세스 로그/화면 보기 | 추가 작업, 디버깅 |


✅ 4. docker attach test vs docker exec -it test bash 비교  

| 구분 | `docker attach test` | `docker exec -it test bash` |
|------|----------------------|------------------------------|
| 하는 일 | 이미 돌고 있는 **메인 프로세스에 연결** | 컨테이너 안에서 **새 프로세스(bash) 실행** |
| 대상 | 이미 존재함 (연결만 하면 됨) | 지금 새로 만들어야 함 (뭘 실행할지 알려줘야 함) |
| 실행 대상 명시 | 불필요 (기본 bash에 연결) | 필요 (`bash` 등 실행할 프로그램 지정) |

🔑 핵심: 둘은 "하는 일"이 달라요  
> **요약**: attach는 기존 bash에 "연결", exec는 새 bash를 "실행". 
> 그래서 exec는 `-it`와 `bash`를 명시해야 한다.


먼저 백그라운드로 컨테이너 실행
```bash
docker run -dit --name test ubuntu
```
- `-d`: 백그라운드(detached) 실행

```bash
docker attach test
```

```bash
docker exec -it test bash
```

> attach, exec 실행 화면
<img width="998" height="377" alt="image" src="https://github.com/user-attachments/assets/a562bc4e-5202-4d6f-a655-103a21fdbac0" />


🪟 정리
- `attach`는 **원래 돌아가던 프로세스에 붙는 것**
- `exec`는 **새 명령을 추가로 실행하는 것**
- 실무에서는 컨테이너 중지를 피하려고 주로 `exec`를 사용함




--------------------------------------------------------------------------------------   
## STEP 6. 기존 Dockerfile 기반 커스텀 이미지 제작 
--------------------------------------------------------------------------------------    

### 1. 선택한 방식과 베이스
- **선택 방식**: (A) 웹 서버 베이스 이미지 활용 + 정적 콘텐츠/설정 교체
- **기존 베이스 이미지**: `nginx:alpine` (경량 alpine 리눅스 기반 NGINX 공식 이미지)
- **작업 경로**: `~/codyssey/docker-custom` (리눅스 홈. `/mnt/c`는 권한이 777로 고정되어 홈에서 진행)

### 2. 디렉토리 구성
```
docker-custom/
├── Dockerfile
└── site/
    └── index.html
```

### 3. 작업 폴더 생성 및 파일 작성
```bash
$ cd ~
$ mkdir -p ~/codyssey/docker-custom/site     # 작업 폴더 + 하위 폴더 생성
$ cd ~/codyssey/docker-custom                # 작업 폴더로 이동
$ pwd                                        # 현재 위치 확인
/home/user/codyssey/docker-custom
```
> 작업 폴더 생성
<img width="992" height="97" alt="image" src="https://github.com/user-attachments/assets/dd77b31c-48a2-474c-a0b9-77091d64b72d" />

**site/index.html**
```bash
$ cat > site/index.html << 'EOF'
<!DOCTYPE html>
<html lang="ko">
<head><meta charset="UTF-8"><title>My Custom Nginx</title></head>
<body>
  <h1>Hello from my custom image!</h1>
  <p>내가 만든 NGINX 커스텀 이미지입니다.</p>
</body>
</html>
EOF
```
> 웹 페이지 만들기

| 내용 작성 : cat >  site/index.html << 'EOF' | 내용 확인 : cat site/index.html |
| --- | --- | 
| <img width="937" height="232" alt="image" src="https://github.com/user-attachments/assets/078181c9-f2bf-46f3-ba6e-8440fdbabaeb" /> | <img width="797" height="327" alt="image" src="https://github.com/user-attachments/assets/e5df2c4f-a1e6-409a-a54f-0d9a17cf37eb" /> | 


**Dockerfile**
```bash
$ cat > Dockerfile << 'EOF'
FROM nginx:alpine
LABEL org.opencontainers.image.title="my-custom-nginx"
ENV APP_ENV=dev
COPY site/ /usr/share/nginx/html/
EOF
```

> Dockerfile 만들기  
<img width="882" height="235" alt="image" src="https://github.com/user-attachments/assets/7929e6e4-e8c4-45f4-abfd-ead8bfc70fdd" />


### 4. 커스텀 포인트와 목적

| Dockerfile 줄 | 커스텀 포인트 | 목적 |
|---|---|---|
| `FROM nginx:alpine` | 기존 베이스 이미지 사용 | 웹 서버를 처음부터 만들지 않고, 검증된 경량 NGINX 이미지를 기반으로 시작 |
| `LABEL ...title="my-custom-nginx"` | 이미지 메타데이터 | 이미지의 이름/용도를 표시해 식별·관리를 쉽게 함 |
| `ENV APP_ENV=dev` | 환경변수 설정 | 실행 모드를 코드가 아닌 설정으로 분리 (설정과 코드의 분리) |
| `COPY site/ /usr/share/nginx/html/` | 정적 콘텐츠 교체 | NGINX 기본 페이지를 내가 만든 페이지로 교체 (핵심 커스텀) |

### 5. 이미지 빌드
```bash
$ docker build -t my-custom-nginx:1.0 .
```
- `-t 이름:태그`: 이미지 이름 지정 / 마지막 `.`: 현재 폴더의 Dockerfile 사용
- `Using cache`: 같은 내용의 단계는 이전 빌드 결과를 재사용
- 출력 맨 위에 legacy builder DEPRECATED 경고가 표시됨 (buildx 설치 안내). 빌드는 정상 성공.

> docker build 출력 캡처

<img width="1013" height="450" alt="image" src="https://github.com/user-attachments/assets/227b4cca-2f30-4115-9997-342c49a11867" />


```bash
$ docker images
```
> docker images 실행 화면  
<img width="1456" height="191" alt="image" src="https://github.com/user-attachments/assets/befaf287-0ef3-4749-85f6-f720ac378526" />


### 6. 컨테이너 실행
```bash
$ docker run -d -p 8080:80 --name my-nginx my-custom-nginx:1.0
$ docker ps
CONTAINER ID   IMAGE                 STATUS           PORTS                                     NAMES
c9dd7ff84780   my-custom-nginx:1.0   Up About a minute   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-nginx
```
- `-d`: 백그라운드 실행 / `-p 8080:80`: 호스트 8080 → 컨테이너 80 / `--name`: 컨테이너 이름

> docker run, docker ps 실행 화면  
<img width="1622" height="218" alt="image" src="https://github.com/user-attachments/assets/f76ea94c-a54b-48c4-9c02-a96221988576" />


### 7. 검증 (접속 · 환경변수 · 로그)

**① curl 응답**
```bash
$ curl http://localhost:8080
```
> curl 실행 화면
<img width="887" height="217" alt="image" src="https://github.com/user-attachments/assets/49a0ca0b-602e-44de-86a8-553f677b2fba" />

**② 브라우저 접속** (주소창 `localhost:8080` + 응답 화면)
<img width="673" height="213" alt="image" src="https://github.com/user-attachments/assets/bc059b5b-9b2c-417a-8c22-982384feecf8" />


**③ 환경변수 적용 확인**
```bash
$ docker exec my-nginx env | grep APP_ENV
APP_ENV=dev
```
> 환경변수 실행 화면 
<img width="1058" height="51" alt="image" src="https://github.com/user-attachments/assets/fa781323-459b-4de0-8c78-dbab0bff779b" />

**④ 접속 로그 확인**
```bash
$ docker logs --tail 5 my-nginx
```

> 접속로그 실행 화면 
<img width="1621" height="191" alt="image" src="https://github.com/user-attachments/assets/be21dfca-699e-4c97-94d0-4549a7832636" />

> curl과 Chrome 요청이 모두 `200`으로 컨테이너에 도달했음을 확인 (로그 시각은 UTC)

### 8. 검증 요약

| 검증 항목 | 명령 | 결과 |
|---|---|---|
| 이미지 빌드 성공 | `docker build`, `docker images` | `my-custom-nginx:1.0` 생성 |
| 컨테이너 실행 성공 | `docker ps` | `my-nginx` Up, `8080->80` |
| 커스텀 콘텐츠 적용 | `curl`, 브라우저 | 내가 만든 HTML 응답 |
| 환경변수 적용 | `docker exec ... env` | `APP_ENV=dev` |
| 요청 도달 | `docker logs` | `GET / ... 200` |

### 9. 정리 (이 단계에서 이해한 점)
- **이미지**는 설계도(읽기 전용), **컨테이너**는 그것을 실행한 인스턴스이며 한 이미지로 여러 컨테이너를 만들 수 있다.
- 컨테이너는 격리되어 있어 `-p` 포트 매핑을 해야 호스트에서 접근할 수 있다.
- 기존 이미지 위에 `COPY`/`ENV`/`LABEL`을 얹는 것이 "커스텀 이미지" 제작이다.



-------------------------------------------------------------------------------------- 
## STEP 7. 포트 매핑 및 접속 증거 
-------------------------------------------------------------------------------------- 




















