pipeline {
    agent any

    stages {
        stage('Debug File System') {
            steps {
                echo '=== SHOWING ALL FILES IN REPOSITORY ==='
                // Ця команда покаже ВСІ файли у всіх папках
                bat 'dir /s /b'
                echo '======================================='
            }
        }
    }
}
