# Jenkins & Docker

```bash
1단계 - host에 app jar 보관할 폴더 생성
mkdir appjar


2단계 - volume 생성 및 volume 생성 확인
docker volume create appjardir
sudo ls /var/lib/docker/volumes



3단계 - jenkins container 생성
docker run --name myjenkins --privileged -p 8080:8080  -v  $(pwd)/appjardir:/var/jenkins_home/appjar   jenkins/jenkins:lts-jdk17


4단계 - container 내부의 var/jenkins_home/경로에 appjar 디렉토리 생성
```
![image](https://github.com/user-attachments/assets/fc917ee5-85be-4497-9006-ffe34c9c3e05)

