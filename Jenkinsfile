properties([pipelineTriggers([githubPush()])])

pipeline {
    environment {
        // name of the image without tag
        dockerRepo = "siddu-143/password-manager-repo"
        dockerCredentials = '5e8a7261-1508-4c88-82bd-8faff92d90d2'
        dockerImageVersioned = ""
        dockerImageLatest = ""
    }

    agent any

    stages {
        /* checkout repo */
        stage('Checkout SCM') {
            steps {
                checkout([
                 $class: 'GitSCM',
                 branches: [[name: 'master']],
                 userRemoteConfigs: [[
                    url: 'https://www.github.com/siddu-143/password-manager-repo.git',
                    credentialsId: 'c9f65483-a2c6-40e6-a515-0dcfa154bd9a',
                 ]]
                ])
            }
        }
        stage("Building docker image"){
            steps{
                script{
                    dockerImageVersioned = docker.build dockerRepo + ":$BUILD_NUMBER"
                    dockerImageLatest = docker.build dockerRepo + ":latest"
                }
            }
        }
        stage("Pushing image to registry"){
            steps{
                script{
                    // if you want to use custom registry, use the first argument, which is blank in this case
                    docker.withRegistry( '', dockerCredentials){
                        dockerImageVersioned.push()
                        dockerImageLatest.push()
                    }
                }
            }
        }
        stage('Cleaning up') {
            steps {
                sh "docker rmi $dockerRepo:$BUILD_NUMBER"
            }
        }
    }

    /* Cleanup workspace */
    post {
       always {
           deleteDir()
       }
   }
}
