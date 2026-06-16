@Library("Shared") _
pipeline {
    agent { label "worker1" }
    
    triggers {
        githubPush()
    }
    stages {
        stage("Hello"){
            steps{
                script{
                    hello()
                }
            }
        }
        stage("code") {
            steps {
                script{
                clone("https://github.com/21satyam/django-notes-app.git","main")
                }
            }
        }

        stage("build") {
            steps {
                echo "Build Stage"
                sh "docker build -t notes-app:latest ."
            }
        }

        stage("test") {
            steps {
                echo "Test Stage"
            }
        }
        stage('Push to DockerHub') {
            steps {
                echo 'Pushing the image to Docker Hub'
        
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerHubCred',
                        usernameVariable: 'dockerHubUser',
                        passwordVariable: 'dockerHubPass'
                    )
                ]) {
                    sh '''
                        echo "$dockerHubPass" | docker login -u "$dockerHubUser" --password-stdin
                        docker tag notes-app:latest "$dockerHubUser/notes-app:latest"
                        docker push "$dockerHubUser/notes-app:latest"
                    '''
                }
            }
        }

        stage("deploy") {
            steps {
                sh '''
                echo "Deploy Stage"
                docker compose down --remove-orphans || true
                docker compose up -d
                '''
            }
        }
    }
}
