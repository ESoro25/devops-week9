pipeline {
    agent {
        kubernetes {
        yaml '''
apiVersion: v1 
kind: Pod 
spec: 
  containers: 
  - name: gradle 
    image: gradle:6.3-jdk14 
    command: 
    - sleep 
    args: 
    - 99d 
    volumeMounts: 
    - name: shared-storage 
      mountPath: /mnt         
  - name: kaniko 
    image: gcr.io/kaniko-project/executor:debug 
    command: 
    - sleep 
    args: 
    - 9999999 
    volumeMounts: 
    - name: shared-storage 
      mountPath: /mnt 
    - name: kaniko-secret 
      mountPath: /kaniko/.docker 
  restartPolicy: Never 
  volumes: 
  - name: shared-storage 
    persistentVolumeClaim: 
      claimName: jenkins-pv-claim-new 
  - name: kaniko-secret 
    secret: 
        secretName: dockercred 
        items: 
        - key: .dockerconfigjson 
          path: config.json
        '''
        }
    }
    stages {
        stage('Build Grade') {
            when {not { branch 'playground'}}
            steps {
                container('gradle') {    
                sh '''
                ./gradlew build
                cp ./build/libs/calculator-0.0.1-SNAPSHOT.jar /mnt
                '''
                }
            }
        }
        stage('Main Branch') {
            when { branch 'main'}
            stages {
                stage('Unit Test') {
                    steps {
                        container('gradle') {
                        sh './gradlew test'
                        }
                    }
                }
                stage('Code Coverage Test') {
                    steps {
                        container('gradle') {
                        sh './gradlew jacocoTestCoverageVerification'
                        }
                    }
                }
                stage('Checkstyle Test') {
                    steps {
                        container('gradle') {
                        sh './gradlew CheckstyleMain'
                        }
                    }
                }
            }
            post {
                success {
                    container('kaniko') {
                    sh '''
                    echo 'FROM openjdk:8-jre' > Dockerfile
                    echo 'COPY ./calculator-0.0.1-SNAPSHOT.jar app.jar' >> Dockerfile
                    echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
                    mv /mnt/calculator-0.0.1-SNAPSHOT.jar .
                    /kaniko/executor --context `pwd` --destination esoro25/calculator:1.0
                    '''    
                    }
                }
            }
        }
        stage('Feature Branch') {
            when { branch 'feature'}
            stages {
                stage('Unit Test') {
                    steps {
                        container('gradle') {
                        sh './gradlew test'
                        }
                    }
                }
                stage('Checkstyle Test') {
                    steps {
                        container('gradle') {
                        sh './gradlew CheckstyleMain'
                        }
                    }
                }
            }
            post {
                success {
                    container('kaniko') {
                    sh '''
                    echo 'FROM openjdk:8-jre' > Dockerfile
                    echo 'COPY ./calculator-0.0.1-SNAPSHOT.jar app.jar' >> Dockerfile
                    echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
                    mv /mnt/calculator-0.0.1-SNAPSHOT.jar .
                    /kaniko/executor --context `pwd` --destination esoro25/calculator-feature:0.1
                    '''    
                    }
                }
            }
        }
        stage('Playground Branch') {
            when { branch 'playground'}
            steps {
                echo 'Nothing to run in playground branch.'
            }
        }
    }
}
