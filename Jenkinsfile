pipeline {
    agent any

    environment {
        // Назва файлу рішення (з твого репозиторію)
        SOLUTION_NAME = 'test_repos.sln'
        // Шлях до exe файлу після збірки (x64/Debug)
        TEST_EXE = 'x64\\Debug\\test_repos.exe'
    }

    stages {
        // Етап 1: Відновлення пакетів (щоб працював GoogleTest)
        stage('Restore NuGet') {
            steps {
                script {
                    // Скачуємо nuget.exe, якщо його немає
                    bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                    // Відновлюємо пакети
                    bat "nuget.exe restore ${SOLUTION_NAME}"
                }
            }
        }

        // Етап 2: Збірка (Build)
        stage('Build') {
            steps {
                // Використовуємо інструмент, який ми налаштували в Етапі 1 (пункт 2)
                // 'MyMSBuild' - це назва, яку ти дав у Global Tool Configuration
                // Якщо ти не налаштував Tools, заміни tool 'MyMSBuild' на повний шлях у лапках
                bat "\"${tool 'MyMSBuild'}\" ${SOLUTION_NAME} /p:Configuration=Debug /p:Platform=x64"
            }
        }

        // Етап 3: Тестування (Test)
        stage('Test') {
            steps {
                // Запуск тестів і генерація XML звіту
                bat "${TEST_EXE} --gtest_output=xml:test_report.xml"
            }
        }
    }

    post {
        always {
            // Публікація результатів (вимога методички для звітів)
            junit 'test_report.xml'
            cleanWs() // Очистка робочої папки
        }
    }
}
