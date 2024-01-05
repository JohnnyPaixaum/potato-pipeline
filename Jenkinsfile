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
            stage('Docker') {
                steps {
                    script {
                        sh 'echo "Test Dockerd connection"'
                    }
                Image.id
                Container.id
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
    }
}
