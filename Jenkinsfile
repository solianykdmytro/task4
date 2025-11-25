pipeline {
    agent any

    stages {
        stage('Setup NuGet') {
            steps {
                // Завантажуємо nuget.exe
                bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                
                // Відновлюємо пакети
                bat 'nuget.exe restore test_repos.sln'
            }
        }

        stage('Build') {
            steps {
                // Будуємо через папку 18 (де ти тільки що встановив C++)
                bat '"C:\\Program Files\\Microsoft Visual Studio\\18\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" test_repos.sln /p:Configuration=Debug /p:Platform=x64'
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        // Запускаємо тести
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
