pipeline {
  agent any

  stages {
      stage('Checkout') {
            steps{
                git branch: 'main', url: 'https://github.com/dardakunal/devsecops.git'
            }
          
      }
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
       stage('Unit Test') {
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
            withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
                sh 'printenv'
              sh 'sudo docker build -t dardakunal/numeric-app:""$BUILD_ID"" .'
              sh 'docker push dardakunal/numeric-app:""$BUILD_ID""'
            }
            }
        }
        
        stage('K8S deployment-DEV') {
             steps {
               withKubeConfig([credentialsId: 'kubeconfig'])  {
                   sh "sed -i 's#replace#dardakunal/numeric-app:${BUILD_ID}#g' k8s_deployment_service.yaml"
                   sh "kubectl apply -f k8s_deployment_service.yaml"
          
            }
            }
        }
    
}
}
