pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' 
            }
        }   
      stage('Unit test') {
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
      stages('Docker Image Build and Push')
        steps {
          sh 'printenv'
          sh 'docker built -t m3ds0/numeric-app:""GIT_COMMIT"" .'
          sh 'docker push m3ds0/numeric-app:""GIT_COMMIT""'
        }
    }
}