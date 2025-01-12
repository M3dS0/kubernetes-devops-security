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
      stage('Mutation Test - PIT') {
        steps {
          sh "mvn org.pitest:pitest-maven:mutationCoverage"
        }
        post {
          always {
            pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
          }
        }
      }
      stage('Docker Image Build and Push') {
        steps {
          withDockerRegistry([credentialsId: "docker-hub", url:""]) {
            sh 'printenv'
            sh 'docker build -t m3ds0/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push m3ds0/numeric-app:""$GIT_COMMIT""'
          }
        }
      } 
      stage('K8S Deployment - DEV') {
        steps{
          withKubeConfig([credentialsId: 'kubeconfig']) {
            sh "sed -i 's#replace#m3ds0/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml" //test
            sh "kubectl apply -f k8s_deployment_service.yaml"
          }
        }
      } 
    }
}
