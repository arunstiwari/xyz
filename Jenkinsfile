pipeline{
	agent any

	stages{
		stage('UnitTest') {
			steps {

				sh '/usr/local/bin/mvn -f ./spring-examples/jpa-demo/pom.xml clean test'
			}
			post{
				success{
					junit allowEmptyResults: true, testResults: './target/surefire-reports/*.xml'
				}
			}
		}
		stage('Build') {
			steps {
				sh '/usr/local/bin/mvn -f ./spring-examples/jpa-demo/pom.xml package -DskipTests=true'
			}
			post {
				success {
					archiveArtifacts(artifacts: '**/target/*.jar', allowEmptyArchive: true)
				}
			}
		}
		stage('Checkstyle') {
			steps {
				sh 'mvn checkstyle:checkstyle'
			}
		}
		stage('Jacoco') {
			steps {
				sh 'mvn jacoco:report'
			}
		}
		stage('Reports-Junit') {
			steps {
				junit allowEmptyResults: true, testResults: './target/surefire-reports/*.xml'
			}
		}
		stage('MutationTest'){
			steps {
				sh 'mvn org.pitest:pitest-maven:mutationCoverage'
			}
		}
		stage('Checkstyle-Reports') {
			steps{
				checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: 'target/checkstyle-result.xml', unHealthy: ''
			}
		}

		stage('CodeCoverage-Reports') {
			steps{
				step( [ $class: 'JacocoPublisher' ] )
			}
		}

		stage('MutationCoverage') {
			steps {
				publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'target/pit-reports', reportFiles: '**/index.html', reportName: 'Mutation Reports', reportTitles: 'MutationReport'])
			}
		}
	  }
}
