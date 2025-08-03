pipeline {
    agent any

    stages {
        stage('Verify Branch') {
            steps {
                echo "${GIT_BRANCH}"
            }
        }
        stage('Docker Build') {
            steps {
                sh(script: 'docker compose build')
            }
        }
        stage('Start App') {
            steps {
                sh(script: 'docker compose up -d')
            }
        }
        stage('Run Tests') {
            steps {
                sh(script: 'docker exec -t azure-vote-front pytest ./tests/test_sample.py')
            }
            post {
                success {
                    echo "Tests passed!"
                }
                failure {
                    echo "Tests failed"
                }
            }
        }
        stage('Docker Push') {
            steps {
                echo "Running in $WORKSPACE"
                dir("$WORKSPACE/azure-vote") {
                    script {
                        docker.withRegistry('', 'dockerhub') {
                            def image = docker.build('meletiop22/azure-vote-app:2025')
                            image.push()
                        }
                    }
                }
            }
        }
        stage('Run Grype') {
            agent {label ''}
            steps {
                grypeScan autoInstall: true, repName: 'grypeReport_${JOB_NAME}_${BUILD_NUMBER}.txt', scanDest: 'registry:meletiop22/azure-vote-app:2025'
            }
            post {
                always {
                    recordIssues(
                        tools: [grype()],
                        aggregatingResults: true,
                    )
                }
            }
        }
    }
    post {
        always {
            sh(script: 'docker compose down')
        }
    }
}