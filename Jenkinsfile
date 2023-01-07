pipeline{
    agent any
    tools {
      maven 'maven_3_5_0'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/javahometech/dockeransiblejenkins'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t akshayraina/dockeransiblejenkins:${DOCKER_TAG} "
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]){
                    sh 'docker login -u akshayraina -p ${dockerhubpwd}'
                }
                
                sh "docker push akshayraina/dockeransiblejenkins:${DOCKER_TAG} "
            }
        }
        
//         stage('Docker Deploy'){
//             steps{
//               ansiblePlaybook credentialsId: 'ansible', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible-test', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
//             }
//         }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
