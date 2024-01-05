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
                            potatoapp = docker.build("things.madlabs.com.br:5000/potato:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                        }
                    }
                }
            }
           
            stage('Push Image') {
                steps {
                    script {
                        sh 'echo "Test Image Push"'                        
                        docker.withRegistry('https://things.madlabs.com.br:5000', 'Docker_Registry') {
                            potatoapp.push('lastest')
                            potatoapp.push("${env.BUILD_ID}")
                        }
                    }
                }
            }

            /*
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
