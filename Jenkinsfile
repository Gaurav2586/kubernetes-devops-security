pipeline {
  agent {
      label 'jenkins-slave'
	}

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        }  
    }
}

