pipeline {
    agent {
        docker {
            image 'python:3.9' // Use a stable Python version
            args '--user root' // Ensures required permissions
        }
    }

    environment {
        VENV_DIR = "venv"
    }

    stages {
        stage('Setup') {
            steps {
                script {
                    sh """
                        apt-get update && apt-get install -y nodejs npm
                        python -m venv ${VENV_DIR}
                        . ${VENV_DIR}/bin/activate
                        pip install --upgrade pip
                        pip install robotframework-browser
                        rfbrowser init
                        pip install -U robotframework-pabot
                    """
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh """
                        . ${VENV_DIR}/bin/activate
                        pabot --processes 2 ./SGBAuto
                    """
                }
            }
        }

        stage('Publish Results') {
            steps {
                robot outputPath: 'results'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'log.html, output.html, report.html, pabot_results/', fingerprint: true
        }
    }
}
