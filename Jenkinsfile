pipeline {
    agent any
    
    environment {
        VENV = "venv"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:yourname/python-app.git'
            }
        }

        stage('Setup Python') {
            steps {
                sh '''
                python3 -m venv $VENV
                source $VENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                source $VENV/bin/activate
                pytest --maxfail=1 --disable-warnings -q
                '''
            }
        }

        stage('Push to Git (Trigger Render)') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'git-credentials', 
                    usernameVariable: 'GIT_USER', 
                    passwordVariable: 'GIT_PASS'
                )]) {
                    sh '''
                    git config user.email "jenkins@ci.com"
                    git config user.name "Jenkins CI"
                    git remote set-url origin https://$GIT_USER:$GIT_PASS@github.com/yourname/python-app.git
                    
                    git add .
                    git commit -m "CI: update from Jenkins build" || echo "No changes"
                    git push origin main
                    '''
                }
            }
        }
    }
}
