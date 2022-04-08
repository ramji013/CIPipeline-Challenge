pipeline{
   agent any 
    tools {
      maven 'm1'
     }
  
  stages {
      stage("Maven Build"){
          steps{
             script{
                last_started=env.STAGE_NAME
               }
              sh 'mvn -B -DskipTests clean package'
             
          }
        
      }
      stage('Maven Test'){
            steps{
               script{
                  last_started=env.STAGE_NAME
            }
                sh 'mvn test'
            }
            post{
            always{
                junit 'target/surefire-reports/*.xml'
            }
        }
        }
     stage("Build & SonarQube analysis") {
            agent any
            steps {
               script{
                    last_started=env.STAGE_NAME
            }
              withSonarQubeEnv('sonar-server-config') {
                sh 'java -version'
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
     stage("Quality gate") {
            steps {
               script{
                  last_started=env.STAGE_NAME
            }
                waitForQualityGate abortPipeline: true
            }
        }
     stage('Deploy to artifactory'){
        steps{
           script{
              last_started=env.STAGE_NAME
            }
        rtUpload(
         serverId : 'artifactory-pipeline',
         spec :'''{
           "files" :[
           {
           "pattern":"target/*.jar",
           "target":"maven-dev-v1"
           }
           ]
         }''')
        }
     }
     
     stage("deploy artifact to s3"){
       steps{
         withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', 
                           accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'deploy-s3', 
                           secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        
            s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true,
                     file:'/Users/ramji013/.jenkins/workspace/ci-challenge/target/simplecalculator-0.0.2.jar', bucket:'bucket123417', 
                     path:'/Users/ramji013/.jenkins/workspace/ci-challenge/target/simplecalculator-0.0.2.jar')
       
      }
        }
        }
     
    }
    post {  
         always {  
             echo 'This will always run'  
         }  
         success {   
            echo "========Deploying executed successfully========"
            emailext attachLog: true, body: "<b>Example</b><br>Project: ${env.JOB_NAME}", from: 'devopspractice17@gmail.com',compressLog: true, mimeType: 'text/html', replyTo: '', subject: "Deploy Successfull Project ${env.JOB_NAME}", to: "devopspractice17@gmail.com";
         }  
         failure {  
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> Stage Name: $last_started <br> URL de build: ${env.BUILD_URL}", cc: 'ramji013@gmail.com', charset: 'UTF-8', from: 'devopspractice17@gmail.com', mimeType: 'text/html', replyTo: '', subject: "Deployment failed for Project -> ${env.JOB_NAME}", to: "devopspractice17@gmail.com";  
         }  
         unstable {  
             echo 'This will run only if the run was marked as unstable'  
         }  
         changed {  
             echo 'This will run only if the state of the Pipeline has changed'   
             echo 'For example, if the Pipeline was previously failing but is now successful'  
         }  
     }
  }
