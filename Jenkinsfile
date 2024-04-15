pipeline {
    agent { 
    docker {
        image 'registry.hub.docker.com/NightnHawk/python-snake-by-chuyangliu'
        registryUrl 'https://registry.hub.docker.com'
        registryCredentialsId '1234'
    }
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
                    def appImage = docker.build('your-dockerhub-username/python-snake-by-chuyangliu:latest', '-f ./Dockerfiles/Dockerfile .')

                    appImage.tag('NightnHawk/python-snake-by-chuyangliu:1.0.0')

                    withDockerRegistry([credentialsId: '1234', url: 'https://registry.hub.docker.com']) {
                        appImage.push('1.0.0')
                    }
                }
            }  
        }
    }
}