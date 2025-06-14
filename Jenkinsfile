pipeline {
    agent any

    tools {
        nodejs 'NodeJS-24' // Nombre de tu instalación de NodeJS en Jenkins
    }

    stages {
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
                sh 'dependency-check --project "SafeNotes" --scan . --format "HTML" --out ./dependency-check-report'

            }
            post {
                always {
                    publishHTML target: [
            allowMissing: false,
            alwaysLinkToLastBuild: true,
            keepAll: true,
            reportDir: 'report',
            reportFiles: 'dependency-check-report.html',
            reportName: 'Reporte de Seguridad OWASP'
        ]
                }
            }
        }
    }

    post {
        always {
            // Limpieza o notificaciones adicionales
            echo 'Pipeline completado - Revisa el reporte de seguridad en dependency-check-report/'
        }
    }
}
