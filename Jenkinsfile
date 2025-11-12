pipeline {
    agent any

    environment {
        GITHUB_CREDS = 'github-creds' // Jenkins credential ID (username + PAT)
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
    echo "Test failed: index.html missing"
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

# Clean previous clone if exists
rm -rf cicd-prod

# Clone production repo
git clone https://${USER}:${TOKEN}@github.com/sn0313/cicd-prod.git
cd cicd-prod

# Pull latest changes first to avoid non-fast-forward errors
git pull --rebase origin main || true

# Copy only index.html from dev repo
cp ../index.html index.html

# Stage changes
git add index.html

# Commit only if there are changes
if ! git diff --staged --quiet; then
    git commit -m "Auto-sync from cicd-dev on $(date)"
    git push origin main --force
else
    echo "No changes to commit."
fi
'''
                }
            }
        }
    }

    post {
        success {
            echo 'CI Pipeline completed successfully!'
        }
        failure {
            echo 'CI Pipeline failed!'
        }
    }
}
