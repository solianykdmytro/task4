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
                // ПОВЕРТАЄМОСЯ ДО ВЕРСІЇ 18 (Community), де ти встановив C++
                // Додаємо /p:WindowsTargetPlatformVersion=10.0 щоб уникнути проблем з SDK
                bat '"C:\\Program Files\\Microsoft Visual Studio\\18\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" test_repos.sln /p:Configuration=Debug /p:Platform=x64 /p:WindowsTargetPlatformVersion=10.0'
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
