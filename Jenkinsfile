pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    def appImage = docker.build('my-python-app:latest')
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    def testImage = docker.build('my-python-app-test:latest')
                    def testContainer = testImage.run('--name my-python-app-test-container')
                    testContainer.inside {
                        sh 'python -m unittest discover'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def deployImage = docker.build('my-python-app-deploy:latest')
                    def deployContainer = deployImage.run('--name my python-app-deploy-container')
                    deployContainer.inside {
                        sh 'echo "Deploying...'
                    }
                }
            }
        }
    }
}