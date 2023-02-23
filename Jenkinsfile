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
                stage('Main Branch Unit Tests') {
                    if (env.BRANCH_NAME == 'main') {
                        echo "I am in the ${env.BRANCH_NAME} branch"
                        chmod +x gradlew
                        ./gradlew jacocoTestCoverageVerification
                    }
                    else {
                        echo "I am in the ${env.BRANCH_NAME} branch"
                        chmod +x gradlew
                        ./gradlew Checkstylemain
                    }
                }
            }
        }
    }
}
