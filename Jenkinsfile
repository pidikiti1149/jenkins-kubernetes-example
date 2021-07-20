pipeline{
    agent any
    
    stages {
        stage('Build Maven') {
            steps{
              checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/pidikiti1149/jenkins-kubernetes-example.git']]])
                
            }
        }
        

         stage('Build Docker Image') {
            steps {
                script {
                  sh 'docker build -t pidikiti1149/nodejsapp-1.0 .'
                }
            }
        }
        
           stage('Deploy Docker Image') {
            steps {
                script {
                 withCredentials([string(credentialsId: 'pidikiti1149', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u pidikiti1149 -p ${dockerhubpwd}'
                 }  
                 sh 'docker push pidikiti1149/nodejsapp-1.0:latest'
                }
            }
        }
        stage('Deploy on k8s') {
            steps {
                sshagent(['k8s']) {
                    sh "scp -o StrictHostKeyChecking=no nodejsapp.yaml azureuser@<PublicIP>:/home/azureuser"
                    script{
                        try{
                            sh "ssh azureuser@<PublicIP> kubectl apply -f ."
                        }catch(error){
                            sh "ssh azureuser@<PublicIP> kubectl apply -f ."
                        }
                    }
                }
            }
            
        }
        
    }
}
