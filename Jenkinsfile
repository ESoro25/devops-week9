pipeline {
    agent {
        kubernetes {
        yaml '''
apiVersion: v1 
kind: Pod 
spec: 
  containers: 
  - name: gradle 
    image: gradle:jdk8 
    command: 
    - sleep 
    args: 
    - 99d 
  restartPolicy: Never 
        '''
        }
    }
    stages {
        stage('Update Calculator') {
            steps {
                sh '''
                curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
                chmod +x kubectl
                echo 'Switch to Staging Namespace...'
                ./kubectl config set-context --current --namespace=staging
                echo 'Updating Calculator...'
                ./kubectl apply -f calculator.yaml
                '''
            }
        }
    }
}