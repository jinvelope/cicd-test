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
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command:
    - sleep
    args:
    - 9999999
    volumeMounts:
    - name: workspace-volume
      mountPath: /home/jenkins/agent
  - name: kubectl
    image: bitnami/kubectl:latest
    command:
    - sleep
    args:
    - 9999999
    securityContext:
      runAsUser: 0
  volumes:
  - name: workspace-volume
    emptyDir: {}
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
                container('kaniko') {
                    sh '''
                        /kaniko/executor \
                          --context=/home/jenkins/agent/workspace/github-cicd \
                          --dockerfile=/home/jenkins/agent/workspace/github-cicd/Dockerfile \
                          --destination=cicd-test:latest \
                          --no-push \
                          --tarPath=/home/jenkins/agent/cicd-test.tar
                    '''
                    echo 'Docker 이미지 빌드 완료'
                }
            }
        }
        stage('Deploy to K8s') {
            steps {
                container('kubectl') {
                    sh '''
                        kubectl apply -f k8s-deployment.yaml
                        kubectl rollout status deployment/cicd-test
                        echo "배포 완료"
                    '''
                }
            }
        }
    }
}
