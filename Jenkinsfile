def registry = 'http://valaxy679.jfrog.io'  // Removed extra '/'

pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean deploy -Dmaven.test.skip=true'
            }
        }

        /* Uncomment if tests are needed
        stage('test') {
            steps {
                echo "<-----unit test started----->"
                sh 'mvn surefire-report:report'
                echo "<-----unit test ended----->"
            }
        }
        

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }*/
        stage('Upload Artifact to JFrog') {
            steps {
                script {
                    echo '<----- Uploading artifact to JFrog ----->'

                    // Create Artifactory server connection
                    def server = Artifactory.newServer (url: registry+"/artifactory", credentialsId: 'jfrog-cred')
                    def properties = "buildid=${env.BUILD_ID},commitid=${env.GIT_COMMIT}";

                    // Ensure target repository is correct
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging/(*)",
                                "target": "lib-release-local2/{1}",
                                "flat": "false",
                                "props": "${properties}",
                                "exclusions": ["*.sha1", "*.md5"]
                            }
                        ]
                    }"""

                    echo "Uploading with spec: ${uploadSpec}"

                    // Upload artifact
                    def buildInfo = server.upload(spec: uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)

                    echo '<----- JAR publish completed ----->'
                }
            }
        }
    }
}
