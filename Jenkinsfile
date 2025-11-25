pipeline {
    agent any

    stages {
        stage('Setup Environment') {
            steps {
                script {
                    // 1. Скачуємо NuGet (тупо в лоб)
                    bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                    // 2. Відновлюємо пакети
                    bat 'nuget.exe restore test_repos.sln'
                }
            }
        }

        stage('Find & Build') {
            steps {
                script {
                    // Список ВСІХ можливих шляхів, які ми бачили у твоїх логах і скріншотах
                    def possiblePaths = [
                        // Шлях 1: Папка 18 (якщо ти довстановив туди C++)
                        "C:\\Program Files\\Microsoft Visual Studio\\18\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe",
                        // Шлях 2: BuildTools (який був на твоєму скріншоті - найнадійніший)
                        "C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe",
                        // Шлях 3: VS 2019 (про запас)
                        "C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe"
                    ]

                    def built = false

                    for (path in possiblePaths) {
                        if (fileExists(path)) {
                            echo "Trying build with: ${path}"
                            try {
                                // Пробуємо зібрати. Якщо вийде - ставимо прапорець built = true
                                bat "\"${path}\" test_repos.sln /p:Configuration=Debug /p:Platform=x64"
                                built = true
                                break // Виходимо з циклу, бо все вийшло
                            } catch (Exception e) {
                                echo "Failed with ${path}, trying next..."
                            }
                        }
                    }

                    if (!built) {
                        error "СУКА, ЖОДЕН MSBUILD НЕ ПРАЦЮЄ! Перевір встановлення C++ компонентів."
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
                        echo 'Tests failed but report generated'
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
