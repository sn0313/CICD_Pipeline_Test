pipeline {
    agent any

    environment {
        // Your GitHub credentials ID (must match Jenkins credentials)
        GITHUB_CREDS = 'github-creds'
        PROD_REPO = 'https://github.com/sn0313/cicd-prod.git'
    }

    stages {
        stage('Build') {
            steps {
                echo "Building dev branch..."
                sh 'ls -l'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh '''
                    if [ -f index.html ]; then
                        echo "Test passed"
                    else
                        echo "index.html missing, test failed"
                        exit 1
                    fi
                '''
            }
        }

        stage('Promote to Prod') {
            when {
                expression { env.BRANCH_NAME == 'main' || env.GIT_BRANCH?.endsWith('/main') }
            }
            steps {
                echo "Mirroring only index.html to cicd-prod repository..."

                withCredentials([usernamePassword(credentialsId: env.GITHUB_CREDS, usernameVariable: 'USER', passwordVariable: 'TOKEN')]) {
                    sh '''
                        git config --global user.email "jenkins@myci.com"
                        git config --global user.name "Jenkins CI"

                        # Clone production repo fresh each time
                        rm -rf cicd-prod
                        git clone https://${USER}:${TOKEN}@github.com/sn0313/cicd-prod.git

                        # Copy only index.html into prod repo
                        cp index.html cicd-prod/index.html

                        cd cicd-prod
                        git add index.html

                        # Commit if there are changes
                        git diff --staged --quiet || git commit -m "Auto-sync from cicd-dev on $(date)"

                        git push origin main
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ CI Pipeline succeeded and mirrored to cicd-prod!'
        }
        failure {
            echo '❌ CI Pipeline failed!'
        }
    }
}
