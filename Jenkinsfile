pipeline {
    environment {
        IMAGEN = "berenn/mundoseApache"
        USUARIO = 'USER_DOCKERHUB'
    }
    agent any
    stages {
        stage('Clone') {
            steps {
                git branch: "main", url: 'https://github.com/B3renn/jenkins_docker'
            }
        }
        stage('Build') {
            steps {
                script {
                    newApp = docker.build "$IMAGEN:$BUILD_NUMBER"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    docker.image("$IMAGEN:$BUILD_NUMBER").inside('-u root') {
                           sh 'apache2ctl -v'
                        }
                    }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    docker.withRegistry( '', USUARIO ) {
                        newApp.push()
                    }
                }
            }
        }
        stage('Docker Push') {
          agent any
          steps {
            withCredentials([usernamePassword(credentialsId: 'USER_DOCKERHUB', passwordVariable: 'USER_DOCKERHUBPassword', usernameVariable: 'USER_DOCKERHUB')]) {
              sh "docker login -u ${env.USER_DOCKERHUB} -p ${env.USER_DOCKERHUBPassword}"
              sh 'docker push $IMAGEN:$BUILD_NUMBER'
            }
          }
        }
        stage('Clean Up') {
            steps {
                sh "docker rmi $IMAGEN:$BUILD_NUMBER"
                }
        }
    }
}
