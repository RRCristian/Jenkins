pipeline {
    agent any

    stages {
        stage('Execute Command') {
            steps {
                bat 'echo Hello EY - COE DevOps'
            }
        }

        stage('Prepare bpprocess and bprelease') {
            when {
                anyOf {
                    branch 'development'
                    branch 'feature/*'
                }
            }
            steps {
                script {
                    def workspace = env.WORKSPACE
                    
                    // Obtener la versión de Suma.bpprocess
                    def bpprocessVersion = powershell(
                        returnStdout: true,
                        script: """
                        \$filePath = "${workspace}/Process/Suma.bpprocess"
                        
                        if (-Not (Test-Path \$filePath)) {
                            Write-Output "Archivo no encontrado: \$filePath"
                            exit 1
                        }

                        [xml]\$xml = Get-Content \$filePath
                        \$version = \$xml.process.version
                        Write-Output \$version
                        """
                    ).trim()
                    
                    echo "La versión del proceso de Blue Prism (Suma.bpprocess) es: ${bpprocessVersion}"
                    
                    // Obtener la versión de Suma.bprelease
                    def bpreleaseVersion = powershell(
                        returnStdout: true,
                        script: """
                        \$filePath = "${workspace}/Release/Suma.bprelease"
                        
                        if (-Not (Test-Path \$filePath)) {
                            Write-Output "Archivo no encontrado: \$filePath"
                            exit 1
                        }

                        [xml]\$xml = Get-Content \$filePath
                        \$version = \$xml.bpRelease.version
                        Write-Output \$version
                        """
                    ).trim()
                    
                    echo "La versión del release de Blue Prism (Suma.bprelease) es: ${bpreleaseVersion}"
                }
            }
        }
        
        stage('Deploy to QA') {
            steps {
                node('local-agent') { // El agente de Jenkins que se ejecuta en tu máquina local
                    script {

                        def newProcessName = 'Suma-QA.bpprocess'
                        def newReleaseName = 'Suma-QA.bprelease'

                        def bpProcessPath = "C:/Users/User/AppData/Local/Jenkins/.jenkins/workspace/BPTest_development/Process/Suma.bpprocess"
                        def bpReleasePath = "C:/Users/User/AppData/Local/Jenkins/.jenkins/workspace/BPTest_development/Release/Suma.bprelease"
                        def bluePrismPath = 'C:\\Program Files\\Blue Prism Limited\\Blue Prism Automate\\automatec.exe'

                        bat """
                        copy "${bpProcessPath}" "C:/Users/User/AppData/Local/Jenkins/.jenkins/workspace/BPTest_development/Process/${newProcessName}"
                        copy "${bpReleasePath}" "C:/Users/User/AppData/Local/Jenkins/.jenkins/workspace/BPTest_development/Release/${newReleaseName}"
                        """
                        
                        def processPathQA = "C:/Users/User/AppData/Local/Jenkins/.jenkins/workspace/BPTest_development/Process/${newProcessName}"
                        def releasePathQA = "C:/Users/User/AppData/Local/Jenkins/.jenkins/workspace/BPTest_development/Release/${newReleaseName}"

                        // Importar Suma.bpprocess
                        bat """
                        cd "C:\\Program Files\\Blue Prism Limited\\Blue Prism Automate"
                        automatec.exe /import "${processPathQA}" /user admin Devops2024
                        """
                        
                        // Importar Suma.bprelease
                        bat """
                        cd "C:\\Program Files\\Blue Prism Limited\\Blue Prism Automate"
                        automatec.exe /importrelease "${releasePathQA}" /user admin Devops2024
                        """
                    }
                }
            }
        }
    }
}
