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
                    // Build the Docker image
                    def appImage = docker.build('nightnhawk/python-snake-by-chuyangliu:latest', '-f ./Dockerfiles/Dockerfile .')
                    
                    // Correctly tag the image
                    sh 'docker tag nightnhawk/python-snake-by-chuyangliu:latest nightnhawk/python-snake-by-chuyangliu:1.0.0'
                    
                    // Push the image to Docker Hub
                    withDockerRegistry([credentialsId: 'Docker-Hub-Credentials', url: 'https://registry.hub.docker.com']) {
                        sh 'docker push nightnhawk/python-snake-by-chuyangliu:1.0.0'
                    }
                }
            }  
        }
    }
}