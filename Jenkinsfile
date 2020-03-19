pipeline {
//    {dockerfile true} 
  agent any
  tools {
    maven 'maven3';
}
 
  stages {
    stage('Unit Test') {
       steps {
           sh label: 'Test running', script: '''mvn test'''
           echo 'Hello Testing done'
       }
       }
    stage('Jacoco Coverage Report') {
       steps{
        jacoco()
        }
    }   
      stage('SonarQube'){
        steps{
            sh label: '', script: '''mvn sonar:sonar \
          -Dsonar.host.url=http://sonar:9000 \
          -Dsonar.login=f18bd60881e930515051e739c9850bc14c324476'''
        }
    }
    stage('Maven Build'){
        steps{
                sh label:'Maven Build of war file', script:'''
                    mvn clean install -DskipTests=false
                    mvn package
                '''
        }
    }
  stage('Docker Image Build') {
      steps{
         sh "docker build -t zuul_server:latest ."
      }
    }
    stage('Docker save'){
        steps{
            sh "docker save zuul_server:latest>zuul_server.tar"
        }
    }
 
    stage('Upload to S3'){
        steps{
            withAWS(region:'us-east-1',credentials:'aws-cred')
            {
                s3Upload(bucket:'bucketforsprint',file:'zuul_server.tar',workingDir:'./');
            }
        }
    }
    stage('Tar remove'){
        steps{
            sh "rm zuul_server.tar"
        }
    }
  }
 
}
