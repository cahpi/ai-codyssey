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

실행 환경
- OS / Shell / Terminal: Windows 11 / Git bush
- Docker Version: Docker version 29.6.2, build dfc4efb
- Git Version: git version 2.55.0.windows.3

수행 체크리스트
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

## 6. Git hub 설정 및 연동 

사용자 이름 설정
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey
$ git config --global user.name "cahpi"

사용자 이메일 설정
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey
$ git config --global user.email "hellohoho19@gmail.com"

기본 브랜치 이름을 main으로 설정 (최신 관례)
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey
$ git config --global init.defaultBranch main

#vscode와 git hub 리모트 연결
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey 
$ git remote add origin https://github.com/cahpi/ai-codyssey.git

현재 브런치 이름을 main으로 변경
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git branch -M main


설정된 전체 리스트 확인
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git config --list
diff.astextplain.textconv=astextplain
filter.lfs.clean=git-lfs clean -- %f
filter.lfs.smudge=git-lfs smudge -- %f
filter.lfs.process=git-lfs filter-process
filter.lfs.required=true
http.sslbackend=schannel
core.autocrlf=true
core.fscache=true
core.symlinks=false
pull.rebase=false
credential.helper=manager
credential.https://dev.azure.com.usehttppath=true
init.defaultbranch=master
user.name=cahpi
user.email=hellohoho19@gmail.com
init.defaultbranch=main
(END)

git 저장소 초기화
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git init
Initialized empty Git repository in C:/ai-codyssey/.git/

git add . (변경 사항 무대에 올리기) **.**은 모든 변경된 파일을 의미
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git add . 

코멘트 달기, 무엇이 변했는지
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git commit -m "update: README.md"
[main 30a8537] update: README.md

git 저장소에 파일 업로드(push)하기
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git push -u origin main
Enumerating objects: 19, done.
Counting objects: 100% (19/19), done.
Delta compression using up to 16 threads
Compressing objects: 100% (16/16), done.
Writing objects: 100% (16/16), 1.64 KiB | 841.00 KiB/s, done.
Total 16 (delta 11), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (11/11), completed with 3 local objects.
To https://github.com/cahpi/ai-codyssey.git
   5468039..30a8537  main -> main
branch 'main' set up to track 'origin/main'.


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


## 추가 실습 docker compose 멀티 컨테이너 실행

GitHub SSH 키 설정 (보안 및 편의성)
SSH 키 생성 (터미널 입력):
user@DESKTOP-BOQ4DKE MINGW64 ~
$ ssh-keygen -t ed25519 -C "hellohoho19@gmail.com"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/c/Users/user/.ssh/id_ed25519): 
Created directory '/c/Users/user/.ssh'.
Enter passphrase for "/c/Users/user/.ssh/id_ed25519" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /c/Users/user/.ssh/id_ed25519
Your public key has been saved in /c/Users/user/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:Fj4OgPhA... (SSH key 생략)
The key's randomart image is:
+--[ED25519 256]--+
|OBXO+o.          |
|=BEBo..          |
|*o++B.  .        |
|++*o + . .       |
|++.o. . S        |
|=o.    + .       |
|.+      .        |
|.                |
|                 |
+----[SHA256]-----+

#깃 허브 연동
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git remote set-url origin git@github.com:cahpi/ai-codyssey.git

#깃 허브 연동 확인
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git remote -v
origin  git@github.com:cahpi/ai-codyssey.git (push)

#깃 허브에 SSH 키를 넣은 후
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ ssh -T git@github.com
Hi cahpi! You've successfully authenticated, but GitHub does not provide shell access.

멀티 컨테이너(Nginx + Redis) 실행하기

멀티 컨테이너(Nginx + Redis) 실행하기

docker-compose.yml 생헝
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    depends_on:
      - cache-db # cache-db가 먼저 실행되어야 함을 명시

  cache-db:
    image: redis:alpine # 가벼운 Redis 이미지 사용

user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker compose up -d
time="2026-07-31T14:58:23+09:00" level=warning msg="C:\\ai-codyssey\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
[+] up 14/14
 ✔ Image redis:alpine               Pulled                              13.1s
 ✔ Image nginx:latest               Pulled                               9.4s
 ✔ Network ai-codyssey_default      Created                              0.0s
 ✔ Container ai-codyssey-cache-db-1 Started                              0.5s
 ✔ Container ai-codyssey-web-1      Started                              0.5s

