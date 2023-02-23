podTemplate(containers: [
    containerTemplate(
        name: 'gradle',
        image: 'gradle:6.3-jdk14',
        command: 'sleep',
        args: '30d'
    ),
]) {
    node(POD_LABEL) {
        stage('Run pipeline against a gradle project') {
            container('gradle') {
                stage('Unit Tests') {
                    if (env.BRANCH_NAME == 'main') {
                        echo "I am in the ${env.BRANCH_NAME} branch"
                        git branch: 'main', url: 'https://github.com/ESoro25/devops-week6'
                        sh '''
                        chmod +x gradlew
                        ./gradlew jacocoTestCoverageVerification
                        '''
                    }
                    else {
                        echo "I am in the ${env.BRANCH_NAME} branch"
                        git branch: ${env.BRANCH_NAME}, url: 'https://github.com/ESoro25/devops-week6'
                        sh '''
                        chmod +x gradlew
                        ./gradlew Checkstylemain
                        '''
                    }
                }
            }
        }
    }
}
