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

        //생성된 jarfile은 jenkins디렉토리 아래에 위치시키기
        stage('Copy JAR') {
            steps {
                script {
                    // Correct jarFile path to the actual JAR file, not the .original
                    def jarFile = 'target/step19-1-0.0.1-SNAPSHOT.jar'
                    sh "cp ${jarFile} /var/jenkins_home/appjar/"
                }
            }
        }
    }
}
```
![image](https://github.com/user-attachments/assets/3d711507-c3d7-4f98-916d-4fc7527d0766)


Maven 프로젝트에서 JAR 파일은 기본적으로 target 디렉토리 아래에 생성됩니다.
<br/>
mvn clean install 또는 mvn clean package 명령어를 실행하면, Maven은 프로젝트를 빌드하고 결과물(JAR, WAR 등)을 target 디렉토리에 생성합니다.
<br/>
예시:
프로젝트 루트에 있는 pom.xml 파일을 기준으로 빌드를 진행하면, 프로젝트 구조는 다음과 같이 됩니다:

```bash
/workspace/
   ├── src/
   ├── target/
   │   ├── project-name-version.jar
   ├── pom.xml
```
빌드 후, target/ 디렉토리 아래에 빌드된 JAR 파일이 위치하게 됩니다. 이 디렉토리는 Maven이 컴파일된 클래스 파일, 패키징된 파일(JAR, WAR 등)을 저장하는 기본 위치입니다.