user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker compose ps
time="2026-07-31T14:58:46+09:00" level=warning msg="C:\\ai-codyssey\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
NAME                     IMAGE          COMMAND                   SERVICE    CREATED          STATUS         PORTS
ai-codyssey-cache-db-1   redis:alpine   "docker-entrypoint.s…"   cache-db   10 seconds ago   Up 8 seconds   6379/tcp
ai-codyssey-web-1        nginx:latest   "/docker-entrypoint.…"   web        9 seconds ago    Up 8 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

*참고: 
ai-codyssey-web-1: 8080 포트로 접속하면 Nginx 웹 서버가 응답
ai-codyssey-cache-db-1: 내부적으로 6379 포트를 사용하는 Redis 데이터베이스가 준비

Docker Compose 운영 (컨테이너 끄기, ps ,로그 확인)

로그확인
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker compose logs -f
time="2026-07-31T15:00:22+09:00" level=warning msg="C:\\ai-codyssey\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
cache-db-1  | Starting Redis Server
cache-db-1  | 1:C 31 Jul 2026 05:58:37.316 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
cache-db-1  | 1:C 31 Jul 2026 05:58:37.316 * Redis version=8.10.0, bits=64, commit=00000000, modified=1, pid=1, just started
cache-db-1  | 1:C 31 Jul 2026 05:58:37.316 * Configuration loaded
cache-db-1  | 1:M 31 Jul 2026 05:58:37.316 * monotonic clock: POSIX clock_gettime
cache-db-1  | 1:M 31 Jul 2026 05:58:37.317 * Running mode=standalone, port=6379.
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf> RedisBloom version 8.10.0 (Git=unknown)
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf> Registering configuration options: [
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf>       { bf-error-rate       :      0.01 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf>       { bf-initial-size     :       100 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf>       { bf-expansion-factor :         2 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf>       { cf-bucket-size      :         2 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf>       { cf-initial-size     :      1024 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf>       { cf-max-iterations   :        20 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf>       { cf-expansion-factor :         1 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf>       { cf-max-expansions   :        32 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * <bf> ]
cache-db-1  | 1:M 31 Jul 2026 05:58:37.318 * Module 'bf' loaded from /usr/local/lib/redis/modules//redisbloom.so
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> search-workers default: 16 (min of MAX_WORKER_THREADS=16 and CPU cores)
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> Redis version found by RedisSearch : 8.10.0 - oss
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> RediSearch version 8.10.0 (Git=)
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> gc: ON, prefix min length: 2, min word length to stem: 4, prefix max expansions: 200, query timeout (ms): 500, timeout policy: return, oom policy: return, cursor read size: 1000, cursor max idle (ms): 300000, max doctable size: 1000000, max number of search results:  1000000, default scorer: BM25STD, 
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> Initialized thread pools!
web-1       | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> Enabled workers threadpool of size 16
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> Subscribe to config changes
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> Subscribe to cluster slot migration events
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> Subscribe to cluster topology change events
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> Enabled role change notification
cache-db-1  | 1:M 31 Jul 2026 05:58:37.321 * <search> Cluster configuration: AUTO partitions, type: 0, coordinator timeout: 0ms
web-1       | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * Module 'search' loaded from /usr/local/lib/redis/modules//redisearch.so
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries> RedisTimeSeries version 81000, git_sha=
web-1       | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
web-1       | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
web-1       | 10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries> Redis version found by RedisTimeSeries : 8.10.0 - oss
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries> Registering configuration options: [
web-1       | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
web-1       | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
web-1       | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
web-1       | /docker-entrypoint.sh: Configuration complete; ready for start up
web-1       | 2026/07/31 05:58:37 [notice] 1#1: using the "epoll" event method
web-1       | 2026/07/31 05:58:37 [notice] 1#1: nginx/1.31.3
web-1       | 2026/07/31 05:58:37 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
web-1       | 2026/07/31 05:58:37 [notice] 1#1: OS: Linux 6.18.33.2-microsoft-standard-WSL2
web-1       | 2026/07/31 05:58:37 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-compaction-policy   :              }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-num-threads         :            3 }
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker processes
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 29
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-libmr-protocol      :     INTERNAL }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-retention-policy    :            0 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-duplicate-policy    :        block }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-chunk-size-bytes    :         4096 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-encoding            :   compressed }
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 30
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 31
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 32
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 33
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 34
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 35
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 36
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 37
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 38
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 39
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 40
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 41
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-ignore-max-time-diff:            0 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-ignore-max-val-diff :     0.000000 }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries>       { ts-topology-events     :         true }
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries> ]
cache-db-1  | 1:M 31 Jul 2026 05:58:37.322 * <timeseries> Detected redis oss (cluster-enabled=no)
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <timeseries> Subscribe to ASM events
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <timeseries> Subscribe to topology changes events
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <timeseries> Enabled diskless replication
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 42
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 43
web-1       | 2026/07/31 05:58:37 [notice] 1#1: start worker process 44
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * Module 'timeseries' loaded from /usr/local/lib/redis/modules//redistimeseries.so
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Created new data type 'ReJSON-RL'
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> version: 81000 git sha: unknown branch: unknown
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Exported RedisJSON_V1 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Exported RedisJSON_V2 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Exported RedisJSON_V3 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Exported RedisJSON_V4 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Exported RedisJSON_V5 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Exported RedisJSON_V6 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Exported RedisJSON_V7 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Exported RedisJSON_V8 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Enabled diskless replication
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <ReJSON> Initialized shared string cache, thread safe: true.
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * Module 'ReJSON' loaded from /usr/local/lib/redis/modules//rejson.so
cache-db-1  | 1:M 31 Jul 2026 05:58:37.323 * <search> Acquired RedisJSON_V8 API
cache-db-1  | 1:M 31 Jul 2026 05:58:37.324 * Server initialized
cache-db-1  | 1:M 31 Jul 2026 05:58:37.324 * Ready to accept connections tcp
cache-db-1  | 1:M 31 Jul 2026 05:58:37.324 # WARNING: Redis does not require authentication and is not protected by network restrictions. Redis will accept connections from any IP address on any network interface.

Compose 프로젝트의 상태 확인
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker compose ps
time="2026-07-31T15:15:07+09:00" level=warning msg="C:\\ai-codyssey\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
NAME                     IMAGE          COMMAND                   SERVICE    CREATED         STATUS         PORTS
ai-codyssey-cache-db-1   redis:alpine   "docker-entrypoint.s…"   cache-db   6 minutes ago   Up 6 minutes   6379/tcp
ai-codyssey-web-1        nginx:latest   "/docker-entrypoint.…"   web        6 minutes ago   Up 6 minutes   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

컨테이너 끄기
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker compose down
time="2026-07-31T15:02:00+09:00" level=warning msg="C:\\ai-codyssey\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
[+] down 3/3
 ✔ Container ai-codyssey-web-1      Removed                                                                                                                     0.3s
 ✔ Container ai-codyssey-cache-db-1 Removed                                                                                                                     0.3s
 ✔ Network ai-codyssey_default      Removed    

컨테이너 간 통신 확인 
1. 컨테이너 다시 실행
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker compose up -d
time="2026-07-31T15:08:37+09:00" level=warning msg="C:\\ai-codyssey\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
[+] up 3/3
 ✔ Network ai-codyssey_default      Created                                                                                                                     0.0s
 ✔ Container ai-codyssey-cache-db-1 Started                                                                                                                     0.5s
 ✔ Container ai-codyssey-web-1      Started 

2. 웹 서버(web) 컨테이너 내부로 접속하기
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker exec -it ai-codyssey-web-1 bash
root@61993b98c526:/# 

3. 컨테이너 내부 연결 확인을 위해서 ping 도구 설치
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker exec -it ai-codyssey-web-1 bash
root@61993b98c526:/# apt-get update && apt-get install -y iputils-ping
Get:1 http://deb.debian.org/debian trixie InRelease [140 kB]
Get:2 http://deb.debian.org/debian trixie-updates InRelease [47.3 kB]
Get:3 http://deb.debian.org/debian-security trixie-security InRelease [43.4 kB]
Get:4 http://deb.debian.org/debian trixie/main amd64 Packages [9673 kB]
Get:5 http://deb.debian.org/debian trixie-updates/main amd64 Packages [4412 B]
Get:6 http://deb.debian.org/debian-security trixie-security/main amd64 Packages [229 kB]
Fetched 10.1 MB in 1s (6873 kB/s)                       
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  linux-sysctl-defaults
The following NEW packages will be installed:
  iputils-ping linux-sysctl-defaults
0 upgraded, 2 newly installed, 0 to remove and 2 not upgraded.
Need to get 57.0 kB of archives.
After this operation, 211 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian trixie/main amd64 iputils-ping amd64 3:20240905-3 [51.2 kB]
Get:2 http://deb.debian.org/debian trixie/main amd64 linux-sysctl-defaults all 4.12.1 [5724 B]
Fetched 57.0 kB in 0s (1215 kB/s)               
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 79, <STDIN> line 2.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC entries checked: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.40.1 /usr/local/share/perl/5.40.1 /usr/lib/x86_64-linux-gnu/perl5/5.40 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.40 /usr/share/perl/5.40 /usr/local/lib/site_perl) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 8, <STDIN> line 2.)
debconf: falling back to frontend: Teletype
Selecting previously unselected package iputils-ping.
(Reading database ... 6704 files and directories currently installed.)
Preparing to unpack .../iputils-ping_3%3a20240905-3_amd64.deb ...
Unpacking iputils-ping (3:20240905-3) ...
Selecting previously unselected package linux-sysctl-defaults.
Preparing to unpack .../linux-sysctl-defaults_4.12.1_all.deb ...
Unpacking linux-sysctl-defaults (4.12.1) ...
Setting up linux-sysctl-defaults (4.12.1) ...
Setting up iputils-ping (3:20240905-3) ...

