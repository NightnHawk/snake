pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    def appImage = docker.build('my-python-app:latest', '-f ./Dockerfiles/Dockerfile .')
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'docker rm -f my-python-app-test-container || true'
                    
                    def testImage = docker.build('my-python-app-test:latest', '-f ./Dockerfiles/Dockerfile .')
                    testImage.inside {
                        sh 'python -m pytest'
                    }
                    def testContainer = testImage.run('--name my-python-app-test-container')
                }
            }
        }
        stage('Deploy with Docker') {
            steps {
                script {
                    sh 'docker build -t my-python-app .'
                    withDockerRegistry(credentialsId: 'my-docker-registry-credentials', toolName: 'docker') {
                        sh 'docker push my-python-app:latest'
                    }
                    sh 'ssh ubuntu@ec2-xx-xx-xx-xx.compute-1.amazonaws.com "docker pull my-python-app:latest && docker run -d -p 80:80 my-python-app:latest"'
                }
            }  
        }
    }
}