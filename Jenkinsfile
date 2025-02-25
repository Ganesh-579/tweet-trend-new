pipeline {
    agent {
        node{
            label 'maven'
        }
    }

    stages {
        stage('clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Ganesh-579/tweet-trend-new.git'
            }
        }
    }
}