4. 서비스 이름으로 "핑(Ping)" 날려보기
root@61993b98c526:/# ping cache-db
PING cache-db (172.18.0.2) 56(84) bytes of data.
64 bytes from ai-codyssey-cache-db-1.ai-codyssey_default (172.18.0.2): icmp_seq=1 ttl=64 time=0.091 ms
64 bytes from ai-codyssey-cache-db-1.ai-codyssey_default (172.18.0.2): icmp_seq=2 ttl=64 time=0.099 ms
64 bytes from ai-codyssey-cache-db-1.ai-codyssey_default (172.18.0.2): icmp_seq=3 ttl=64 time=0.195 ms
64 bytes from ai-codyssey-cache-db-1.ai-codyssey_default (172.18.0.2): icmp_seq=4 ttl=64 time=0.053 ms
64 bytes from ai-codyssey-cache-db-1.ai-codyssey_default (172.18.0.2): icmp_seq=5 ttl=64 time=0.070 ms
64 bytes from ai-codyssey-cache-db-1.ai-codyssey_default (172.18.0.2): icmp_seq=6 ttl=64 time=0.065 ms
64 bytes from ai-codyssey-cache-db-1.ai-codyssey_default (172.18.0.2): icmp_seq=7 ttl=64 time=0.095 ms
64 bytes from ai-codyssey-cache-db-1.ai-codyssey_default (172.18.0.2): icmp_seq=8 ttl=64 time=0.112 ms
^C
--- cache-db ping statistics ---
8 packets transmitted, 8 received, 0% packet loss, time 7126ms
rtt min/avg/max/mdev = 0.053/0.097/0.195/0.041 ms

