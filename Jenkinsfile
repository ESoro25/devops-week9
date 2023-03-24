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
        stage('Run Smoke Test') {
            steps {
                container('gradle') {
                    sh '''
                    cd Chapter09/sample3
                    ./gradlew build
                    ./gradlew smokeTest -Dcalculator.url=http://calculator-service.staging.svc.cluster.local:8080
                    ''' 
                }
            }
        }
    }
}