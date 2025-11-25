pipeline {
    agent any

    stages {
        stage('Restore NuGet') {
            steps {
                script {
                    bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                    // Якщо restore падає, ігноруємо це, бо пакети вже можуть бути на місці
                    try {
                        bat 'nuget.exe restore test_repos.sln'
                    } catch (Exception e) {
                        echo 'NuGet restore warning (might be ok if packages exist)'
                    }
                }
            }
        }

        stage('Auto-Detect & Build') {
            steps {
                script {
                    echo "--- АВТОПОШУК MSBUILD ---"
                    
                    // Використовуємо офіційну утиліту vswhere для пошуку шляху
                    def vswhere = "C:\\Program Files (x86)\\Microsoft Visual Studio\\Installer\\vswhere.exe"
                    
                    // Шукаємо останній встановлений MSBuild
                    def cmd = "\"${vswhere}\" -latest -products * -requires Microsoft.Component.MSBuild -find MSBuild\\**\\Bin\\MSBuild.exe"
                    
                    try {
                        // Отримуємо результат команди
                        def msbuildPath = bat(returnStdout: true, script: cmd).trim()
                        
                        // Jenkins може повернути сміття в лог, тому беремо останній рядок, який схожий на шлях
                        def lines = msbuildPath.readLines()
                        def realPath = ""
                        for (line in lines) {
                            if (line.contains("MSBuild.exe") && line.contains(":")) {
                                realPath = line.trim()
                            }
                        }

                        if (realPath != "") {
                            echo "ЗНАЙДЕНО ІДЕАЛЬНИЙ MSBUILD: ${realPath}"
                            bat "\"${realPath}\" test_repos.sln /p:Configuration=Debug /p:Platform=x64"
                        } else {
                            error "vswhere не знайшов жодної робочої Visual Studio!"
                        }
                    } catch (Exception e) {
                        error "Помилка при запуску vswhere. Перевір, чи встановлена Visual Studio коректно."
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        bat 'x64\\Debug\\test_repos.exe --gtest_output=xml:test_report.xml'
                    } catch (err) {
                        echo 'Tests failed'
                    }
                }
            }
        }
    }

    post {
        always {
            junit 'test_report.xml'
            cleanWs()
        }
    }
}
