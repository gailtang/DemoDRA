#!groovy
/*
    This is an sample Jenkins file for the Weather App, which is a node.js application that has unit test, code coverage
    and functional verification tests, deploy to staging and production environment and use IBM Cloud DevOps gate.
    We use this as an example to use our plugin in the Jenkinsfile
    Basically, you need to specify required 4 environment variables and then you will be able to use the 4 different methods
    for the build/test/deploy stage and the gate
 */
pipeline {
    agent any
    environment {
        // You need to specify 4 required environment variables first, they are going to be used for the following IBM Cloud DevOps steps
        IBM_CLOUD_DEVOPS_API_KEY = '7313881c824346b28bdea2c70e7d7ce2'
        IBM_CLOUD_DEVOPS_ORG = 'gail.tang@cn.ibm.com'
        IBM_CLOUD_DEVOPS_APP_NAME = 'Gail-devops-insight-1'
        IBM_CLOUD_DEVOPS_TOOLCHAIN_ID = 'e704d358-4712-4fe3-8715-6f8054306183'
    }
    stages {
        stage('Build') {
            environment {
                // get git commit from Jenkins
                GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                GIT_BRANCH = 'master'
                GIT_REPO = 'https://github.com/gailtang/DemoDRA/'
            }
            steps {
                echo "building1111"
                echo $IBM_CLOUD_DEVOPS_API_KEY
            }
            // post build section to use "publishBuildRecord" method to publish build record
            post {
                success {
                    publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "${GIT_REPO}", result:"SUCCESS"
                }
                failure {
                    publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "${GIT_REPO}", result:"FAIL"
                }
            }
        }
        stage('Unit Test and Code Coverage') {
            steps {
                echo "unit testing and code coverage"
            }
            // post build section to use "publishTestResult" method to publish test result
            post {
                always {
                    publishTestResult type:'unittest', fileLocation: './mochatest.json'
                    publishTestResult type:'code', fileLocation: './tests/coverage/reports/coverage-summary.json'
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                // Push the Weather App to Bluemix, staging space
                sh '''
                        echo "Deploying App to Staging"

                    '''
            }
            // post build section to use "publishDeployRecord" method to publish deploy record
            post {
                success {
                    publishDeployRecord environment: "STAGING", result:"SUCCESS"
                }
                failure {
                    publishDeployRecord environment: "STAGING", result:"FAIL"
                }
            }
        }
        stage('Gate') {
            steps {
                // use "evaluateGate" method to leverage IBM Cloud DevOps gate
                evaluateGate policy: 'POLICY_NAME_PLACEHOLDER', forceDecision: 'true'
            }
        }
        stage('Deploy to Prod') {
            steps {
                // Push the Weather App to Bluemix, production space
                sh '''
                        echo "Deploying to Prod"
                    '''
            }
            // post build section to use "publishDeployRecord" method to publish deploy record
            post {
                success {
                    publishDeployRecord environment: "PRODUCTION", result:"SUCCESS"
                }
                failure {
                    publishDeployRecord environment: "PRODUCTION", result:"FAIL"
                }
            }
        }
    }
}
