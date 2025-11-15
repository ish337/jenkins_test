#!groovy

pipeline {
    agent any

    stages {
        stage("Create Site image") {
            steps {
                echo '🧱 Building Site image ...'
                sh "docker build --no-cache -t ish337/test_site ."
            }
        }

        stage("Docker login") {
            steps {
                echo "🔐 Logging in to DockerHub ..."
                withCredentials([usernamePassword(credentialsId: 'DockerHub-Credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        docker login -u $USERNAME -p $PASSWORD
                    '''
                }
            }
        }

        stage("Push Site to DockerHub") {
            steps {
                echo "🚀 Pushing Site image to DockerHub ..."
                sh '''
                    docker push ish337/test_site
                '''
            }
        }

        stage("Deploy Site on remote server") {
            agent { label 'agent1' }
            steps {
                echo "🌐 Deploying Site container on remote server ..."
                sh '''
                    echo "🧹 Cleaning up old site container and image..."
                    docker stop test_site || true
                    docker rm test_site || true
                    docker rmi ish337/test_site || true

                    echo "⬇️ Pulling latest site image..."
                    docker pull ish337/test_site

                    echo "🚀 Starting new test_site container..."
                    docker run -d --name test_site --restart=always -p 0.0.0.0:80  ish337/test_site
                '''
            } 
        }
    }
}