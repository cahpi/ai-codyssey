# 프로젝트 개요

📑 프로젝트 개요 요약

1. 수행 배경 및 목적
   본 프로젝트는 Docker를 활용한 애플리케이션 컨테이너화 및 데이터 영속성(Persistence) 관리 기법을 실습하고, 작업된 최종 코드를 Git/GitHub 기반의 버전 관리 시스템으로 통합·관리하는 것을 목적으로 진행되었습니다.

2. 주요 수행 내용

   맞춤형 웹 서버 이미지 구축
   GINX 웹 서버 기반의 컨테이너 환경을 구성하고, 애플리케이션 소스 코드를 포함하는 전용 커스텀 Docker 이미지를 성공적으로 생성했습니다.

   바인드 마운트(Bind Mount)를 통한 실시간 개발 환경 검증
   호스트 PC의 소스 코드 디렉터리와 컨테이너 내부 웹 경로를 직접 연결했습니다.
   컨테이너를 재시작하거나 재빌드하지 않아도 호스트 파일 수정 사항이 웹 서비스에 즉각 반영되는 개발 효율성을 확인했습니다.

   네임드 볼륨(Named Volume)을 통한 데이터 영속성 검증
   컨테이너 삭제 시 내부 데이터가 손실되는 문제를 해결하기 위해 독립적인 Docker 볼륨 영역을 생성·연결했습니다.
   데이터 작성 후 실행 중인 컨테이너를 완전 삭제하고 새로운 컨테이너를 실행하더라도, 볼륨에 저장된 데이터가 유실 없이 그대로 보존되는 데이터와 컨테이너의 생명주기 분리를 검증했습니다.

   GitHub 원격 저장소 연동 및 트러블슈팅
   완성된 프로젝트의 코드와 도커 설정 파일들을 원격 저장소(cahpi/ai-codyssey)에 등록했습니다.
   개인 액세스 토큰(PAT) 인증 설정, 원격 계정 주소 스펠링 수정, 로컬-원격 저장소 간 파일 충돌 해결 프로세스를 거쳐 main 브랜치로 최종 동기화를 완료했습니다.

3. 핵심 성과 및 결론
   운영 안정성 확보: 컨테이너의 생성과 삭제에 영향을 받지 않는 데이터 보존 체계를 성공적으로 구축했습니다.
   개발 생산성 향상: 로컬 코드 변경 사항이 컨테이너 환경에 즉시 반영되는 구조를 검증했습니다.
   형상 관리 체계 완성: Docker 환경 구축 과정 및 최종 결과물을 GitHub 저장소에 안전하게 업로드 및 백업했습니다.
   


## 1. 실행 환경 및 수행 체크리스트

- OS / Shell / Terminal: Windows 11 / Git bush
- Docker Version: Docker version 29.6.2, build dfc4efb
- Git Version: git version 2.55.0.windows.3

[x] 터미널 기본 조작 및 폴더 구성
[x] 권한 변경 실습
[x] Docker 설치/ 점검
[x] hello-world 실행
[x] Dockerfile 빌드/ 실행
[x] 포트 매핑 접속(2회)
[x] 바인드 마운드 반영
[x] 볼륨 영속성
[x] Git 설정 + VS Code Github 연동

## 2. 터미널 및 권한 실습 로그

'practice'라는 이름의 새 폴더 만들기
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ mkdir practice

'practice' 폴더 안으로 들어가기
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ cd practice

제대로 들어왔는지 현재 위치 확인하기
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ pwd
/c/ai_codyssey/practice

'test.txt'라는 빈 파일 생성
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ touch test.txt

'test.txt' 파일에 "Hello World"라는 글자 집어넣기
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ echo "Hello World" > test.txt

'test.txt' 파일 내용 확인해보기
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ cat test.txt
Hello World

test.txt를 복사해서 copy.txt 만들기
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ cp test.txt copy.txt

