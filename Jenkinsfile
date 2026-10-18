pipeline {
    agent any

    stages {
        stage('Ilk Test') {
            steps {
                echo 'Merhaba Jenkins!'
                echo 'GitHub baglantisi basarili!'
            }
        }

        stage('Linux SSH Test') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat 'ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 whoami'
                }
            }
        }

        stage('Docker Container Listesi') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat 'ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 docker ps'
                }
            }
        }

        stage('Docker Image Build') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat 'ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 docker version'
                }
            }
        }
    }
}
