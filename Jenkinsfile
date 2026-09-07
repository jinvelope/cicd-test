pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: jnlp
    image: jenkins/inbound-agent:latest
  - name: dind
    image: docker:24-dind
    securityContext:
      privileged: true
    env:
    - name: DOCKER_TLS_CERTDIR
      value: ""
  - name: docker-cli
    image: docker:24-cli
    env:
    - name: DOCKER_HOST
      value: tcp://localhost:2375
"""
        }
    }
    stages {
        stage('Checkout') {
            steps {
                echo '코드 체크아웃 완료'
                sh 'ls -la'
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker-cli') {
                    sh 'sleep 5 && docker build -t cicd-test:latest .'
                    echo 'Docker 이미지 빌드 완료'
                }
            }
        }
        stage('Deploy to K8s') {
            steps {
                sh '''
                    kubectl apply -f k8s-deployment.yaml
                    echo "배포 완료"
                '''
            }
        }
    }
}
