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
```bash
ngrok http 8080
```

![image](https://github.com/user-attachments/assets/ae34c958-5a51-4fcd-9c7a-932b916ea1da)

![image](https://github.com/user-attachments/assets/3c1c60e3-0b84-4088-9e91-27b5b6daa822)

파이프라인 작성
```bash
pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/seungji2001/fisatest.git'
            }
        }
        
         stage('Build') {
            steps {
                script {
                    // Move to the root directory where the pom.xml and mvnw are located
                    dir("${WORKSPACE}") {
                        // Make mvnw executable (optional if the permissions are already set)
                        sh 'chmod +x mvnw'
                        
                        // Run Maven clean install using Maven Wrapper, skipping tests if needed
                        sh './mvnw clean install -DskipTests'
                        
                        // Output the workspace directory
                        sh 'echo $WORKSPACE'
                    }
                }
            }
        }
        
    }
}
```
