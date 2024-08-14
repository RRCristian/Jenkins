pipeline {
    agent any

    stages {
        stage('Execute Command') {
            steps {
                bat 'echo Hello EY - COE DevOps'
            }
        }

        stage('Prepare bpprocess') {
            when {
                anyOf {
                    branch 'development'
                    branch 'feature/*'
                }
            }
            steps {
                script {
                    // Usando PowerShell para extraer la versión del archivo .bprelease
                    def workspace = env.WORKSPACE
                    def version = powershell(
                        returnStdout: true,
                        script: """
                        \$filePath = "${workspace}/Process/Suma.bpprocess"
                        
                        # Verificar que el archivo existe
                        if (-Not (Test-Path \$filePath)) {
                            Write-Output "Archivo no encontrado: \$filePath"
                            exit 1
                        }

                        # Leer el XML y extraer la versión
                        [xml]\$xml = Get-Content \$filePath
                        \$version = \$xml.process.version
                        Write-Output \$version
                        """
                    ).trim()
                    echo "La versión del proceso de Blue Prism es: ${version}"
                }
            }
        }
    }
}
