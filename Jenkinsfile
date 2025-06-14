pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-24'
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
                sh '''
                    echo "Ejecutando tests..."
                    npm test
                '''
            }
        }
        
        stage('Security Scan') {
            steps {
                sh '''
                    echo "Ejecutando análisis de seguridad..."
                    dependency-check --project "SafeNotes" --scan . --format "HTML"
                '''
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'dependency-check-report.html', allowEmptyArchive: true
            cleanWs()
        }
        success {
            echo 'Pipeline ejecutado exitosamente!'
        }
        failure {
            echo 'Pipeline falló. Revisa los logs.'
        }
    }
}