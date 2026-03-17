pipeline {
    agent any
    
    options {
        ansiColor('xterm') // Enables colored output for better readability
        timestamps() // Adds timestamps to the logs for easier debugging
    }

    environment {
        NODE_VERSION = '22' // Switch to non-Alpine Node.js version
    }

    stages {
        stage('build') {
            agent {
                docker {
                    image "node:${NODE_VERSION}" // Use non-Alpine Node.js image
                    args "-v ${WORKSPACE}:/workspace" // Explicit volume mapping
                }
            }
            steps {
                script {
                    try {
                        echo 'Installing dependencies...'
                        sh 'npm ci --verbose' // Add verbose logging
                        echo 'Building the project...'
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
                            reuseNode true
                            args "-v ${WORKSPACE}:/workspace"
                        }
                    }
                    steps {
                        script {
                            try {
                                echo 'Running unit tests...'
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
                    args "-v ${WORKSPACE}:/workspace"
                }
            }
            steps {
                script {
                    try {
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
