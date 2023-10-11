pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker') // Jenkins에 등록한 Docker Hub 자격 증명
        IMAGE_NAME = "luckyjakey/nginx" // Docker Hub 이미지 이름
        TAG = "latest" // 이미지 태그 (원하는 태그로 변경)
        BUILD_TAG = "${BUILD_NUMBER}" // 빌드 번호를 태그로 사용
        KUBE_CONFIG = credentials('nks-pipeline') // Kubernetes 클러스터 구성 파일 (Kubeconfig) 자격 증명
    }

    stages {
        stage('Checkout') {
            steps {
                // 소스 코드 체크아웃 (예: Git, SVN 등)
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Docker 이미지 빌드
                    def latestImage = docker.build("${IMAGE_NAME}:${LATEST_TAG}", "--file Dockerfile .")

                    // Docker 이미지 빌드 (빌드 번호 태그)
                    def buildImage = docker.build("${IMAGE_NAME}:${BUILD_TAG}", "--file Dockerfile .")

                    // Docker Hub에 로그인
                    docker.withRegistry('https://index.docker.io/v1/', 'docker') {
                       // Docker 이미지 푸시 (latest 태그)
                        latestImage.push()
                        // Docker 이미지 푸시 (빌드 번호 태그)
                        buildImage.push()
                    }
                }
            }
        }

        stage('Deploy to NKS') {
            steps {
                script {
                    // Kubernetes 클러스터에 연결
                    withKubeConfig(credentialsId: 'nks-pipeline', doNotReplace: true) {
                        // Kubernetes 클러스터에 배포
                        sh "kubectl set image deployment/nginx-deployment nginx=${IMAGE_NAME}:${LATEST_TAG}"                        
                        // Kubernetes 클러스터에 배포 (Build 태그)
                        //sh "kubectl set image deployment/nginx-deployment nginx=${IMAGE_NAME}:${BUILD_TAG}"
                        sh "kubectl rollout restart deployment/nginx-deployment"
                    }
                }
            }
        }
    }
}
