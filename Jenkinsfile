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
        /*stage('test'){
            steps {
                echo "<-----unit test started----->"
                sh 'mvn surefire-report:report'
                echo "<-----unit test started----->"
            }
        }*/
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
    stage ('Quality Gate'){
        steps {
            script { 
            timeout(time: 1, unit: 'HOURS') {
                def qg = waitForQualityGate()
                if (qg.status != 'OK') {
                    error "pipeline aborted due to quality gate failure: ${qg.status}"
                }
            }
            }
        }
    }
    }
} 