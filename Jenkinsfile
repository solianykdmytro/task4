pipeline {
    agent any

    stages {
        stage('Restore NuGet') {
            steps {
                script {
                    // 1. Завантажуємо nuget
                    bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                    
                    // 2. Відновлюємо пакети
                    // (Ігноруємо помилки, бо пакети вже можуть бути на місці)
                    try {
                        bat 'nuget.exe restore test_repos.sln'
                    } catch (Exception e) {
                        echo 'NuGet restore warning'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // ВИПРАВЛЕННЯ ТУТ:
                // Було: ...\Bin"
                // Стало: ...\Bin\MSBuild.exe"
                bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe" test_repos.sln /p:Configuration=Debug /p:Platform=x64'
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        // Запускаємо тести
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
