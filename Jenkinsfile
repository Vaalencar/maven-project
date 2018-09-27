pipeline {
    agent any
    tools{
        maven 'localMaven' 
        }
    stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage ('Deploy to Staging'){
            steps {
                parallel(
                    a: {
                        build job: 'deploy-to-staging'
                    },
                    b: {
                        build job: 'static-analysis'
                    }
                ) 
            }
        }
        stage ('Deploy to Production'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve PRODUCTION Deployment?'
                }

                build job: 'deploy-to-Prod'
            }
            post {
                success {
                    echo 'Code deployed to Production.'
                }

                failure {
                    echo ' Deployment failed.'
                }
            }
        }


    }
}
node {
    stage ('JIRA'){
            def issue = [fields: [ project: [key: 'TESTPRO'],
                        summary: 'New JIRA Created from Jenkins.',
                        description: 'New JIRA Created from Jenkins.',
                        issuetype: [name: 'Task']]]
            def newIssue = jiraNewIssue issue: issue, site: 'YOURJIRASITE'
            echo newIssue.data.key
        }
}