파일 목록 확인해보기 (test.txt, copy.txt 두 개가 보여야 함)
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ ls
copy.txt test.txt

copy.txt의 이름을 renamed.txt로 바꾸기
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ mv copy.txt renamed.txt

파일 목록 다시 확인 (copy.txt가 사라지고 renamed.txt가 생김)
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ ls
renamed.txt test.txt

renamed.txt 파일 삭제하기
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ rm renamed.txt

현재폴더 파일 확인
hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ ls
test.txt

hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$ ls
test.txt

hello@Desktop-1 MINGW64 /c/ai_codyssey/practice
$

## 3. Docker 운영 & 컨테이너 실행 로그

(docker ps, docker images, docker logs 등의 명령어 및 결과)

Docker 버전 확인
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker --version
Docker version 29.6.2, build dfc4efb

Docker 데몬(엔진) 동작 여부 확인
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker info
Client:
Version: 29.6.2
Context: default
Debug Mode: false
Plugins:
agent: Docker AI Agent Runner (Docker Inc.)
Version: v1.111.0
Path: C:\Users\hello\.docker\cli-plugins\docker-agent.exe
ai: Docker AI Agent - Ask Gordon (Docker Inc.)
Version: v1.27.0
Path: C:\Users\hello\.docker\cli-plugins\docker-ai.exe
buildx: Docker Buildx (Docker Inc.)
Version: v0.35.0-desktop.2
Path: C:\Users\hello\.docker\cli-plugins\docker-buildx.exe
compose: Docker Compose (Docker Inc.)
Version: v5.3.1
Path: C:\Users\hello\.docker\cli-plugins\docker-compose.exe
debug: Get a shell into any image or container (Docker Inc.)
Version: 0.0.47
Path: C:\Users\hello\.docker\cli-plugins\docker-debug.exe
desktop: Docker Desktop commands (Docker Inc.)
Version: v0.4.3
Path: C:\Users\hello\.docker\cli-plugins\docker-desktop.exe
dhi: CLI for managing Docker Hardened Images (Docker Inc.)
Version: v0.0.7
Path: C:\Users\hello\.docker\cli-plugins\docker-dhi.exe
extension: Manages Docker extensions (Docker Inc.)
Version: v0.2.31
Path: C:\Users\hello\.docker\cli-plugins\docker-extension.exe
init: Creates Docker-related starter files for your project (Docker Inc.)
Version: v1.4.0
Path: C:\Users\hello\.docker\cli-plugins\docker-init.exe
mcp: Docker MCP Plugin (Docker Inc.)
Version: v0.43.3
Path: C:\Users\hello\.docker\cli-plugins\docker-mcp.exe
model: Docker Model Runner (Docker Inc.)
Version: v1.2.6
Path: C:\Users\hello\.docker\cli-plugins\docker-model.exe
offload: Docker Offload (Docker Inc.)
Version: v0.6.9
Path: C:\Users\hello\.docker\cli-plugins\docker-offload.exe
pass: Docker Pass Secrets Manager Plugin (beta) (Docker Inc.)
Version: v0.2.0
Path: C:\Users\hello\.docker\cli-plugins\docker-pass.exe
sandbox: "docker sandbox" is deprecated, use Docker Sandboxes instead (Docker Inc.)
Version: v0.13.0
Path: C:\Users\hello\.docker\cli-plugins\docker-sandbox.exe
scout: Docker Scout (Docker Inc.)
Version: v1.23.1
Path: C:\Users\hello\.docker\cli-plugins\docker-scout.exe

Server:
failed to connect to the docker API at npipe:////./pipe/docker_engine; check if the path is correct and if the daemon is running: open //./pipe/docker_engine: The system cannot find the file specified.

hello-world 컨테이너 실행
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:

1.  The Docker client contacted the Docker daemon.
2.  The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
3.  The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
4.  The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
$ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
https://hub.docker.com/

For more examples and ideas, visit:
https://docs.docker.com/get-started/

