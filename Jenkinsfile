pipeline {
    agent { docker { image 'alpine/git' } }
    stages {
        stage('clone'){
            steps{
            sh 'sudo apt install git-all'
            git 'https://github.com/myaqut/devops.git'
            }
        }
        stage('testing') {
            steps {
                sh 'python -m pip install --upgrade pip'
                sh 'pip install -r ./flaskapp/requirements.txt'
                sh 'python3 ./flaskapp/unitest.py'
            }
        }
    }
}