서비스 디스커버리 (Service Discovery) 성공
상황: 당신은 ping 172.18.0.2라고 IP 주소를 치지 않음, 대신 ping cache-db라고 서비스 이름을 입력
의미: Docker Compose가 내부적으로 DNS 서버를 운영하고 있다는 증거입니다. cache-db라는 이름을 가진 컨테이너가 어떤 IP를 가지고 있는지 자동으로 찾아내서 연결해준 것입니다.
컨테이너는 삭제되고 다시 생성될 때마다 IP가 바뀔 수 있습니다. 하지만 서비스 이름(cache-db)은 고정되어 있죠. 덕분에 우리는 IP가 바뀌어도 코드를 수정할 필요가 없습니다.
2. 컨테이너 간 네트워크 격리 및 연결
상황: PING cache-db (172.18.0.2)
의미: web 컨테이너와 cache-db 컨테이너가 ai-codyssey_default라는 가상의 전용 네트워크에 함께 묶여 있다는 뜻입니다.


웹서버 + 임의 컨테이너 2개 통신 실습
docker-compose.yml 만듬
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"

  cache-db:
    image: redis:latest

  app-worker:
    image: alpine
    # alpine은 실행할 명령이 없으면 바로 종료되므로, 계속 켜져 있게 설정합니다.
    command: ["tail", "-f", "/dev/null"]

