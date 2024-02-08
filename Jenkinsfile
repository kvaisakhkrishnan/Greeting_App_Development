pipeline {
    agent { label 'jenkins-minion' }

    stages {

        stage('SonarQube Analysis') {
            steps {
                script {
                    def mvn = tool 'maven';
                    withSonarQubeEnv('SonarQubeServer') {
                        sh "/home/jenkins/maven/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=greetings -f pom.xml"
                    }
                }
            }
        }
        stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
       stage('Build') {
        // Run the maven build
		    steps {
			    script {
				      def mvn = tool 'maven';
                                      sh "${mvn}/bin/mvn -Dmaven.test.failure.ignore clean package"
                      }
                 }
				 }
       stage('Test') {
		    steps {
			    script {
				      def mvn = tool 'maven';
                                      sh "${mvn}/bin/mvn -Dmaven.test.failure.ignore=true test"
                      }
                 }
				 }
       stage('Result') {
		    steps {
			    script {
				      def mvn = tool 'maven';
                                      junit(testResults: 'target/surefire-reports/*.xml', skipMarkingBuildUnstable: true, allowEmptyResults : true)
                      }
                 }
				 }
 
     stage('Deploy') {
                 steps {
                       script {
 
                             sh 'rsync -avz $WORKSPACE/target/GreetingApp-0.0.1-SNAPSHOT.jar jenkins@10.0.30.218:/tmp/'
                              }
                        }
                     }
 
}
}
