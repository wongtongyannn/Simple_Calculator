pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                deleteDir() // Clear workspace
                git branch: 'main', url: 'https://github.com/wongtongyannn/Simple_Calculator.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Create a virtual environment and activate it
                sh '''
                    python3 -m venv venv
                    source venv/bin/activate
                    python3 -m pip install -r requirements.txt
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Running calculator script...'
                // Activate the virtual environment before running the script
                sh '''
                    source venv/bin/activate
                    python3 calculator.py 1 10 20
                '''
            }
        }

        stage('Test') {
            steps {
                script {
                    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                        echo 'Running unit tests...'
                        // Activate the virtual environment and run tests
                        sh '''
                            source venv/bin/activate
                            pytest --maxfail=1 --disable-warnings
                        '''
                    }
                }
            }
        }

        stage('Deploy to GitHub Pages') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-credentials', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh '''
                        npm install -g --silent gh-pages@2.1.1
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins"
                        gh-pages --dotfiles --message '[skip ci] Updates' --dist build
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Build and test stages completed successfully.'
        }
        failure {
            echo 'One or more stages failed. Check the logs for details.'
        }
    }
}
