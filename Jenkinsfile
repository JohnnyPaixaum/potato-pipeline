node {
    properties([
        pipelineTriggers([
        [$class: 'GenericTrigger',
            genericVariables: [
            [key: 'ref', value: '$.ref']
            ],
            causeString: 'Triggered on $ref',

            printContributedVariables: true,
            printPostContent: true,

            silentResponse: false,

            regexpFilterText: '$ref',
            regexpFilterExpression: '^refs/heads/(main|dev)$',
            ]
        ])
    ])
}

pipeline {
    agent any
    stages {
            stage('Build Image') {
                steps {
                    script {
                        sh 'echo "Test Image Build"'                        
                        docker.withRegistry('https://things.madlabs.com.br:5000', 'Docker_Registry') {
                            docker.potato = docker.build("things.madlabs.com.br:5000/potato:v1", '-f ./src/Dockerfile ./src')
                        }
                    }
                }
            }
            /*
            stage('Push Image') {
                steps {
                    script {
                        sh 'echo "Test Image Push"'                        
                        docker.withRegistry('https://things.madlabs.com.br:5000', 'Docker_Registry') {
                            docker.push('lastest')
                            docker.push('v1')
                        }
                    }
                }
            }      
            stage('Kubernetes') {
                steps {
                    script {
                        sh 'echo "Test Access"'
                    }           
                }
            }
            stage('DETECT SOURCE BRANCH ') {
                steps {
                    script {
                        if ("$ref" == "refs/heads/main") {
                            echo 'WEBHOOK COMING FROM BRANCH: MAIN'
                        } else {
                            if ("$ref" == "refs/heads/dev") {
                                echo 'WEBHOOK COMING FROM BRANCH: DEV'                    
                           }
                        }
                    }           
                }
            }
            */
    }
}
