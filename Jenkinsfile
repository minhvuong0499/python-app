pipeline{
    agent any
    stages{
        stage('Git clone'){
            steps{
                git 'https://github.com/minhvuong0499/python-app.git'
            }
        }  
        stage('Create Docker image'){
            steps{
                sh 'docker build -t python-app .'
                sh 'docker run  -p 5000:5000 -d python-app'
            }
        }
    }
}