podTemplate(containers: [
  containerTemplate(
    name: 'gradle',
    image: 'gradle:6.3-jdk14',
    command: 'sleep',
    args: '30d'
    ),
  ]) {
    node(POD_LABEL) {
      stage('Run pipeline against a gradle project) {
	container('gradle') {
          stage('Main Branch Unit Tests') {
		when {
		    branch 'main'
		}
	    	steps {
            	echo "I am in the ${env.BRANCH_NAME} branch"
		chmod +x gradlew
		./gradlew jacocoTestCoverageVerification
		}
          }
          stage('Other Branch Unit Tests') {
		when {
		    not {
		        branch 'feature'
		    }	
		}
	    	steps {
            	echo "I am in the ${env.BRANCH_NAME} branch"
		chmod +x gradlew
		./gradlew Checkstylemain
		}
          }
        }
      }   
    }
}
