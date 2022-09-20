pipeline {
  agent any

  stages {
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
              docker.withRegistry([crdentialId: "dockerhub", url: ""])
              sh 'docker build -t dardakunal/numapp:""$GIT_COMMIT"" .'
              sh 'docker push dardakunal/numapp:""$GIT_COMMIT""'
            }
        }
    }
}
