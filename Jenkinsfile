pipeline {
    agent {
        label 'docker'
    }
    stages {
        stage('Source') {
            steps {
                git 'https://github.com/edisongomez/unir-test.git'
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
                archiveArtifacts artifacts: 'results/unit_result.xml', allowEmptyArchive: true
            }
        }
        stage('API tests') {
            steps {
                sh 'sudo mkdir -p results/api'
                sh 'sudo chown -R $USER:$USER results'
                sh 'make test-api'
                archiveArtifacts artifacts: 'results/api_result.xml', allowEmptyArchive: true
            }
        }
        stage('E2E tests') {
            steps {
                sh 'sudo mkdir -p results/e2e'
                sh 'sudo chown -R $USER:$USER results'
                sh 'make test-e2e'
                archiveArtifacts artifacts: 'results/cypress_result.xml', allowEmptyArchive: true
            }
        }
    }
    post {
        always {
            junit '**/results/*.xml'
            cleanWs()
        }
        failure {
            echo "Pipeline failed. Please check the logs for details."
            // mail to: 'edisonjaviergomezs@gmail.com',
            //      subject: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' failed",
            //      body: "Please go to ${env.BUILD_URL} and verify the build"
        }
    }
}
