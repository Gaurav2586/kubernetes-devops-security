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
            sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://34.67.12.150:9000 -Dsonar.login=56cb3c7fc636618a2c2fdfab6f3c6141c86db67d"
         }
      }
      stage('Docker Build and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'gcr:suki-dev', url: 'https://gcr.io/suki-dev/gauravsuki/') {
                      sh 'printenv'
                      sh 'docker build -t gcr.io/suki-dev/gauravsuki/numeric-app:""$GIT_COMMIT"" .'
                      sh 'docker push gcr.io/suki-dev/gauravsuki/numeric-app:""$GIT_COMMIT""'
            }
         }
       }
     }
   }
 }



