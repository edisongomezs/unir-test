pipeline {
    agent {
        label 'docker'
    }
    stages {
        stage('Source') {
            steps {
                git 'https://github.com/edisongomezs/unir-test.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Building stage!'
                sh 'make build'
            }
        }
        stage('Unit tests') {
            steps {
                sh 'make test-unit'
                archiveArtifacts artifacts: 'results/*.xml'
            }
        }
        stage('API tests') {
            steps {
                sh 'mkdir -p results/api'
                sh 'make test-api'
                archiveArtifacts artifacts: 'results/api/*.xml', allowEmptyArchive: true
            }
        }
        stage('E2E tests') {
            steps {
                sh 'make test-e2e'
                archiveArtifacts artifacts: 'results/e2e/*.xml'
            }
        }
    }
    post {
        always {
            junit 'results/*.xml'
            cleanWs()
        }
        //failure {
            // Comentado temporalmente para evitar errores de conexión SMTP
            // mail to: 'edisonjaviergomezs@gmail.com',
            //      subject: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' failed",
            //      body: "Please go to ${env.BUILD_URL} and verify the build"
        //}
    }
}
