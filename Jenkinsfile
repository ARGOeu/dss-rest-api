  

pipeline {
    agent none
    options {
        checkoutToSubdirectory('dss-rest-api')
        newContainerPerStage()
    }
    environment {
        PROJECT_DIR='dss-rest-api'
    }
    stages {
        stage('REST API Testing & Packaging') {
            agent {
                docker {
                    image 'argo.registry:5000/epel-7-java18'
                    args '-u jenkins:jenkins'
                }
            }
            steps {
                echo 'REST API Packaging & Testing' 
                sh """
                mvn clean install -f ${PROJECT_DIR}/pom.xml
                mvn clean install -f ${PROJECT_DIR}/dss-mock-tsa/pom.xml
                mvn clean package cobertura:cobertura -Dcobertura.report.format=xml -f ${PROJECT_DIR}/rest-api/pom.xml
                """
                junit '**/target/surefire-reports/*.xml'
                cobertura coberturaReportFile: '**/target/site/cobertura/coverage.xml'
                archiveArtifacts artifacts: '**/target/*.war'

            }
            post {
                always {
                    cleanWs()
                }
            }
        }
    }
    post {
        success {
            script{
                if ( env.BRANCH_NAME == 'master' || env.BRANCH_NAME == 'devel' ) {
                    slackSend( message: ":rocket: New version for <$BUILD_URL|$PROJECT_DIR>:$BRANCH_NAME Job: $JOB_NAME !")
                }
            }
        }
        failure {
            script{
                if ( env.BRANCH_NAME == 'master' || env.BRANCH_NAME == 'devel' ) {
                    slackSend( message: ":rain_cloud: Build Failed for <$BUILD_URL|$PROJECT_DIR>:$BRANCH_NAME Job: $JOB_NAME")
                }
            }
        }
    }
}
