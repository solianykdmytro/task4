pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    def possiblePaths = [
                        "C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe",
                        "C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Professional\\MSBuild\\Current\\Bin\\MSBuild.exe",
                        "C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Enterprise\\MSBuild\\Current\\Bin\\MSBuild.exe",
                        "C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe",
                        "C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe",
                        "C:\\Program Files\\Microsoft Visual Studio\\2022\\Professional\\MSBuild\\Current\\Bin\\MSBuild.exe"
                    ]

                    def msbuildPath = null

                    for (path in possiblePaths) {
                        if (fileExists(path)) {
                            msbuildPath = path
                            echo "SUCCESS: Found MSBuild at: ${msbuildPath}"
                            break
                        }
                    }

                    if (msbuildPath == null) {
                        error "CRITICAL: Could not find MSBuild in any standard folder!"
                    }

                    bat "\"${msbuildPath}\" test_repos.sln /p:Configuration=Debug /p:Platform=x64"
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
        }
    }
}
