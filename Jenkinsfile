pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        DOCKER_IMAGE = 'ayoubater23/expense-tracker'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Ayoubater23/expense_tracker.git'
            }
        }

        stage('Install Node.js') {
            steps {
                sh 'node -v'
                sh 'npm -v'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_ID}").push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '/usr/local/bin/docker-compose -f docker-compose.prod.yml up -d'

            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/dist/**'
            junit 'test-results/**/*.xml'
        }

        success {
            mail to: 'atir.ayoub@aiac.ma',
                 subject: "Build Succès: ${currentBuild.fullDisplayName}",
                 body: "Bonne nouvelle!\n\nLe build ${env.BUILD_TAG} a été un succès!"
        }

        failure {
            mail to: 'atir.ayoub@aiac.ma',
                 subject: "Build Échoué: ${currentBuild.fullDisplayName}",
                 body: "Mauvaise nouvelle!\n\nLe build ${env.BUILD_TAG} a échoué. Veuillez vérifier les logs pour plus de détails."
        }
    }
}