ubuntu 컨테이너 실행 및 내부 진입
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker run -it ubuntu bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
a3679419df18: Pull complete
ed819469700f: Pull complete
e16351a257e4: Download complete
Digest: sha256:3131b4cc82a783df6c9df078f86e01819a13594b865c2cad47bd1bca2b7063bb
Status: Downloaded newer image for ubuntu:latest

# 컨테이너 내부 파일 목록 확인

root@b417da1dd2cf:/# ls -la
total 60
drwxr-xr-x 1 root root 4096 Jul 30 11:41 .
drwxr-xr-x 1 root root 4096 Jul 30 11:41 ..
-rwxr-xr-x 1 root root 0 Jul 30 11:41 .dockerenv
drwxr-xr-x 2 root root 4096 Jul 13 16:06 .rock
lrwxrwxrwx 1 root root 7 Apr 20 08:46 bin -> usr/bin
drwxr-xr-x 2 root root 4096 Apr 20 08:46 boot
drwxr-xr-x 5 root root 360 Jul 30 11:41 dev
drwxr-xr-x 1 root root 4096 Jul 30 11:41 etc
drwxr-xr-x 3 root root 4096 Jul 13 16:06 home
lrwxrwxrwx 1 root root 7 Apr 20 08:46 lib -> usr/lib
lrwxrwxrwx 1 root root 9 Apr 20 08:46 lib64 -> usr/lib64
drwxr-xr-x 2 root root 4096 Jul 13 16:05 media
drwxr-xr-x 2 root root 4096 Jul 13 16:05 mnt
drwxr-xr-x 2 root root 4096 Jul 13 16:05 opt
dr-xr-xr-x 225 root root 0 Jul 30 11:41 proc
drwx------ 2 root root 4096 Jul 13 16:06 root
drwxr-xr-x 4 root root 4096 Jul 13 16:06 run
lrwxrwxrwx 1 root root 8 Apr 20 08:46 sbin -> usr/sbin
drwxr-xr-x 2 root root 4096 Jul 13 16:05 srv
dr-xr-xr-x 13 root root 0 Jul 30 11:41 sys
drwxrwxrwt 2 root root 4096 Jul 13 16:06 tmp
drwxr-xr-x 12 root root 4096 Jul 13 16:05 usr
drwxr-xr-x 11 root root 4096 Jul 13 16:06 var

텍스트 출력 테스트
root@b417da1dd2cf:/# echo "Hello from Ubuntu Container"
Hello from Ubuntu Container

우분투 컨테이너 종료 및 밖으로 나오기
exit
root@b417da1dd2cf:/# exit
exit

이미지 목록 조회
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker images
i Info → U In UseIMAGE ID DISK USAGE CONTENT SIZE EXTRA
hello-world:latest c3cbe1cc1aa5 25.9kB 9.49kB U  
ubuntu:latest 3131b4cc82a7 161MB 45.3MB U

전체 컨테이너 목록 조회
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker ps -a
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
b417da1dd2cf ubuntu "bash" 7 minutes ago Exited (0) 4 minutes ago keen_davinci
8c8906112dd8 hello-world "/hello" 8 minutes ago Exited (0) 8 minutes ago focused_hamilton

로그 조회
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker logs
docker: 'docker logs' requires 1 argument
Usage: docker logs [OPTIONS] CONTAINER
Run 'docker logs --help' for more information

## 4. Custom Dockerfile & 포트 매핑 증빙

A 방식: NGINX 웹 서버 베이스 (정적 파일 교체)
HTML, (디렉토리 : /app/index.html)

<h1>My NGINX Custom Web Server</h1>
Dockerfile
FROM nginx:alpine
COPY ./app /usr/share/nginx/html
EXPOSE 80

