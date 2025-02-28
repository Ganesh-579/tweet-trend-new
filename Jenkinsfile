def registry = 'http://valaxy679.jfrog.io/'
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
    stage ('upload artifact to jfrog'){
        steps {
            script {
                echo '<-----uploading artifact to jfrog----->'
                def server = Artifactory.newserver url:registry+"/artifactory", credentialsId: 'jfrog-cred'
                def properties = "buildid=${env.BUILD_ID},commitid=${env.GIT_COMMIT}";
                def uploadspec = """{
                    "files": [
                        {
                            "pattern": "jarstaging/(*)",
                            "target": "libs-release-local1/{1}",
                            "flat": "false",
                            "props": "${properties}"
                            "exclusions": ["**/*.md5", "**/*.sha1"]
                        }
                    ]
                }"""
                def buildInfo = server.upload(uploadspec)
                buildInfo.env.collect()
                server.publishBuildInfo(buildInfo)
                echo '<-----jar publish ended----->'
            }
        }
    }
    }
} 