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

        stage('Build') {
            steps {
                sh 'docker build -t devops_ws .'
            }
        }
    }
}
