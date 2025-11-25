pipeline {
    agent any

    stages {
        stage('Find MSBuild') {
            steps {
                echo '=== ПОЧИНАЄМО ПОШУК ==='
                
                script {
                    // Шукаємо в папці Program Files (x86) - тут часто бувають BuildTools
                    echo '--- Searching in Program Files (x86) ---'
                    try {
                        // Ця команда знайде всі файли MSBuild.exe, навіть сховані
                        bat 'dir "C:\\Program Files (x86)\\Microsoft Visual Studio\\MSBuild.exe" /s /b'
                    } catch (Exception e) {
                        echo 'Нічого не знайдено в x86'
                    }

                    // Шукаємо в папці Program Files (x64)
                    echo '--- Searching in Program Files ---'
                    try {
                        bat 'dir "C:\\Program Files\\Microsoft Visual Studio\\MSBuild.exe" /s /b'
                    } catch (Exception e) {
                        echo 'Нічого не знайдено в x64'
                    }
                }
            }
        }
    }
}
