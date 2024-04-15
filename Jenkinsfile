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
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Docker-Hub-Credentials', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                }
            }
        }
        stage('Deploy with Docker') {
            steps {
                script {
                    // Build the Docker image
                    def appImage = docker.build('nightnhawk/python-snake-by-chuyangliu:latest', '-f ./Dockerfiles/Dockerfile .')
                    
                    sh 'docker tag nightnhawk/python-snake-by-chuyangliu:latest nightnhawk/python-snake-by-chuyangliu:1.0.0'
                    
                    // Push the image to Docker Hub
                    sh 'docker push nightnhawk/python-snake-by-chuyangliu:1.0.0'
                }
            }  
        }
    }
}