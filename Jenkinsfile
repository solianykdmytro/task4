pipeline {
    agent any

    stages {
        stage('Spy Files') {
            steps {
                echo '=== ДИВИМОСЬ ЩО Є У ПАПЦІ 2019 ==='
                // Ця команда покаже, яка версія стоїть у папці 2019 (Community, Enterprise чи інша)
                script {
                    try {
                        bat 'dir "C:\\Program Files (x86)\\Microsoft Visual Studio\\2019"'
                    } catch (err) {
                        echo 'Папки 2019 не знайдено'
                    }
                }
                
                echo '=== ДИВИМОСЬ ЩО Є У ПАПЦІ Program Files (x64) ==='
                // Перевіримо, що є у новій папці
                script {
                    try {
                        bat 'dir "C:\\Program Files\\Microsoft Visual Studio"'
                    } catch (err) {
                        echo 'Папки VS x64 не знайдено'
                    }
                }
            }
        }
    }
}
