pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-24'
    }
    
    stages {
        stage('Verificar Entorno') {
            steps {
                echo "=== VERIFICANDO ENTORNO ==="
                sh 'pwd'
                sh 'ls -la'
                sh 'whoami'
                sh 'node --version || echo "Node no encontrado"'
                sh 'npm --version || echo "NPM no encontrado"'
                sh 'dependency-check --version || echo "OWASP Dependency Check no encontrado"'
            }
        }
        
        stage('Verificar package.json') {
            steps {
                echo "=== VERIFICANDO PACKAGE.JSON ==="
                sh 'cat package.json || echo "package.json no encontrado"'
            }
        }
        
        stage('Instalar dependencias') {
            steps {
                echo "=== INSTALANDO DEPENDENCIAS ==="
                sh 'npm install'
                sh 'ls -la node_modules/ || echo "node_modules no creado"'
            }
        }
        
        stage('Ejecutar tests') {
            steps {
                echo "=== EJECUTANDO TESTS ==="
                sh 'npm test'
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                echo "=== INICIANDO OWASP DEPENDENCY CHECK ==="
                sh 'mkdir -p dependency-check-report'
                sh 'ls -la dependency-check-report/'
                
                echo "=== EJECUTANDO ESCANEO ==="
                sh '''
                    dependency-check \
                        --project "SafeNotes" \
                        --scan . \
                        --format HTML \
                        --out ./dependency-check-report/ \
                        --enableExperimental
                '''
                
                echo "=== VERIFICANDO ARCHIVOS GENERADOS ==="
                sh 'ls -la dependency-check-report/'
                sh 'find . -name "*.html" -type f'
            }
            post {
                always {
                    script {
                        // Verifica si el archivo existe antes de publicar
                        if (fileExists('dependency-check-report/dependency-check-report.html')) {
                            echo "Archivo de reporte encontrado, publicando..."
                            publishHTML([
                                allowMissing: false,
                                alwaysLinkToLastBuild: true,
                                keepAll: true,
                                reportDir: 'dependency-check-report',
                                reportFiles: 'dependency-check-report.html',
                                reportName: 'OWASP Dependency Check Report'
                            ])
                        } else {
                            echo "ERROR: No se encontró el archivo de reporte"
                            sh 'find . -name "*dependency*" -type f'
                        }
                    }
                }
            }
        }
        
        stage('Archivar Artefactos') {
            steps {
                echo "=== ARCHIVANDO ARTEFACTOS ==="
                script {
                    // Archiva todos los archivos del reporte
                    if (fileExists('dependency-check-report')) {
                        archiveArtifacts artifacts: 'dependency-check-report/**/*', allowEmptyArchive: true
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo "=== PIPELINE COMPLETADO ==="
            sh 'echo "Workspace actual: $(pwd)"'
            sh 'echo "Archivos en workspace:"'
            sh 'find . -type f -name "*dependency*" || echo "No se encontraron archivos de dependency check"'
        }
        success {
            echo "✅ Pipeline ejecutado exitosamente"
        }
        failure {
            echo "❌ Pipeline falló - Revisa los logs"
            sh 'echo "Listando todos los archivos para debug:"'
            sh 'find . -type f'
        }
    }
}