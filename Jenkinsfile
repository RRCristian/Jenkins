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

                        # Mostrar el contenido del archivo
                        \$content = Get-Content \$filePath
                        Write-Output "Contenido del archivo: \$content"

                        # Leer el XML y extraer la versión
                        [xml]\$xml = Get-Content \$filePath
                        \$version = \$xml.SelectSingleNode("//Version").InnerText
                        Write-Output \$version
                        """
                    ).trim()
                    echo "La versión del proceso de Blue Prism es: ${version}"
                }
            }
        }
    }
}
