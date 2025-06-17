# Model
## docker-upload-250616

![스크린샷 2025-06-16 110957](https://github.com/user-attachments/assets/e04c4730-1219-43e8-95a2-6356770d65f7)
![스크린샷 2025-06-16 111803](https://github.com/user-attachments/assets/73f3af65-ebaf-4b3b-ac3b-64cd1d4bfc8c)
![스크린샷 2025-06-16 114121](https://github.com/user-attachments/assets/73d72268-e417-4954-97aa-9d494f01fc8a)
![스크린샷 2025-06-16 114824](https://github.com/user-attachments/assets/9626ccd0-2d47-4396-9559-26c9abf23c2a)
![스크린샷 2025-06-16 115021](https://github.com/user-attachments/assets/abeecb7a-0a6c-42cf-8fce-fa87ac7a7106)
![스크린샷 2025-06-16 115128](https://github.com/user-attachments/assets/37eaba11-a35c-4555-a63d-78f3530baefb)
![스크린샷 2025-06-16 115638](https://github.com/user-attachments/assets/3e93e841-6a1a-4bbe-9cf1-0c9fa95c795e)
![스크린샷 2025-06-16 115802](https://github.com/user-attachments/assets/e3a3384d-c5f0-44b1-9e51-b45e335cc48f)
![스크린샷 2025-06-16 115806](https://github.com/user-attachments/assets/a424272c-d813-42e8-a1c8-c122c2702b54)
![스크린샷 2025-06-16 120251](https://github.com/user-attachments/assets/7b469b62-d018-45ad-8171-76aa2d79d429)
![스크린샷 2025-06-16 120753](https://github.com/user-attachments/assets/2adfe947-4dd6-4494-8ed9-7c9c2519ed04)
![스크린샷 2025-06-16 120803](https://github.com/user-attachments/assets/6cc5dd56-dd33-4e64-a4c2-79e57aa6f75d)
![스크린샷 2025-06-16 120853](https://github.com/user-attachments/assets/b11ecf35-f088-4fda-9e09-ddb56f7b49b4)
![스크린샷 2025-06-16 121812](https://github.com/user-attachments/assets/034e64ea-7cae-4495-ab87-157f51861eca)
![스크린샷 2025-06-16 135223](https://github.com/user-attachments/assets/a0a6b1c7-d88e-4cb1-a860-144680ad112a)
![스크린샷 2025-06-16 135429](https://github.com/user-attachments/assets/8bf7f1ca-b943-4a27-b3f0-ea0137d5b70c)

## Docker 기본 컨테이너부터 Java 애플리케이션 도커라이징까지
- Docker를 사용하여 Nginx 컨테이너 생성 및 포트 바인딩을 실습하고, Dockerfile로 커스텀 웹 이미지를 빌드하여 Docker Hub에 푸시했습니다.
- 이어서 Spring Boot 애플리케이션(Inventory, Monolith)을 Docker 이미지로 만들어 컨테이너화하고, CI/CD 기반 배포의 기초 과정을 경험했습니다.

Docker 실습 내용
이 문서는 Docker의 기본적인 이미지/컨테이너 관리부터 Dockerfile을 이용한 커스텀 이미지 빌드, 그리고 실제 Java 애플리케이션을 Docker 컨테이너로 만드는 과정을 담고 있습니다.

실습 단계
1. 이미지 기반 컨테이너 생성 및 확인
- Nginx 이미지를 다운로드하고, 포트 바인딩을 다르게 하여 여러 개의 Nginx 컨테이너를 실행합니다.
```
# Nginx 이미지 다운로드
docker image pull nginx:latest

# 첫 번째 Nginx 컨테이너 실행 (로컬 8080 포트와 바인딩)
docker run --name my-nginx -d -p 8080:80 nginx:latest

# 두 번째 Nginx 컨테이너 실행 (로컬 8081 포트와 바인딩)
docker run --name my-new-nginx -d -p 8081:80 nginx:latest

# 실행 중인 컨테이너 목록 확인
docker container ls
# 또는
# docker ps
```
- 참고: 컨테이너는 격리되어 있으므로, 내부 포트(예: Nginx 기본 포트 80)는 고정적으로 유지하고, docker run 시 --p 옵션을 사용하여 호스트 포트를 다르게 바인딩할 수 있습니다.

2. 컨테이너 접속 확인
- httpie를 사용하여 실행 중인 Nginx 웹 서버에 접속하여 정상 동작을 확인합니다.
```
# httpie 설치 (설치되어 있지 않다면)
# pip install httpie

# 첫 번째 Nginx 컨테이너에 접속
http GET http://localhost:8080

# 두 번째 Nginx 컨테이너에 접속
http GET http://localhost:8081
```
- 결과: 두 요청 모두 Nginx 웹 서버에 성공적으로 접속했습니다. (HTTP/1.1 200 OK)

3. 이미지 및 컨테이너 삭제
- 실행 중인 컨테이너는 이미지를 삭제할 수 없으므로, 컨테이너를 먼저 중지하고 삭제한 후 이미지를 삭제합니다.
```
# 실행 중인 컨테이너 목록 확인
docker container ls

# 컨테이너 중지
docker container stop my-nginx
docker container stop my-new-nginx

# 컨테이너 삭제
docker container rm my-nginx
docker container rm my-new-nginx

# Nginx 이미지 삭제
docker image rm nginx:latest

# 남아있는 이미지 목록 확인
docker images
```
4. 나만의 Docker 이미지 만들기
- 웹 페이지(index.html)와 Dockerfile을 생성하여 나만의 Nginx 기반 웹 서버 이미지를 빌드합니다.
```
# 상위에 Docker 폴더 생성 및 이동
mkdir Docker
cd Docker/

# index.html 파일 생성 및 내용 복사 (아래 내용은 파일에 붙여넣기)
# <html>
# <body>
# <center>
# <br/></br>
# <img src="https://raw.githubusercontent.com/acmexii/demo/master/materials/smile.jpg">
# <br/></br>
# <h1> Hi~ My name is Hong Gil-Dong...~~~ </h1>
# </center>
# </body>
# </html>

# Dockerfile 파일 생성 및 내용 복사 (아래 내용은 파일에 붙여넣기)
# FROM nginx
# COPY index.html /usr/share/nginx/html/

# 이미지 빌드 (현재 디렉토리의 Dockerfile을 사용, 'sukuai'는 본인의 Docker Hub ID로 변경)
docker build -t sukuai/welcome:v1 .

# 빌드된 이미지 목록 확인
docker image ls
```
5. Docker Hub 로그인 및 이미지 푸시
- 로컬에서 빌드한 이미지를 Docker Hub에 업로드합니다.
```
# Docker Hub 로그인 (프롬프트에 따라 사용자 이름, 비밀번호 입력)
docker login

# 빌드한 이미지를 Docker Hub에 푸시
docker push sukuai/welcome:v1
```
6. Docker Hub 이미지 기반 컨테이너 생성
- Docker Hub에서 이미지를 가져와 컨테이너를 실행하고, 웹 서비스가 잘 동작하는지 확인합니다.
```
# (선택 사항) 로컬에 있는 'sukuai/welcome:v1' 이미지 삭제 (Docker Hub에서 다시 가져오는지 확인 목적)
docker image rm sukuai/welcome:v1

# Docker Hub에서 이미지를 가져와 'welcome' 컨테이너 실행
docker run --name=welcome -d -p 8080:80 sukuai/welcome:v1

# 컨테이너 접속 확인
http http://localhost:8080
```
7. Java Spring Boot 애플리케이션 도커라이징
- Inventory 및 Monolith Spring Boot 애플리케이션을 Docker 이미지로 빌드하고 Docker Hub에 푸시합니다.
```
# (필요시) Java 개발 환경 설정 (Gitpod에서는 일반적으로 sdk install java로 처리)
sdk install java

# Inventory 애플리케이션 도커라이징
cd inventory/
# Maven을 사용하여 애플리케이션 패키징 (테스트 스킵)
mvn package -B -Dmaven.test.skip=true
# 로컬에서 jar 파일 실행 (옵션, 도커 이미지 빌드 전 동작 확인)
# java -jar target/inventory-0.0.1-SNAPSHOT.jar
# Docker 이미지 빌드 ('sukuai'는 본인의 Docker Hub ID로 변경, 태그는 날짜로 예시)
docker build -t sukuai/inventory:250616 .
# 빌드된 이미지 목록 확인
docker images
# Docker Hub에 이미지 푸시
docker push sukuai/inventory:250616

# (옵션) 빌드된 이미지로 컨테이너 실행 (포트 바인딩 필요시 추가: -p 8080:8080)
# docker run sukuai/inventory:250616
cd .. # 상위 디렉토리로 이동

# Monolith 애플리케이션 도커라이징
cd monolith/
# Maven을 사용하여 애플리케이션 패키징 (테스트 스킵)
mvn package -B -Dmaven.test.skip=true
# 로컬에서 jar 파일 실행 (옵션, 도커 이미지 빌드 전 동작 확인)
# java -jar target/monolith-0.0.1-SNAPSHOT.jar
# Docker 이미지 빌드 ('sukuai'는 본인의 Docker Hub ID로 변경, 태그는 날짜로 예시)
docker build -t sukuai/monolith:250616 .
# 빌드된 이미지 목록 확인
docker images
# Docker Hub에 이미지 푸시
docker push sukuai/monolith:250616

# (옵션) 빌드된 이미지로 컨테이너 실행 (포트 바인딩 필요시 추가: -p 8080:8080)
# docker run sukuai/monolith:250616
cd .. # 상위 디렉토리로 이동
```
핵심 개념:  
- Dockerfile 내에서 FROM openjdk:15-jdk-alpine와 같이 특정 자바 환경을 지정하고, COPY target/*.jar app.jar와 같이 빌드된 JAR 파일을 컨테이너로 복사합니다.
- 애플리케이션이 8080 포트를 사용하도록 설정되었다면, Docker 컨테이너 내에서도 해당 포트를 사용하며, 외부에 노출 시 호스트 포트와 바인딩합니다.
- Docker 이미지는 애플리케이션의 실행 환경을 완벽하게 캡슐화하므로, CI/CD 파이프라인을 통해 쉽게 배포 가능한 형태를 만듭니다. latest 태그 대신 250616과 같이 명확한 버전 태그를 사용하는 것이 배포 관리의 원칙입니다.
- Gitpod과 같은 클라우드 환경은 대부분 Linux 기반의 amd64 (또는 intel) 아키텍처이므로 Docker 이미지 빌드가 쉽게 가능합니다.
