pipeline {
    agent any

    stages {
        stage('Restore NuGet') {
            steps {
                script {
                    // 1. Скачуємо nuget.exe (щоб точно був)
                    bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                    
                    // 2. Відновлюємо пакети (ігноруємо помилки, якщо пакети вже є)
                    try {
                        bat 'nuget.exe restore test_repos.sln'
                    } catch (Exception e) {
                        echo 'NuGet restore warning (continuing...)'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // ВИКОРИСТОВУЄМО НАДІЙНИЙ ШЛЯХ (BuildTools)
                // Ми бачили цю папку на твоєму скріншоті, вона має працювати
                bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe" test_repos.sln /p:Configuration=Debug /p:Platform=x64'
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        // Запускаємо тести і генеруємо звіт
                        bat 'x64\\Debug\\test_repos.exe --gtest_output=xml:test_report.xml'
                    } catch (err) {
                        // Навіть якщо тести впадуть, ми хочемо бачити звіт
                        echo 'Tests finished'
                    }
                }
            }
        }
    }

    post {
        always {
            // Публікуємо результати, щоб з'явився графік
            junit 'test_report.xml'
            cleanWs()
        }
    }
}
