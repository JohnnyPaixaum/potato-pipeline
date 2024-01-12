node {
    properties([
        pipelineTriggers([
        [$class: 'GenericTrigger',
            genericVariables: [
            [key: 'ref', value: '$.ref']
            ],
            causeString: 'Triggered on $ref',

            token: '',
            tokenCredentialId: 'Token_Webhook',

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
                        docker.withRegistry('https://registry.madlabs.com.br', 'Docker_Registry') {
                            potatoapp = docker.build("registry.madlabs.com.br/${env.JOB_NAME}:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                        }
                    }
                }
            }
           
            stage('Push Image') {
                steps {
                    script {
                        sh 'echo "Test Image Push"'                        
                        docker.withRegistry('https://registry.madlabs.com.br', 'Docker_Registry') {
                            potatoapp.push('lastest')
                            potatoapp.push("${env.BUILD_ID}")
                        }
                    }
                }
            }
            
            stage('Deploy') {
                environment {
                    tag_version = "${env.BUILD_ID}"
                    proj_name = "${env.JOB_NAME}"
                }
                steps {
                    script {
                        sh 'echo "Test Deploy on Kubernetes"'
                        withKubeConfig([credentialsId: 'kubeconfig']){
                            sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deploy.yaml'
                            sh 'sed -i "s/{{PROJECT_NAME}}/$proj_name/g" ./k8s/deploy.yaml'
                            sh 'kubectl apply -f ./k8s/deploy.yaml'
                        }
                    }
                }
            }

            stage('DETECT SOURCE BRANCH ') {
                environment {
                    git_branch = "${env.GIT_BRANCH}"
                }
                steps {
                    sh 'echo "DEBUG - SCM_GIT_BRANCH: $git_branch"'
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