pipeline {
    agent any

    stages {
        stage('Restore NuGet') {
            steps {
                script {
                    bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                    try {
                        bat 'nuget.exe restore test_repos.sln'
                    } catch (Exception e) {
                        echo 'NuGet warning'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // Використовуємо VsDevCmd.bat для ініціалізації середовища VS 18
                // Це має виправити помилку "Failed to locate: CL.exe"
                bat 'call "C:\\Program Files\\Microsoft Visual Studio\\18\\Community\\Common7\\Tools\\VsDevCmd.bat" && msbuild test_repos.sln /p:Configuration=Debug /p:Platform=x64'
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
