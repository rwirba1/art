pipeline {
    agent any

    stages {
        stage('Initialize') {
            steps {
                script {
                    // Define parameters only if they don't exist
                    if (!params.SOURCE_FILE_URL) {
                        properties([
                            parameters([
                                string(name: 'SOURCE_FILE_URL', defaultValue: '', description: 'Full URL of the source file in Nexus'),
                                string(name: 'DESTINATION_REPO_URL', defaultValue: '', description: 'Base URL of the destination repository in Nexus')
                            ])
                        ])
                    }
                }
            }
        }

        stage('Download File from Source') {
            steps {
                script {
                    // Ensure parameters are set
                    if (params.SOURCE_FILE_URL && params.DESTINATION_REPO_URL) {
                        def fileName = params.SOURCE_FILE_URL.tokenize('/').last()

                        // Create a temporary directory to store the downloaded file
                        sh "mkdir -p /tmp/nexus-download"

                        // Download the file from the source URL
                        def encodedSourceUrl = params.SOURCE_FILE_URL.replaceAll(' ', '%20')
                        sh "curl -u \$NEXUS_USER:\$NEXUS_PASSWORD -o /tmp/nexus-download/${fileName} '${params.SOURCE_FILE_URL}'"
                    } else {
                        error "Source or Destination URL not defined."
                    }
                }
            }
        }

        stage('Upload File to Destination Repo') {
            steps {
                script {
                    def fileName = params.SOURCE_FILE_URL.tokenize('/').last()

                    // Upload the file to the destination repository
                    sh "curl -u admin:admin123 --upload-file /tmp/nexus-download/${fileName} '${params.DESTINATION_REPO_URL}${fileName}'"
                }
            }
        }
    }

    post {
        always {
            // Cleanup temporary directory
            sh "rm -rf /tmp/nexus-download"
        }
    }
}
