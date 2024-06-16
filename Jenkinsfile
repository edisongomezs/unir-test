pipeline {
    agent {
        // Utiliza un nodo etiquetado con 'docker' para ejecutar el pipeline
        label 'docker'
    }
    stages {
        stage('Source') {
            steps {
                // Clona el repositorio especificado
                git 'https://github.com/srayuso/unir-cicd.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Building stage!'
                // Ejecuta el comando de construcción definido en el Makefile
                sh 'make build'
            }
        }
        stage('Unit tests') {
            steps {
                // Ejecuta las pruebas unitarias definidas en el Makefile
                sh 'make test-unit'
                // Archiva los archivos de resultados de las pruebas unitarias
                archiveArtifacts artifacts: 'results/unit_result.xml', allowEmptyArchive: true
            }
        }
        stage('API tests') {
            steps {
                // Crea el directorio para los resultados de las pruebas de API
                sh 'sudo mkdir -p results/api'
                // Cambia el propietario del directorio para evitar problemas de permisos
                sh 'sudo chown -R $USER:$USER results'
                // Ejecuta las pruebas de API definidas en el Makefile
                sh 'make test-api'
                // Archiva los archivos de resultados de las pruebas de API
                archiveArtifacts artifacts: 'results/api_result.xml', allowEmptyArchive: true
            }
        }
        stage('E2E tests') {
            steps {
                // Crea el directorio para los resultados de las pruebas E2E
                sh 'sudo mkdir -p results/e2e'
                // Cambia el propietario del directorio para evitar problemas de permisos
                sh 'sudo chown -R $USER:$USER results'
                // Ejecuta las pruebas E2E definidas en el Makefile
                sh 'make test-e2e'
                // Archiva los archivos de resultados de las pruebas E2E
                archiveArtifacts artifacts: 'results/e2e_result.xml', allowEmptyArchive: true
            }
        }
    }
    post {
        always {
            // Publica los resultados de las pruebas en formato JUnit
            junit 'results/*_result.xml'
            // Limpia el espacio de trabajo después de la ejecución
            cleanWs()
        }
        failure {
            echo "Pipeline failed. Job: ${env.JOB_NAME}, Build: ${env.BUILD_NUMBER}"
            // Envía un correo en caso de fallo, incluyendo el nombre del trabajo y el número de ejecución
            // mail to: 'edisonjaviergomezs@gmail.com',
            //      subject: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' failed",
            //      body: "Please go to ${env.BUILD_URL} and verify the build"
        }
    }
}
