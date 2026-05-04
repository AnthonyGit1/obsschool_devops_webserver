pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Análisis') {
            parallel {
                stage('Pruebas de SAST') {
                    steps {
                        withSonarQubeEnv('SonarQube') {
                            sh "${tool('SonarQubeScanner')}/bin/sonar-scanner"
                        }
                        timeout(time: 5, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: false
                        }
                    }
                }
                stage('Imprimir Env') {
                    steps {
                        echo "Workspace: ${env.WORKSPACE}"
                    }
                }
            }
        }

        stage('Configurar archivo') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Credentials_Threepoints',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh '''
                        echo "[credentials]" > credentials.ini
                        echo "user=$USER" >> credentials.ini
                        echo "password=$PASSWORD" >> credentials.ini
                        cat credentials.ini
                    '''
                }
                archiveArtifacts artifacts: 'credentials.ini', fingerprint: true
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t devops_ws .'
            }
        }

        stage('Despliegue del servidor') {
            steps {
                sh 'docker stop devops_ws || true'
                sh 'docker stop devops || true'
                sh 'docker rm devops || true'
                sh 'docker run -d -p 8090:8090 --name devops devops_ws'
            }
        }
    }
}
