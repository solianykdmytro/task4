pipeline {
    agent any

    stages {
        stage('Setup NuGet') {
            steps {
                // Простий спосіб скачати nuget без складних змінних
                bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                
                // Відновлення пакетів
                bat 'nuget.exe restore test_repos.sln'
            }
        }

        stage('Build') {
            steps {
                // Використовуємо шлях до BuildTools 2022 (з твого скріншоту), бо 18 не має C++
                bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe" test_repos.sln /p:Configuration=Debug /p:Platform=x64'
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
