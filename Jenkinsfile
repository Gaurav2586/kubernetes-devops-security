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
        IMAGE_TAG = "${GIT_BRANCH}.${GIT_COMMIT_SHORT}"
        GIT= credentials('ci-cd-demo')
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
          withCredentials([usernamePassword(credentialsId: 'ci-cd-demo', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
            sh "git clone https://github.com/Gaurav2586/kubernetes-devops-security.git"
            sh "git config --global user.email megaurav25@gmail.com"
            sh "git config --global user.name Gaurav2586"
            sh "ls -lrt"
            sh "pwd"
            dir('kubernetes-devops-security'){

               sh "sed -e 's+gcr.io/suki-dev/gauravsuki/numeric-app.*+gcr.io/suki-dev/gauravsuki/numeric-app:$IMAGE_TAG+g' -i k8s_deployment_service.yaml"
               sh "cat k8s_deployment_service.yaml"
               sh "git add ."
               sh "git commit -m 'Done by JenkinsJob ChangeManifest: $BUILD_NUMBER'" 
               sh "git push https:/${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetes-devops-security.git HEAD:main"
            }
          }
         }
        }
      stage("sleep") {
        steps {
              echo "Started Sleep"
              sleep(time: 600, unit: "SECONDS")
            }
            
          }
          
        }

      
      }
    




