pipeline {
   agent any
   environment {
      dockerhub=credentials('dockerhub')
   }
   stages{
       stage("clean"){
           
         steps
            {
                sh 'mvn clean'
            }
       }
       stage("test"){
           
         steps
            {


                sh 'mvn test'
            }

   }
   stage("packaging"){
         when{
                branch "Production"
                }  
         steps
            {
                sh 'mvn package -DskipTests'
            }
   }
   stage('build image')
        {
         when{
                branch "Production"
                }
            steps{
                // sh 'echo $dockerhub_USR | xargs echo'
                sh 'docker build -t capstone:${GIT_COMMIT} .'
            }
        } 

        stage('pushing to dockerhub')
        {
          when{
                branch "Production"
                }
            steps{
                // sh 'docker tag capstone:1.01 muzakkirsaifi/final_assignment:1.01 '
                // sh 'docker login -u $dockerhub_USR -p $dockerhub_PSW'
                // sh 'docker push muzakkirsaifi/final_assignment:1.01'
                               
                sh 'docker tag capstone:${GIT_COMMIT} muzakkirsaifi/springboot:${GIT_COMMIT}'
                sh 'echo $dockerhub_PSW | docker login -u $dockerhub_USR --password-stdin'
                sh 'docker push muzakkirsaifi/springboot:${GIT_COMMIT}'
                            


            }
        }
        stage('deploy')
        {
            when{
                branch "Production"
                }
            steps{
                script{
                 kubernetesDeploy configs: '**/deployment.yml', kubeConfig: [path: ''], kubeconfigId: 'kubeconfig', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']  
                }
            }
        }
}
}

