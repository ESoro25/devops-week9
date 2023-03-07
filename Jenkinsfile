pipeline {
    agent {
        kubernetes {
        yaml '''
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
        stage('Main Branch Tests') {
            when { branch 'main'}
            steps {
                container('gradle') {
                sh '''
                ./gradlew build
                cp ./build/libs/calculator-0.0.1-SNAPSHOT.jar /mnt
                ./gradlew test
                ./gradlew jacocoTestCoverageVerification
                ./gradlew CheckstyleMain
                '''  
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
        stage('Feature Branch Tests') {
            when { branch 'feature'}
            steps {
                container('gradle') {
                sh '''
                ./gradlew build
                cp ./build/libs/calculator-0.0.1-SNAPSHOT.jar /mnt
                ./gradlew test
                ./gradlew CheckstyleMain
                '''  
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
