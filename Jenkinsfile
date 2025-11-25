pipeline {
    agent any

    stages {
        stage('Restore NuGet Packages') {
            steps {
                script {
                    // Завантажуємо nuget.exe, якщо його немає (для надійності)
                    def nugetPath = "${WORKSPACE}\\nuget.exe"
                    if (!fileExists(nugetPath)) {
                        powershell -Command "Invoke-WebRequest -Uri 'https://dist.nuget.org/win-x86-commandline/latest/nuget.exe' -OutFile '${nugetPath}'"
                    }
                    // Відновлюємо пакети
                    bat "\"${nugetPath}\" restore \"test_repos.sln\""
                }
            }
        }

        stage('Build') {
            steps {
                // Твій шлях до папки 18
                bat '"C:\\Program Files\\Microsoft Visual Studio\\18\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" test_repos.sln /p:Configuration=Debug /p:Platform=x64'
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        // Шлях до exe файлу
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
