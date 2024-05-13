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
                    sh 'docker tag nightnhawk/python-snake-by-chuyangliu:latest nightnhawk/python-snake-by-chuyangliu:1.0.1'
                    sh 'docker push nightnhawk/python-snake-by-chuyangliu:1.0.1'
                }
            }  
        }
        stage('Compile to Executable') {
            steps {
                script {
                    def publishImage = docker.build('my-python-app-publish:latest', '-f ./Dockerfiles/Dockerfile.publisher .')
                    publishImage.inside {
                        sh 'pip install pyinstaller'
                        sh 'pyinstaller --onefile run.py'
                    }
                }
            }
        }
        stage('Archive Executable') {
            steps {
                script {
                    if (fileExists('dist/*.exe')) {
                        archiveArtifacts artifacts: 'dist/*.exe', fingerprint: true
                    } else {
                        echo "No .exe files found in dist directory."
                    }
                }
            }
        }
        stage('Push to GitHub') {
            steps {
                script {
                    if (sh(script: 'git rev-parse --is-inside-work-tree', returnStdout: true).trim() == 'true' && sh(script: 'git rev-parse --is-inside-git-dir', returnStdout: true).trim() == 'false') {
                        if (sh(script: 'git show-ref --verify --quiet refs/heads/temp-branch', returnStatus: true) != 0) {
                            sh 'git checkout -b temp-branch'
                        } else {
                            sh 'git branch -D temp-branch'
                            sh 'git checkout -b temp-branch'
                        }
                        withCredentials([string(credentialsId: 'PAT', variable: 'GITHUB_PAT')]) {
                            sh 'git config --global url."https://${GITHUB_PAT}@github.com/".insteadOf "https://github.com/"'
                            sh 'git push origin temp-branch'
                            sh 'git push origin --delete temp-branch'
                        }
                    } else {
                        withCredentials([string(credentialsId: 'PAT', variable: 'GITHUB_PAT')]) {
                            sh 'git config --global url."https://${GITHUB_PAT}@github.com/".insteadOf "https://github.com/"'
                            sh 'git push'
                        }
                    }
                }
            }
        }
    }
}
