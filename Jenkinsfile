pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker') // Jenkins에 등록한 Docker Hub 자격 증명
        IMAGE_NAME = "luckyjakey/nginx" // Docker Hub 이미지 이름
        LATEST_TAG = "latest" // 이미지 태그 (원하는 태그로 변경)
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
                    def buildImage = docker.build("${IMAGE_NAME}:${BUILD_TAG}", "--file Dockerfile .")

                    // Docker 이미지를 .tar 파일로 저장
                    sh "docker save -o latest_image.tar ${IMAGE_NAME}:${LATEST_TAG}"
                    sh "docker save -o build_image.tar ${IMAGE_NAME}:${BUILD_TAG}"

                    // 아티팩트로 Docker 이미지 저장
                    archiveArtifacts artifacts: 'latest_image.tar,build_image.tar', fingerprint: true
                }
            }
        }

        stage('Deploy to NKS') {
            steps {
                script {
                    // Kubernetes 클러스터에 연결
                    withKubeConfig(credentialsId: 'nks-pipeline', doNotReplace: true) {
                        // Kubernetes 클러스터에 배포
                        sh "kubectl rollout restart deployment/nginx-deployment"
                    }
                }
            }
        }
    }
}
