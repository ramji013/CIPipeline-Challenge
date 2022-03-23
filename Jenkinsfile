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
