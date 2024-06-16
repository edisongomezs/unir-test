pipeline {
    // Definición del agente que se usará para ejecutar el pipeline
    agent {
        label 'docker'
    }
    stages {
        // Etapa de obtención del código fuente desde el repositorio
        stage('Source') {
            steps {
                // Uso de un bloque script para definir las variables de SCM
                script {
                    def scmVars = checkout scm: [
                        $class: 'GitSCM',
                        branches: [[name: '*/master']],
                        userRemoteConfigs: [[
                            url: 'https://github.com/edisongomezs/unir-test.git',
                            credentialsId: 'your-credentials-id' // ID de las credenciales configuradas en Jenkins
                        ]]
                    ]
                }
            }
        }
        // Etapa de construcción del proyecto
        stage('Build') {
            steps {
                echo 'Building stage!'
                // Ejecuta el comando de construcción definido en el Makefile
                sh 'make build'
            }
        }
        // Etapa de pruebas unitarias
        stage('Unit tests') {
            steps {
                // Ejecuta las pruebas unitarias definidas en el Makefile
                sh 'make test-unit'
                // Archiva los archivos de resultados de las pruebas unitarias
                archiveArtifacts artifacts: 'results/unit_result.xml', allowEmptyArchive: true
            }
        }
        // Nueva etapa para pruebas de API
        stage('API tests') {
            steps {
                // Crear directorio para los resultados de las pruebas de API con permisos adecuados
                sh 'sudo mkdir -p results/api'
                sh 'sudo chown -R $USER:$USER results'
                // Ejecuta las pruebas de API definidas en el Makefile
                sh 'make test-api'
                // Archiva los archivos de resultados de las pruebas de API
                archiveArtifacts artifacts: 'results/api_result.xml', allowEmptyArchive: true
            }
        }
        // Nueva etapa para pruebas de extremo a extremo (E2E)
        stage('E2E tests') {
            steps {
                // Crear directorio para los resultados de las pruebas E2E con permisos adecuados
                sh 'sudo mkdir -p results/e2e'
                sh 'sudo chown -R $USER:$USER results'
                // Ejecuta las pruebas E2E definidas en el Makefile
                sh 'make test-e2e'
                // Archiva los archivos de resultados de las pruebas E2E
                archiveArtifacts artifacts: 'results/cypress_result.xml', allowEmptyArchive: true
            }
        }
    }
    post {
        // Etapas a ejecutar siempre, independientemente del resultado
        always {
            // Publica los resultados de las pruebas en formato JUnit
            junit '**/results/*.xml'
            // Limpia el espacio de trabajo después de la ejecución
            cleanWs()
        }
        // Etapa a ejecutar solo en caso de fallo del pipeline
        failure {
            echo "Pipeline failed. Please check the logs for details."
            // Comentado temporalmente para evitar errores de conexión SMTP
            // mail to: 'edisonjaviergomezs@gmail.com',
            //      subject: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' failed",
            //      body: "Please go to ${env.BUILD_URL} and verify the build"
        }
    }
}
