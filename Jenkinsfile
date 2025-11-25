pipeline {
    agent any

    stages {
        stage('Restore NuGet') {
            steps {
                script {
                    // Завантажуємо та відновлюємо пакети
                    bat 'powershell -Command "Invoke-WebRequest https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget.exe"'
                    bat 'nuget.exe restore test_repos.sln'
                }
            }
        }

        stage('Find & Build') {
            steps {
                script {
                    echo "--- ШУКАЄМО MSBUILD У ПАПЦІ BUILDTOOLS ---"
                    // Ця команда знайде реальний шлях до MSBuild.exe всередині папки BuildTools
                    // Ми використовуємо 'dir /s /b', щоб знайти файл де завгодно всередині
                    def findCmd = 'dir /s /b "C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild.exe"'
                    
                    try {
                        // Отримуємо результат пошуку
                        def msbuildPath = bat(returnStdout: true, script: findCmd).trim()
                        
                        // Jenkins може повернути кілька рядків, беремо останній (шлях до файлу)
                        def lines = msbuildPath.readLines()
                        def realPath = ""
                        
                        for (line in lines) {
                            if (line.contains("MSBuild.exe") && line.contains(":")) {
                                realPath = line.trim()
                            }
                        }

                        if (realPath != "") {
                            echo "ЗНАЙДЕНО: ${realPath}"
                            // Запускаємо знайдений MSBuild
                            bat "\"${realPath}\" test_repos.sln /p:Configuration=Debug /p:Platform=x64"
                        } else {
                            error "У папці BuildTools файлу MSBuild.exe не знайдено!"
                        }
                    } catch (Exception e) {
                        echo "Помилка пошуку в BuildTools. Пробуємо папку 18..."
                        // Якщо не знайшли, пробуємо твою папку 18 (на випадок, якщо ти її вже полагодив)
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
