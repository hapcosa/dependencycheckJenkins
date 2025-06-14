pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-24' // Asegúrate de que este nombre coincida con tu instalación
    }
    
    stages {
        stage('Preparar entorno') {
            steps {
                sh 'pwd'
                sh 'ls -la'
            }
        }
        
        stage('Instalar dependencias') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Ejecutar tests') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                sh 'mkdir -p dependency-check-report'
                sh 'dependency-check --project "SafeNotes" --scan . --format HTML --out ./dependency-check-report'
            }
            post {
                always {
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'dependency-check-report',
                        reportFiles: 'dependency-check-report.html',
                        reportName: 'Reporte de Seguridad OWASP'
                    ])
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completado - Revisa el reporte de seguridad en dependency-check-report/'
        }
        success {
            echo 'Pipeline ejecutado exitosamente'
        }
        failure {
            echo 'Pipeline falló - Revisa los logs para más detalles'
        }
    }
}