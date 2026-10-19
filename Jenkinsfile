
pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

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

        stage('Yeni Image Test') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat '''
ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 "docker container inspect jenkins-docker-test-verify > /dev/null 2>&1 && exit 1 || exit 0"
ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 "docker run -d --name jenkins-docker-test-verify jenkins-docker-test:1.0"
ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 "docker exec jenkins-docker-test-verify wget -q -O /dev/null http://127.0.0.1/"
                    '''
                }
            }
        }

        stage('Docker Container Guncelle') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat '''
ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 "docker rm -f jenkins-docker-test-verify"
ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 "docker container inspect jenkins-docker-test-web --format '{{.Config.Image}}'"
ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 "docker rm -f jenkins-docker-test-web"
ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 "docker run -d --name jenkins-docker-test-web -p 8086:80 jenkins-docker-test:1.0"
                    '''
                }
            }
        }

        stage('Web Uygulamasi Kontrol') {
            steps {
                sshagent(credentials: ['docker3-ssh']) {
                    bat 'ssh -o BatchMode=yes -o StrictHostKeyChecking=yes jenkins@10.34.41.3 curl -f http://127.0.0.1:8086/'
                }
            }
        }
    }

    post {
        always {
            echo 'Jenkins Pipeline tamamlandi.'
        }
        success {
            echo 'Docker uygulamasi basariyla guncellendi.'
        }
        failure {
            echo 'Pipeline basarisiz. Console Output kontrol edilmeli.'
        }
    }
}
