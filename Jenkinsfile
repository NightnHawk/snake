pipeline {
    agent any
}
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
                    def appImage = docker.build('nightnhawk/python-snake-by-chuyangliu:latest', '-f ./Dockerfiles/Dockerfile .')
                    
                    appImage.tag('nightnhawk/python-snake-by-chuyangliu:1.0.0')
                    
                    withDockerRegistry([credentialsId: 'Docker-Hub-Credentials', url: 'https://registry.hub.docker.com']) {
                        appImage.push('1.0.0')
                }
            }  
        }
    }
}