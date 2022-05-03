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

      stage('Docker Build and Push') {
            steps {
                sh 'docker login -u oauth2accesstoken -p "$(gcloud auth print-access-token)" https://gcr.io'
                sh 'printenv'
                sh 'docker build -t gcr.io/suki-dev/gauravsuki/numeric-app:""$GIT_COMMIT"" .'
                sh 'docker push gcr.io/suki-dev/gauravsuki/numeric-app:""$GIT_COMMIT""'
            }
        }
      }
    }


