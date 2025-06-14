pipeline {
    agent any

    tools {
        nodejs 'NodeJS-24' // Nombre de tu instalación de NodeJS en Jenkins
    }

    stages {
        // Etapa 1: Verificar workspace (aquí sí van los steps)
        stage('Preparar entorno') {
            steps {
                sh 'pwd'  // Imprime la ruta actual (debería ser el workspace)
                sh 'ls -la'  // Lista archivos antes de ejecutar comandos
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
                // Asegúrate de que el directorio de salida existe
                sh 'mkdir -p dependency-check-report'
                // Ejecuta el escaneo
                sh 'dependency-check --project "SafeNotes" --scan . --format HTML --out ./dependency-check-report'
            }
            post {
                always {
                    // Publica el reporte HTML (¡usa la ruta correcta!)
                    publishHTML target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'dependency-check-report',  // Corregido: misma ruta que --out
                        reportFiles: 'dependency-check-report.html',
                        reportName: 'Reporte de Seguridad OWASP'
                    ]
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completado - Revisa el reporte de seguridad en dependency-check-report/'
        }
    }
}