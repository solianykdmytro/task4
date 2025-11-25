pipeline {
    agent any

    stages {
        stage('Restore NuGet') {
            steps {
                script {
                    // 1. Завантажуємо nuget
                    bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                    
                    // 2. Відновлюємо пакети (ігноруємо помилку, якщо Visual Studio ще "глючить", бо пакети вже є)
                    try {
                        bat 'nuget.exe restore test_repos.sln'
                    } catch (Exception e) {
                        echo 'Warning: NuGet restore had issues, but attempting build anyway...'
                    }
                }
            }
        }

        stage('Auto-Build') {
            steps {
                script {
                    echo "--- АВТОМАТИЧНИЙ ПОШУК VISUAL STUDIO ---"
                    
                    // Шлях до vswhere (стандартний інструмент Microsoft)
                    def vswhere = "C:\\Program Files (x86)\\Microsoft Visual Studio\\Installer\\vswhere.exe"
                    
                    // Команда знайде шлях до найновішого MSBuild.exe
                    def cmd = "\"${vswhere}\" -latest -products * -requires Microsoft.Component.MSBuild -find MSBuild\\**\\Bin\\MSBuild.exe"
                    
                    // Виконуємо пошук
                    def msbuildPath = bat(returnStdout: true, script: cmd).trim()
                    // Беремо останній рядок (іноді Jenkins додає сміття в лог)
                    msbuildPath = msbuildPath.readLines().last()

                    if (msbuildPath && fileExists(msbuildPath)) {
                        echo "Знайдено MSBuild: ${msbuildPath}"
                        // Запускаємо збірку
                        bat "\"${msbuildPath}\" test_repos.sln /p:Configuration=Debug /p:Platform=x64"
                    } else {
                        // План Б: Якщо vswhere не впорався, пробуємо твою папку 18 напряму
                        echo "vswhere не знайшов шлях, пробуємо папку 18..."
                        bat '"C:\\Program Files\\Microsoft Visual Studio\\18\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" test_repos.sln /p:Configuration=Debug /p:Platform=x64'
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
                        echo 'Tests failed, but report generated'
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
