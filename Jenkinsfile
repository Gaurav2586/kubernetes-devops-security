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
        }
      stage('Mutation Tests - PIT') {
            steps {
               sh "mvn org.pitest:pitest-maven:mutationCoverage"
            }
      }
    stage('SonarQube - SAST') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://34.122.26.154:9000"
        }
        timeout(time: 2, unit: 'MINUTES') {
          script {
            waitForQualityGate abortPipeline: true
          }
        }
      }
    }
    stage('Vulnerability Scan - Docker') {
      steps {
        parallel(
          "Dependency Scan": {
            sh "mvn dependency-check:check"
          },
          "Trivy Scan": {
            sh "bash trivy-docker-image-scan.sh"
          }
        )
      }
    }
      stage('Docker Build and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'gcr:gcr-repo-cred', url: 'https://gcr.io/absolute-realm-333214/') {
                      sh 'printenv'
                      sh 'docker build -t gcr.io/absolute-realm-333214/numeric-app:""$GIT_COMMIT"" .'
                      sh 'docker push gcr.io/absolute-realm-333214/numeric-app:""$GIT_COMMIT""'
            }
        }
      }
    }     
  }
  post {
    always {
      junit 'target/surefire-reports/*.xml'
      jacoco execPattern: 'target/jacoco.exec'
      pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
      dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
    }

    // success {

    // }

    // failure {

    // }
  }
}
