
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

        stage('Dosyalari Linux Sunucuya Aktar') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat 'scp -o BatchMode=yes -o StrictHostKeyChecking=yes Dockerfile index.html jenkins@10.34.41.3:/home/jenkins/jenkins-docker-test/'
                }
            }
        }

        stage('Docker Image Build') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat 'ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 docker build -t jenkins-docker-test:1.0 /home/jenkins/jenkins-docker-test'
                }
            }
        }

        stage('Docker Container Baslat') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat 'ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 docker run -d --name jenkins-docker-test-web -p 8086:80 jenkins-docker-test:1.0'
                }
            }
        }
    }
}
