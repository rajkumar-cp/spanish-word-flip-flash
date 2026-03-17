pipeline {
    agent any
    
    options {
        ansiColor('xterm') // Enables colored output for better readability
        timestamps() // Adds timestamps to the logs for easier debugging
    }

    environment {
        NODE_VERSION = '22-alpine' // Centralized Node.js version for consistency
    }

    stages {
        stage('build') {
            agent {
                docker {
                    image "node:${NODE_VERSION}" // Dynamically use the specified Node.js version
                }
            }
            steps {
                script {
                    try {
                        sh 'npm ci'
                        sh 'npm run build'
                    } catch (Exception e) {
                        error("Build failed: ${e.message}")
                    }
                }
            }
        }

        stage('test') {
            parallel {
                stage('unit tests') {
                    agent {
                        docker {
                            image "node:${NODE_VERSION}"
                            reuseNode true // Ensures the same container is reused for efficiency
                        }
                    }
                    steps {
                        script {
                            try {
                                // Unit tests with Vitest
                                sh 'npx vitest run --reporter=verbose'
                            } catch (Exception e) {
                                error("Unit tests failed: ${e.message}")
                            }
                        }
                    }
                }
            }
        }

        stage('deploy') {
            agent {
                docker {
                    image 'alpine' // Lightweight image for deployment
                }
            }
            steps {
                script {
                    try {
                        // Mock deployment which does nothing
                        echo 'Mock deployment was successful!'
                    } catch (Exception e) {
                        error("Deployment failed: ${e.message}")
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline execution failed!'
        }
    }
}
