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
        stage('Deploy') {
            steps {
                script {
                    sh 'docker rm -f my-python-app-deployst-container || true'

                    def deployImage = docker.build('my-python-app-deploy:latest', '-f ./Dockerfiles/Dockerfile .')
                    deployImage.inside {
                        sh 'echo "Deploying..."'
                    }
                    def deployContainer = deployImage.run('--name my-python-app-deploy-container')
                }
            }
        }
    }
}