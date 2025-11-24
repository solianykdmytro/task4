pipeline {
    agent any

    stages {
        stage('Restore & Build') {
            steps {
                bat '"C:\\Program Files\\Microsoft Visual Studio\\18\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" vs_mkr_test1\\vs_mkr_test1.sln /t:Restore /p:Configuration=Debug'
                bat '"C:\\Program Files\\Microsoft Visual Studio\\18\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" vs_mkr_test1\\vs_mkr_test1.sln /p:Configuration=Debug /p:Platform="x86"'
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        bat 'vs_mkr_test1\\Debug\\vs_mkr_test1.exe --gtest_output=xml:test_report.xml'
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
        }
    }
}
