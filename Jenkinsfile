pipeline {
    agent any
    
    environment { 
        repository = "jen_tomcat" // Docker Hub ID와 Repository 이름
        DOCKERHUB_CREDENTIALS = credentials('docker') // Jenkins에 등록한 Docker Hub credentials 이름
        dockerImage = '' 
    }

    stages {
        stage('Checkout') {
            steps {
                // 소스 코드 체크아웃
                checkout scm
            }
        }
        
        stage('Make Gradlew Executable') {
            steps {
                // gradlew 파일에 실행 권한 부여
                sh 'chmod +x ./gradlew'
            }
        }

        stage('Build with Gradle') {
            steps {
                // Gradle 빌드 수행
                sh './gradlew clean build'
            }
        }

        stage('Copy war to Dockerfile Folder') {
            steps {
                // Gradle 빌드에서 생성된 sample.war 파일을 Dockerfile 폴더로 복사
                sh ' cp ./build/libs/*.war ./'
                sh 'ls .'
            }
        }
        
        stage('Build image') {
            steps {
                script {
                    app = docker.build("-t kkmee0209 .")
                }
            }
        }
        
        stage('Push image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker') { // 업로드할 레지스트리 정보, Jenkins Credentials ID 
                        app.push("latest") // 이미지에 latest를 태그로 붙인 후 Push
                    }
                }
            }
        }

        stage('Cleaning up') { 
            steps { 
                sh "docker rmi $repository:$env.BUILD_ID" // Docker 이미지 제거
            }
        }
    }
}