# 1. 컨테이너 재실행 (-d는 백그라운드 실행)
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker compose up -d
time="2026-07-31T15:18:58+09:00" level=warning msg="C:\\ai-codyssey\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
[+] up 16/16
 ✔ Image redis:latest                 Pulled                                                                                                                    7.2s
 ✔ Image alpine                       Pulled                                                                                                                    6.7s
 ✔ Container ai-codyssey-web-1        Running                                                                                                                   0.0s
 ✔ Container ai-codyssey-cache-db-1   Started                                                                                                                   0.8s
 ✔ Container ai-codyssey-app-worker-1 Started     


# 2. 3개의 서비스가 모두 running인지 확인
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker compose ps
time="2026-07-31T15:19:26+09:00" level=warning msg="C:\\ai-codyssey\\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
NAME                       IMAGE          COMMAND                   SERVICE      CREATED          STATUS          PORTS
ai-codyssey-app-worker-1   alpine         "tail -f /dev/null"       app-worker   20 seconds ago   Up 19 seconds   
ai-codyssey-cache-db-1     redis:latest   "docker-entrypoint.s…"   cache-db     20 seconds ago   Up 19 seconds   6379/tcp
ai-codyssey-web-1          nginx:latest   "/docker-entrypoint.…"   web          10 minutes ago   Up 10 minutes   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

(1) Worker -> Web 통신 확인 (3 packets)
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker exec -it ai-codyssey-app-worker-1 ping -c 3 web
PING web (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.240 ms
64 bytes from 172.18.0.3: seq=1 ttl=64 time=0.083 ms
64 bytes from 172.18.0.3: seq=2 ttl=64 time=0.141 ms

--- web ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.083/0.154/0.240 ms

(2) Worker -> Cache 통신 확인
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker exec -it ai-codyssey-app-worker-1 ping -c 3 cache-db
PING cache-db (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.173 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.119 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.092 ms

--- cache-db ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.092/0.128/0.173 ms

(3) Web -> Worker 통신 확인 (역방향)
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ docker exec -it ai-codyssey-web-1 ping -c 3 app-worker
PING app-worker (172.18.0.4) 56(84) bytes of data.
64 bytes from ai-codyssey-app-worker-1.ai-codyssey_default (172.18.0.4): icmp_seq=1 ttl=64 time=0.067 ms
64 bytes from ai-codyssey-app-worker-1.ai-codyssey_default (172.18.0.4): icmp_seq=2 ttl=64 time=0.197 ms
64 bytes from ai-codyssey-app-worker-1.ai-codyssey_default (172.18.0.4): icmp_seq=3 ttl=64 time=0.053 ms

--- app-worker ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2047ms
rtt min/avg/max/mdev = 0.053/0.105/0.197/0.064 ms

*참고: web: 외부 손님을 맞이하는 창구 (포트 포워딩 8080:80)
cache-db: 데이터를 임시 저장하는 창고 (외부 노출 없음)
app-worker: 뒤에서 묵묵히 일을 처리하는 일꾼 (외부 노출 없음)
결과: 외부에서는 web만 보이지만, 내부에서는 세 친구가 긴밀하게 협업하는 구조 완성


#깃 허브 업로드
user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git add docker-compose.yml

user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git commit -m "Add app-worker and verify 3-way container communication"
[main f9a498c] Add app-worker and verify 3-way container communication
 1 file changed, 15 insertions(+)
 create mode 100644 docker-compose.yml

user@DESKTOP-BOQ4DKE MINGW64 /c/ai-codyssey (main)
$ git push origin main
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 16 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 555 bytes | 277.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:cahpi/ai-codyssey.git
   30a8537..f9a498c  main -> main