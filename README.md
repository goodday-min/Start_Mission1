



🔐 STEP 2. 권한 실습 및 증거 기록 (요구사항 3) 
-권한을 확인/변경하는 명령을 수행하고, 변경 전/후 비교를 기술 문서에 남긴다.
-최소 요구: 파일 1개, 디렉토리 1개에 대해 권한 변경 실험을 수행한다.


## 🔐 STEP 2. 권한 실습 및 증거 기록

### 📌 과제 목표
- 파일/디렉토리의 권한을 **확인·변경**하는 명령을 수행한다.
- 권한 변경 **전/후를 비교**하여 기술 문서에 기록한다.
- 최소 요구: **파일 1개, 디렉토리 1개**에 대해 권한 변경 실험 수행.

---

### 📖 권한(permission) 개념
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

### ⚠️ 실습 환경 주의사항
- `/mnt/c/...` (윈도우 디스크 영역)는 권한이 **777로 고정**되어 변경이 반영되지 않음.
- 따라서 **리눅스 홈 디렉토리(`~`)** 에서 실습을 진행함.

---

### 💻 실습 명령어
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

### 📊 권한 변경 전/후 비교
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

### ✅ 결론
- `chmod` 명령으로 파일과 디렉토리의 권한을 변경하고,
  `ls -l` 출력을 통해 변경 전/후 차이를 확인함.
- 윈도우 영역(`/mnt/c`)이 아닌 리눅스 홈(`~`)에서 권한이 정상 적용됨을 확인함.

---

### 🔢 참고: 자주 쓰는 권한 숫자
| 숫자 | 권한 | 의미 |
|------|------|------|
| 777 | rwxrwxrwx | 모두 모든 권한 (보안 취약) |
| 755 | rwxr-xr-x | 소유자 전부, 나머지 읽기·실행 |
| 744 | rwxr--r-- | 소유자 전부, 나머지 읽기만 |
| 700 | rwx------ | 소유자만 전부 |
| 644 | rw-r--r-- | 소유자 읽기·쓰기, 나머지 읽기 |


## STEP 3. Docker 설치 점검 (요구사항 4)

| 명령어 | 설명 |
|--------|------|
| `docker --version` | 설치된 Docker의 버전 확인 |
| `sudo service docker start` | Docker 서비스(데몬) 시작 |
| `sudo docker info` | Docker 전체 시스템 정보 조회 |

### 💻 실행 결과

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

> 📸 실행 결과 캡처
> <img width="741" height="287" alt="image" src="https://github.com/user-attachments/assets/2cd84ae9-5107-4d24-b4ab-2f57e6d055e1" />

### 🔍 개념 정리

- **Docker**: 애플리케이션을 **컨테이너** 단위로 패키징하여 어디서든 동일하게 실행할 수 있게 해주는 플랫폼
- **`docker --version`**: 클라이언트 버전만 간단히 확인
- **`service docker start`**: Docker 데몬(백그라운드 서비스)을 실행하는 명령
  - WSL 환경에서는 부팅 시 자동 실행되지 않아 수동으로 시작해야 할 수 있음
- **`docker info`**: 클라이언트/서버 버전, 플러그인, 컨테이너 수 등 상세 정보 확인


