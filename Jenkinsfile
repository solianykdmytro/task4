pipeline {
    agent any

    stages {
        stage('Find MSBuild') {
            steps {
                echo '=== ПОЧИНАЄМО ГЛОБАЛЬНИЙ ПОШУК ==='
                script {
                    try {
                        echo '--- Check vswhere ---'
                        bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\Installer\\vswhere.exe" -latest -products * -requires Microsoft.Component.MSBuild -property installationPath'
                    } catch (err) {
                        echo 'vswhere failed'
                    }
                }
                script {
                    echo '--- Search in x86 ---'
                    bat 'dir /s /b "C:\\Program Files (x86)\\MSBuild.exe"'
                }
                script {
                    echo '--- Search in x64 ---'
                    bat 'dir /s /b "C:\\Program Files\\MSBuild.exe"'
                }
            }
        }
    }
}
