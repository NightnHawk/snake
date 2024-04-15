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
                    def appImage = docker.build('nightnhawk/python-snake-by-chuyangliu:latest', '-f ./Dockerfiles/Dockerfile .')
                    sh 'docker tag nightnhawk/python-snake-by-chuyangliu:latest nightnhawk/python-snake-by-chuyangliu:1.0.0'
                    sh 'docker push nightnhawk/python-snake-by-chuyangliu:1.0.0'
                }
            }  
        }
        stage('Compile to Executable') {
            steps {
                script {
                    def publishImage = docker.build('my-python-app-publish:latest', '-f ./Dockerfiles/Dockerfile.publisher .')
                    publishImage.inside {
                        sh 'sudo pip install pyinstaller'
                        sh 'pyinstaller --onefile run.py'
                    }
                }
            }
        }
        stage('Archive Executable') {
            steps {
                archiveArtifacts artifacts: 'dist/*.exe', fingerprint: true
            }
        }
        stage('Push to GitHub') {
            steps {
                sh '''
                    git config --global user.email "grzegorz_lazinski@o2.pl"
                    git config --global user.name "NightnHawk"
                    git add dist/*.exe
                    git commit -m "Add executable for version 1.0.0"
                    git push
                '''
            }
        }
    }
}