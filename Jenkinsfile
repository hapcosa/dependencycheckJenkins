pipeline {
    agent any
    
    tools {
        nodejs 'Nodejsdevopstest'
    }
    
    environment {
        NODE_ENV = 'test'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "Instalando dependencias..."
                    npm install
                '''
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    // Verificar si existe script de test, si no, omitir
                    def packageJson = readFile('package.json')
                    if (packageJson.contains('"test"')) {
                        sh '''
                            echo "Ejecutando tests..."
                            npm test
                        '''
                    } else {
                        echo "No hay script de test configurado en package.json - saltando tests"
                    }
                }
            }
        }
        
        stage('Security Scan') {
            steps {
                sh '''
                    echo "Configurando directorio para dependency-check..."
                    # Intentar crear directorio de datos local
                    mkdir -p ./dependency-check-data
                    
                    echo "Ejecutando análisis de seguridad..."
                    dependency-check --project "SafeNotes" \\
                                   --scan . \\
                                   --format "HTML" \\
                                   --data ./dependency-check-data \\
                                   --out . \\
                                   --enableRetired \\
                                   --enableExperimental
                '''
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'dependency-check-report.html', allowEmptyArchive: true
            // Removido cleanWs() porque el plugin no está disponible
            deleteDir() // Alternativa para limpiar el workspace
        }
        success {
            echo 'Pipeline ejecutado exitosamente!'
        }
        failure {
            echo 'Pipeline falló. Revisa los logs.'
        }
    }
}