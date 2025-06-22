pipeline {
    agent {
        kubernetes {
            label 'my-k8s-agent'
            defaultContainer 'jnlp'

            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: maven:3.9.3-eclipse-temurin-17
    command: ['cat']
    tty: true
  - name: docker
    image: docker:24.0.6-cli
    command: ['cat']
    tty: true
    volumeMounts:
      - name: docker-sock
        mountPath: /var/run/docker.sock
  - name: kubectl
    image: bitnami/kubectl:latest
    command: ['cat']
    tty: true
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
"""
        }
    }

    environment {
        DOCKER_IMAGE = "naveenkumart55/jenkins_test"
        IMAGE_TAG = "latest"
    }


        stage('Docker Build & Push') {
            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: 'docker hub credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker build -t $DOCKER_IMAGE:$IMAGE_TAG .
                            docker push $DOCKER_IMAGE:$IMAGE_TAG
                        '''
                    }
                }
            }
        }

        stage('K8s Deploy') {
            steps {
                container('kubectl') {
                    sh '''
                        kubectl version --client
                        # Replace with actual deploy command
                        # kubectl apply -f k8s/deployment.yaml
                    '''
                }
            }
        }
    }
}
