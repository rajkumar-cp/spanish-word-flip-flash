pipeline {
    agent any
    
    options {
        ansiColor('xterm') // Enables colored output for better readability
        timestamps() // Adds timestamps to the logs for easier debugging
    }

    environment {
        NODE_VERSION = '22' // Use non-Alpine Node.js version
        NPM_REGISTRY = 'https://registry.npmjs.org' // Default npm registry
    }

    stages {
        stage('build') {
            agent {
                docker {
                    image "node:${NODE_VERSION}" // Use full Node.js image
                    args "--dns 8.8.8.8 --dns 8.8.4.4 --network host" // Use host network and reliable DNS
                }
            }
            steps {
                script {
                    try {
                        echo 'Configuring npm registry...'
                        sh "npm config set registry ${NPM_REGISTRY}" // Use default npm registry
                        echo 'Installing dependencies...'
                        sh 'npm cache clean --force' // Clear npm cache
                        sh 'npm ci --verbose --retry=3' // Retry npm install
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
                            args "--dns 8.8.8.8 --dns 8.8.4.4 --network host"
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
                    args "--dns 8.8.8.8 --dns 8.8.4.4 --network host"
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
