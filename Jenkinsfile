pipeline {
    agent any  // Indica que el pipeline puede ejecutarse en cualquier agente disponible

    stages {
        stage('Execute Command') {
            steps {
                bat 'echo Hello, Jenkins!'
            }
        }

        stage('Prepare bpprocess'){
            when{                            
                anyOf{
                    branch 'development'
                    branch 'feature/*'                                                
                }
                
            }
            step{   
                 script {
                    // Usando PowerShell para extraer la versión del archivo .bprelease
                    def version = powershell(
                        returnStdout: true,
                        script: '''
                        $filePath = "ruta/al/archivo.bprelease"
                        [xml]$xml = Get-Content $filePath
                        $version = $xml.SelectSingleNode("//Version").InnerText
                        Write-Output $version
                        '''
                    ).trim()
                    echo "La versión del proceso de Blue Prism es: ${version}"
            }
        }


    }
}