이미지 빌드
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker build -t my-nginx-web .
[+] Building 0.4s (7/7) FINISHED docker:desktop-linux
=> [internal] load build definition from Dockerfile 0.0s
=> => transferring dockerfile: 99B 0.0s
=> [internal] load metadata for docker.io/library/nginx:alpine 0.0s
=> [internal] load .dockerignore 0.0s
=> => transferring context: 2B 0.0s
=> [internal] load build context 0.0s
=> => transferring context: 109B 0.0s
=> [1/2] FROM docker.io/library/nginx:alpine@sha256:4a73073bd557c65b759505da037898b61f1be6cbc 0.0s
=> => resolve docker.io/library/nginx:alpine@sha256:4a73073bd557c65b759505da037898b61f1be6cbc 0.0s
=> CACHED [2/2] COPY ./app /usr/share/nginx/html 0.0s
=> exporting to image 0.1s
=> => exporting layers 0.0s
=> => exporting manifest sha256:01a18ac8a695ce3ebd4630302947d1dcaf42206e7f4483749fb2e14370d13 0.0s
=> => exporting config sha256:5f09fe41d6b0b19bd98b1b0b3e02b1bbd015eca9f13e77a3b306c11f747475c 0.0s
=> => exporting attestation manifest sha256:d24b5aad1e4110265241d67a53db41a7dc76efd79a1ba170b 0.0s
=> => exporting manifest list sha256:288ca9663c79370ea26ff096f557aef70c7f215d8f084843883dacd5 0.0s
=> => naming to docker.io/library/my-nginx-web:latest 0.0s
=> => unpacking to docker.io/library/my-nginx-web:latest

포트 매핑 후 컨테이너 실행 (호스트 8080 -> 컨테이너 80)
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker run -d -p 8080:80 --name nginx-container my-nginx-web
a579fb088c149e7c3d2fde7b832d31ac29d2551de8a9592dc06fc5e4dd285c76

