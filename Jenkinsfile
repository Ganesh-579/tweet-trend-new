pipeline {
    agent {
        node{
            label 'maven'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
    }

    stages {
        stage('Build'){
            steps {
                sh 'mvn clean deploy -Dmaven.test.skip=true'
            }
        }
        stage('test'{
            steps {
                echo "<-----unit test started----->"
                sh 'mvn surfire-report:report'
                echo "<-----unit test started----->"
            }
        })
        stage('sonarqube analysis'){
        environment {
            scannerHome = tool 'sonar-scanner'
        }
        steps {
            withSonarQubeEnv('sonarqube-server'){
              sh "${scannerHome}/bin/sonar-scanner"
            }
        }
    }
    }
}