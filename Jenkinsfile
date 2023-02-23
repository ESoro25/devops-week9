pipeline {
    agent any
    stages {
        stage("Compile") {
            steps {
                sh "./gradlew test"
            }
        }
        stage("Code Coverage - MAIN") {
            when {branch 'main'}
            steps {
                echo "I am in the ${env.BRANCH_NAME} branch"
                sh './gradlew jacocoTestCoverageVerification' 
            }
        }
        stage("Unit Tests - OTHER") {
            when {not {branch 'main'}}
            steps {
                echo "I am in the ${env.BRANCH_NAME} branch"
                sh './gradlew CheckstyleMain'   
            }
        }
    }
}