접속 검증 (터미널 curl 또는 웹 브라우저에서 http://localhost:8080 접속)
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ curl http://localhost:8080
HTML

<h1>AI Codyssey Docker Test Server</h1>

실행 중인 컨테이너 확인
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
23d70ab7f3c0 my-nginx-web "/docker-entrypoint.…" 5 seconds ago Up 4 seconds 0.0.0.0:8080->80/tcp, [::]:8080->80/tcp nginx-container

다운로드된 이미지 목록 확인

hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker images
i Info → U In UseIMAGE ID DISK USAGE CONTENT SIZE EXTRA
hello-world:latest c3cbe1cc1aa5 25.9kB 9.49kB U  
my-nginx-web:latest 288ca9663c79 92.7MB 26.1MB U  
my-ubuntu-web:latest 65751d34f336 171MB 42.8MB U  
nginx:alpine 4a73073bd557 93.6MB 27MB  
ubuntu:latest 3131b4cc82a7 161MB 45.3MB U

컨테이너 로그 확인하기
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker logs nginx-container
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/07/30 12:39:08 [notice] 1#1: using the "epoll" event method
2026/07/30 12:39:08 [notice] 1#1: nginx/1.31.3
2026/07/30 12:39:08 [notice] 1#1: built by gcc 15.2.0 (Alpine 15.2.0)
2026/07/30 12:39:08 [notice] 1#1: OS: Linux 6.18.33.2-microsoft-standard-WSL2
2026/07/30 12:39:08 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/07/30 12:39:08 [notice] 1#1: start worker processes
2026/07/30 12:39:08 [notice] 1#1: start worker process 30
2026/07/30 12:39:08 [notice] 1#1: start worker process 31
2026/07/30 12:39:08 [notice] 1#1: start worker process 32
2026/07/30 12:39:08 [notice] 1#1: start worker process 33
2026/07/30 12:39:08 [notice] 1#1: start worker process 34
2026/07/30 12:39:08 [notice] 1#1: start worker process 35
2026/07/30 12:39:08 [notice] 1#1: start worker process 36
2026/07/30 12:39:08 [notice] 1#1: start worker process 37

B 방식: Linux(Ubuntu) 베이스 (패키지 설치 및 사용자/환경변수 설정)
Dockerfile.ubuntu 파일 생성

Dockerfile
FROM ubuntu:22.04
필수 패키지(Python3) 설치 및 대화형 입력 방지
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y python3 curl && rm -rf /var/lib/apt/lists/\*
커스텀 환경변수 및 작업 디렉터리 설정
ENV APP_ENV=production
WORKDIR /my_app
간단한 HTML 파일 생성
RUN echo "<h1>Hello from Ubuntu Python Web Server!</h1>" > index.html
EXPOSE 9000
Python 기본 HTTP 서버 실행 (포트 9000)
CMD ["python3", "-m", "http.server", "9000"]

지정한 Dockerfile로 이미지 빌드 (-f 옵션)
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker build -f Dockerfile.ubuntu -t my-ubuntu-web .

컨테이너 실행
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker run -d -p 9000:9000 --name ubuntu-container my-ubuntu-web
de880c8e43d536f90ba9c210826d6495a9a03d99f8a2e747bff25f0a7853f566

접속 검증
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ curl http://localhost:9000

<h1>Hello from Ubuntu Python Web Server!</h1>

## 5. 바인드 마운트 및 볼륨 영속성 검증

바인드 마운트 (Bind Mount) 실습
이미지 빌드
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker build -t my-nginx-web .
\*build -t : 생성될 Docker 이미지에 이름(Name)과 태그(Tag)를 지정하는 옵션입니다.

포트 매핑 후 컨테이너 실행 (호스트 8080 -> 컨테이너 80)
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker run -d -p 8080:80 -v //c/ai_codyssey/app:/usr/share/nginx/html --name nginx-container nginx:alpine
3b7526613529f8caeb17d519f3b715ba70a1976f69c9fdec8335f2a86ccb968e

- run -d :컨테이너를 데몬(Daemon) 형태, 즉 백그라운드에서 실행하도록 하는 옵션
- run -p : -p (Port Mapping) : 포트 바인딩/매핑, 호스트(내 PC)의 네트워크 포트와 컨테이너 내부의 포트를 연결(매핑)해주는 옵션

접속 검증 (터미널 curl 또는 웹 브라우저에서 http://localhost:8080 접속)
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ curl http://localhost:8080

<h1>Hello!, AI codyssey</h1>

호스트에서 index.html 파일 내용을 수정해보기
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ echo "<h1>Updated Content via Bind Mount!</h1>" > app/index.html

수정 후 즉시 반영되었는지 재검증 (컨테이너 재시작 없이 변경됨)
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ curl http://localhost:8080

<h1>Updated Content via Bind Mount!</h1>

실습 내용
-> 호스트 PC의 app/index.html 파일을 수정했을 때, 컨테이너를 재시작하지 않아도 실시간으로 내용이 변경됨을 확인했습니다.
삭제 전/후 비교 결과
-> 만약 -v 옵션(바인드 마운트)을 쓰지 않았다면, 컨테이너를 삭제하고 다시 만들었을 때 수정했던 index.html 내용은 전부 날아가고 NGINX 기본 페이지로 초기화되었을 것입니다.
하지만 바인드 마운트를 적용했기 때문에, 컨테이너를 삭제(docker rm -f)하고 새로 만들더라도 호스트 PC에 원본 파일(app/index.html)이 그대로 남아있어 데이터를 완벽하게 유지(보존)할 수 있습니다.

Docker 볼륨 영속성 (Named Volume) 검증
'my-data-vol'이라는 이름의 Docker 볼륨 생성
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker volume create my-data-vol
my-data-vol

볼륨을 /data 경로에 연결하여 컨테이너 실행 후 파일 쓰기 'Persistent Data'
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker run --rm -v my-data-vol:/data ubuntu bash -c "echo 'Persistent Data' > /data/test.txt"

데이터가 보존되는지 새로운 컨테이너를 열어서 확인
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker run --rm -v my-data-vol:/data ubuntu cat //data/test.txt
Persistent Data
(출력: Persistent Data)

검증 완료 후 볼륨 조회 명령어 기록
hello@Desktop-1 MINGW64 /c/ai_codyssey
$ docker volume ls
DRIVER VOLUME NAME
local my-data-vol

참고
-rm (Remove on exit)
뜻: 컨테이너가 종료(Exited)될 때 자동으로 컨테이너를 삭제하는 옵션입니다.
-v 또는 --volume (Volume / Bind Mount)
뜻: 호스트(내 컴퓨터)의 저장 공간이나 Docker 볼륨을 컨테이너 내부의 경로와 연결(마운트)하는 옵션입니다.

실습 내용
-> my-data-vol 볼륨을 생성하고 첫 번째 컨테이너를 띄워 /data/test.txt에 "Persistent Data"라는 데이터를 기록했습니다.
작업을 마친 첫 번째 컨테이너는 --rm 옵션에 의해 완전히 삭제(제거)되었습니다.
이후 새로운(완전히 별개의) 두 번째 컨테이너를 띄워 동일한 볼륨(my-data-vol)을 연결한 뒤 파일을 읽었습니다(cat //data/test.txt).

삭제 전/후 비교 결과
컨테이너 삭제 전: 첫 번째 컨테이너 내부의 /data/test.txt에 데이터가 저장됨.
컨테이너 삭제 후: 첫 번째 컨테이너는 디스크 상에서 흔적도 없이 완전히 삭제됨(--rm).
비교 및 검증 결과: 컨테이너가 통째로 삭제되었음에도 불구하고, Docker가 관리하는 독립 저장소(my-data-vol)에 데이터가 안전하게 보존되어 있었기 때문에, 새로운 컨테이너를 통해 데이터(Persistent Data)를 유실 없이 그대로 조회(복원)할 수 있음을 증명했습니다.

## 6. Git hub 연동



## 7. 트러블슈팅 (2건 이상)

1.트러블슈팅 이슈 (B 방식: Linux(Ubuntu) 베이스, Dockerfile 기반 커스텀 이미지 제작 시 )

- \*\*문제: Git Bash 환경에서 `-v $(pwd)/app:/usr/share/nginx/html` 옵션으로 바인드 마운트 후 호스트 파일 수정 시 컨테이너에 즉시 반영되지 않고 NGINX 기본 페이지가 출력됨.
- \*\*원인 가설: Windows Git Bash의 POSIX 경로(`/c/ai_codyssey`)가 Docker Desktop으로 전달되는 과정에서 마운트 경로가 깨졌을 것으로 가설 수립.
- \*\*확인: `docker inspect bind-test-container`를 통해 Mounts 정보의 Source 경로가 잘못 매핑된 것을 확인.
- \*\*해결/대안: 경로 앞에 `//`를 추가하여 Windows 절대 경로(`//c/ai_codyssey/app`)로 전달되도록 수정

  2.트러블슈팅 이슈 (파일 읽기 및 영속성 검증 실습)

- \*\*문제: `docker run --rm -v my-data-vol:/data ubuntu cat /data/test.txt` 실행 시 `cat: 'C:/Program Files/Git/data/test.txt': No such file or directory` 에러 발생.
- \*\*원인 가설: Git Bash가 명령어 내의 리눅스 절대 경로(`/data/test.txt`)를 Git Bash 설치 경로(`C:/Program Files/Git/...`)로 자동 변환하여 컨테이너에 전달했을 것임.
- \*\*확인: 에러 출력 메시지에 윈도우 경로가 포함되어 전달된 것을 확인.
- \*\*해결/대안: 경로 맨 앞에 슬래시를 하나 더 붙여 `//data/test.txt` 형태로 작성함으로써 Git Bash의 자동 경로 변환을 방지하고 정상적으로 볼륨 영속성을 검증함.
