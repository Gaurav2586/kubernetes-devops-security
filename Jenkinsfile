pipeline {
  agent {
      label 'jenkins-slave'
	}
  environment {
        GIT_COMMIT_SHORT = sh(
                script: "printf \$(git rev-parse --short ${GIT_COMMIT})",
                returnStdout: true
        )
        GIT_BRANCH = "${GIT_BRANCH.split("/")[1]}"
    }

  stages {
      stage('Build Artifact') {
            steps {
              sh 'printenv'
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        } 
      stage('Docker Build and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'gcr:suki-dev', url: 'https://gcr.io/suki-dev/gauravsuki/') {
                      sh 'printenv'
                      sh 'docker build -t gcr.io/suki-dev/gauravsuki/numeric-app:""$GIT_BRANCH.$GIT_COMMIT_SHORT"" .'
                      sh 'docker push gcr.io/suki-dev/gauravsuki/numeric-app:""$GIT_BRANCH.$GIT_COMMIT_SHORT""' 
            }
         }
       }
     }
      stage('UPDATE GIT'){
       steps {
        container('tools') {
          sh "git clone https://github.com/Gaurav2586/kubernetes-devops-security.git"
          sh "ls -lrt"
          }
        }
      }
   }
}






