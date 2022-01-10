pipeline {
  agent {
      label 'kubepod'
	}

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        }  
      stage('Unit test & JoCoCo') {
            steps {
              sh "mvn test"
            }
            post {
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
        }
      stage('Docker Build and Push') {
            steps {
               withDockerRegistry([gcr: "gcr-repo-cred", url: "https://gcr.io"]) {
                   sh 'printenv'
                   sh 'docker build -t gcr.io/absolute-realm-333214/numeric-app:""$GIT_COMMIT"" .'
                   sh 'docker push gcr.io/absolute-realm-333214/numeric-app:""$GIT_COMMIT""'
        }
      }
    }     
  }
}
