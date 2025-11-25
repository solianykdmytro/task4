pipeline {
    agent any

    environment {
        NUGET_PATH = "${WORKSPACE}\\nuget.exe"
        VS_PATH = "C:\Program Files\Microsoft Visual Studio\18\Community\MSBuild\Current\Bin\MSBuild.exe"
        SOLUTION_FILE = "test_repos.sln"
        TEST_EXECUTABLE = "x64\\Debug\\test_repos.exe"
    }

    stages {
        stage('Restore NuGet Packages') {
            steps {
                script {
                    try {
                        bat """
                            if not exist "${NUGET_PATH}" (
                                powershell -Command "Invoke-WebRequest -Uri 'https://dist.nuget.org/win-x86-commandline/latest/nuget.exe' -OutFile '${NUGET_PATH}'"
                            )
                        """
                        bat "\"${NUGET_PATH}\" restore \"${SOLUTION_FILE}\""
                    } catch (Exception e) {
                        echo "NuGet restore skipped"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    bat "\"${VS_PATH}\" \"${SOLUTION_FILE}\" /p:Configuration=Debug /p:Platform=x64"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        bat "\"${TEST_EXECUTABLE}\" --gtest_output=xml:test_report.xml"
                    } catch (Exception e) {
                        echo "Tests failed but continuing"
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
