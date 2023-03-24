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
    - name: cloud-sdk
      image: google/cloud-sdk
      command:
      - sleep
      args:
      - 9999999
      volumeMounts:
      - name: shared-storage
        mountPath: /mnt
      - name: google-cloud-key
        mountPath: /var/secrets/google
      env:
      - name: GOOGLE_APPLICATION_CREDENTIALS
        value: /var/secrets/google/devops-381618-7c42d8f9ea22.json
    restartPolicy: Never
    volumes:
    - name: shared-storage
      persistentVolumeClaim:
        claimName: jenkins-pv-claim-new
    - name: google-cloud-key
      secret:
        secretName: sdk-key
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
    post {
        success {
            container('cloud-sdk') {
                sh '''
                gcloud auth login --cred-file=$GOOGLE_APPLICATION_CREDENTIALS
                gcloud container clusters get-credentials devops-cluster --region us-east1 --project devops-381618
                kubectl apply -f calculator.yaml
                kubectl apply -f hazelcast.yaml
                '''
            }
        }
    }
}