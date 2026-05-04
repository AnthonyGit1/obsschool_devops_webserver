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
                        echo 'Ejecución de pruebas de SAST'
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
    }
}
