pipeline {
    agent any

    stages {
        stage('Find MSBuild') {
            steps {
                script {
                    try {
                        bat "C:\\Program Files (x86)\\Microsoft Visual Studio\\Installer\\vswhere.exe"
                    } catch (err) {
                    }
                }
                script {
                    bat 'dir /s /b "C:\\Program Files (x86)\\MSBuild.exe"'
                }
                script {
                    bat 'dir /s /b "C:\\Program Files\\MSBuild.exe"'
                }
            }
        }
    }
}
