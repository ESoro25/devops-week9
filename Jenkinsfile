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
                echo 'Updating Calculator...'
                ./kubectl apply -f calculator.yaml -n staging
                '''
            }
        }
        stage('Validate Update') {
            steps {
                sh '''
                ./kubectl get pods -n staging
                echo 'Sum Test'
                test $(curl calculator-service.staging.svc.cluster.local:8080/sum?a=3\\&b=4) -eq 7 && echo 'pass' || echo 'fail'
                echo 'Div Test'
                test $(curl calculator-service.staging.svc.cluster.local:8080/div?a=6\\&b=3) -eq 3 && echo 'pass' || echo 'fail'
                '''
            }
        }
    }
}