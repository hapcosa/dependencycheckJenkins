pipeline {
    agent any
    
    stages {
        stage('Debug Básico') {
            steps {
                script {
                    echo "========================================="
                    echo "INICIANDO DEBUG DEL PIPELINE"
                    echo "========================================="
                    
                    sh 'echo "Usuario actual: $(whoami)"'
                    sh 'echo "Directorio actual: $(pwd)"'
                    sh 'echo "Fecha y hora: $(date)"'
                    
                    echo "Listando archivos en directorio actual:"
                    sh 'ls -la'
                    
                    echo "Verificando si existe package.json:"
                    sh 'test -f package.json && echo "package.json EXISTE" || echo "package.json NO EXISTE"'
                    
                    echo "Verificando herramientas disponibles:"
                    sh 'which node || echo "Node NO encontrado"'
                    sh 'which npm || echo "NPM NO encontrado"'
                    sh 'which dependency-check || echo "dependency-check NO encontrado"'
                    
                    echo "Verificando variables de entorno:"
                    sh 'echo "PATH: $PATH"'
                    sh 'echo "WORKSPACE: $WORKSPACE"'
                }
            }
        }
        
        stage('Crear package.json si no existe') {
            steps {
                script {
                    echo "========================================="
                    echo "CREANDO PACKAGE.JSON DE PRUEBA"
                    echo "========================================="
                    
                    sh '''
                        if [ ! -f package.json ]; then
                            echo "Creando package.json..."
                            cat > package.json << 'EOF'
{
  "name": "safenotes",
  "version": "1.0.0",
  "description": "SafeNotes test project",
  "main": "index.js",
  "scripts": {
    "test": "echo \\"Test ejecutado correctamente\\" && exit 0",
    "start": "echo \\"Aplicación iniciada\\""
  },
  "dependencies": {
    "express": "^4.18.0",
    "lodash": "^4.17.21"
  },
  "devDependencies": {
    "jest": "^29.0.0"
  }
}
EOF
                            echo "package.json creado exitosamente"
                        else
                            echo "package.json ya existe"
                        fi
                    '''
                    
                    sh 'cat package.json'
                }
            }
        }
        
        stage('Test NPM Install') {
            steps {
                script {
                    echo "========================================="
                    echo "PROBANDO NPM INSTALL"
                    echo "========================================="
                    
                    sh 'npm --version'
                    sh 'npm install --verbose'
                    sh 'ls -la'
                    sh 'test -d node_modules && echo "node_modules CREADO" || echo "node_modules NO CREADO"'
                }
            }
        }
        
        stage('Test NPM Test') {
            steps {
                script {
                    echo "========================================="
                    echo "PROBANDO NPM TEST"
                    echo "========================================="
                    
                    sh 'npm test'
                }
            }
        }
        
        stage('Test Dependency Check - Verificación') {
            steps {
                script {
                    echo "========================================="
                    echo "VERIFICANDO DEPENDENCY-CHECK"
                    echo "========================================="
                    
                    // Intentar diferentes formas de encontrar dependency-check
                    sh 'which dependency-check || echo "dependency-check no está en PATH"'
                    sh 'ls -la /usr/local/bin/ | grep dependency || echo "No encontrado en /usr/local/bin"'
                    sh 'find /usr -name "*dependency*" 2>/dev/null || echo "Búsqueda en /usr completada"'
                    
                    // Verificar si el plugin está instalado
                    sh 'test -f /var/jenkins_home/tools/org.jenkinsci.plugins.DependencyCheck.tools.DependencyCheckInstallation/dependency-check/bin/dependency-check.sh && echo "Plugin dependency-check ENCONTRADO" || echo "Plugin dependency-check NO ENCONTRADO"'
                }
            }
        }
        
        stage('Crear reporte manual') {
            steps {
                script {
                    echo "========================================="
                    echo "CREANDO REPORTE MANUAL PARA PRUEBA"
                    echo "========================================="
                    
                    sh 'mkdir -p dependency-check-report'
                    
                    sh '''
                        cat > dependency-check-report/dependency-check-report.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>SafeNotes - Reporte de Prueba</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .header { background-color: #f0f0f0; padding: 20px; }
        .content { margin: 20px 0; }
        .success { color: green; }
        .warning { color: orange; }
    </style>
</head>
<body>
    <div class="header">
        <h1>SafeNotes - Reporte de Seguridad</h1>
        <p>Proyecto: SafeNotes</p>
        <p>Fecha: $(date)</p>
    </div>
    
    <div class="content">
        <h2>Estado del Pipeline</h2>
        <p class="success">✅ Pipeline ejecutándose correctamente</p>
        <p class="success">✅ NPM install completado</p>
        <p class="success">✅ NPM test ejecutado</p>
        
        <h2>Próximos pasos</h2>
        <p>1. Instalar OWASP Dependency Check</p>
        <p>2. Configurar la herramienta en Jenkins</p>
        <p>3. Ejecutar escaneo real de vulnerabilidades</p>
    </div>
</body>
</html>
EOF
                    '''
                    
                    sh 'ls -la dependency-check-report/'
                    sh 'cat dependency-check-report/dependency-check-report.html'
                }
            }
        }
        
        stage('Publicar Reporte') {
            steps {
                script {
                    echo "========================================="
                    echo "PUBLICANDO REPORTE HTML"
                    echo "========================================="
                    
                    // Verificar que el archivo existe
                    sh 'test -f dependency-check-report/dependency-check-report.html && echo "Archivo HTML EXISTE" || echo "Archivo HTML NO EXISTE"'
                    
                    // Publicar el reporte
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'dependency-check-report',
                        reportFiles: 'dependency-check-report.html',
                        reportName: 'SafeNotes Security Report'
                    ])
                    
                    // Archivar como artefacto también
                    archiveArtifacts artifacts: 'dependency-check-report/**/*', allowEmptyArchive: false
                }
            }
        }
    }
    
    post {
        always {
            script {
                echo "========================================="
                echo "RESUMEN FINAL DEL PIPELINE"
                echo "========================================="
                
                sh 'echo "Workspace: $(pwd)"'
                sh 'echo "Archivos generados:"'
                sh 'find . -name "*.html" -o -name "*.json" -o -name "package*"'
                
                echo "Pipeline completado. Revisa:"
                echo "1. La pestaña 'SafeNotes Security Report' en este build"
                echo "2. Los 'Build Artifacts' para descargar archivos"
                echo "3. El workspace para archivos locales"
            }
        }
        success {
            echo "✅ PIPELINE COMPLETADO EXITOSAMENTE"
        }
        failure {
            echo "❌ PIPELINE FALLÓ - Revisa los logs arriba"
        }
    }
}