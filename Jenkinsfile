node {
    properties([pipelineTriggers([githubPush()])])
}

pipeline {
    agent any
    stages {

        // INICIO DO BLOCO DO ESTAGIO DE BUILD
        stage('Build Image') {
            environment {
                proj_name = "${env.JOB_NAME}"
                git_origin = "${env.GIT_BRANCH}"
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                script {
                    // Prepara o arquivo dockerfile do projeto substituindo algumas variaveis
                    sh 'sed -i "s/{{PROJECT_NAME}}/$proj_name/g" ./src/Dockerfile'
                    def BRANCH 
                    switch (git_origin) { // Analisa a Branch de Origem
                        case 'origin/main':
                            BRANCH = 'main'
                            // Prepara o arquivo do projeto substituindo algumas variaveis
                            sh 'sed -i "s/{{BRANCH}}/main/g" ./src/$proj_name/index.html'
                            sh 'sed -i "s/{{TAG_VERSION}}/$tag_version/g" ./src/$proj_name/index.html'
                            echo 'BRANCH MAIN DETECT!'
                            // Realiza o Push da Image utilizando as credenciais do registry privado
                                docker.withRegistry('https://registry.madlabs.com.br', 'Docker_Registry') {
                                    potatoapp = docker.build("registry.madlabs.com.br/${env.JOB_NAME}:$BRANCH-${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                                }
                            break
                        case 'origin/homolog':
                            BRANCH = 'homolog'
                            // Prepara o arquivo do projeto substituindo algumas variaveis
                            sh 'sed -i "s/{{BRANCH}}/homolog/g" ./src/$proj_name/index.html'
                            sh 'sed -i "s/{{TAG_VERSION}}/$tag_version/g" ./src/$proj_name/index.html'
                            echo 'BRANCH homolog DETECT!'
                                // Realiza o Push da Image utilizando as credenciais do registry privado
                                docker.withRegistry('https://registry.madlabs.com.br', 'Docker_Registry') {
                                    potatoapp = docker.build("registry.madlabs.com.br/${env.JOB_NAME}:$BRANCH-${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                                }
                            break
                        default:
                            echo 'NO ORIGIN BRANCH DETECT!'
                            exit 1
                    }
                }
            }
        }
        // FIM DO BLOCO DO ESTAGIO DE BUILD
           
        // INICIO DO BLOCO DO ESTAGIO DE PUSH
        stage('Push Image') {
            environment {
                git_origin = "${env.GIT_BRANCH}"
            }
            steps {
                script {
                    def BRANCH
                    switch (git_origin) { // Analisa a Branch de Origem
                        case 'origin/main':
                            BRANCH = 'main' // Define o valor main a variavel BRANCH
                            echo 'BRANCH MAIN DETECT!'
                                // Realiza o Build da Image
                                docker.withRegistry('https://registry.madlabs.com.br', 'Docker_Registry') {
                                    potatoapp.push("latest")
                                    potatoapp.push("$BRANCH-${env.BUILD_ID}")
                                }
                            break
                        case 'origin/homolog':
                            BRANCH = 'homolog' // Define o valor homolog a variavel BRANCH
                            echo 'BRANCH homolog DETECT!'
                                // Realiza o Build da Image
                                docker.withRegistry('https://registry.madlabs.com.br', 'Docker_Registry') {
                                    potatoapp.push("$BRANCH-${env.BUILD_ID}")
                                }
                            break
                        default:
                            echo 'NO ORIGIN BRANCH DETECT!'
                            exit 1
                    }
                }
            }
        }
        // FIM DO BLOCO DO ESTAGIO DE PUSH
            
        // INICIO DO BLOCO DO ESTAGIO DE DEPLOY
        stage('Deploy') {
            environment {
                tag_version = "${env.BUILD_ID}"
                proj_name = "${env.JOB_NAME}"
                git_origin = "${env.GIT_BRANCH}"
                dockerconfigjson = credentials('Registry-config-json') // Atrela o valor de uma credential a uma varial
            }
            steps {
                script {

                    // GET GIT COMMIT MESSAGE
                    env.GIT_COMMIT_MSG = sh (script: 'git log -1 --pretty=%B ${GIT_COMMIT}', returnStdout: true).trim()
                    // APPROVER TO DEPLOY
                    input id: 'Deploy', message: "You Allow To Deploy?\nBranch: \"${env.GIT_BRANCH}\"\nCommit: \"${env.GIT_COMMIT_MSG}\"", submitter: 'admin'

                    // Analisa a Branch de Origem
                    switch (git_origin) {
                        // Caso a Branch de Origem seja main, será realizado as tarefas de acordo
                        case 'origin/main':
                            echo 'BRANCH MAIN DETECT!'
                            // Prepara arquivo de manifesto para deploy
                            sh 'sed -i "s/{{BRANCH}}/main/g" ./k8s/deploy.yaml'
                            sh 'sed -i "s/{{BRANCH}}/main/g" ./k8s/prod-IngressRoute.yaml'                            
                            sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deploy.yaml'
                            sh 'sed -i "s/{{PROJECT_NAME}}/$proj_name/g" ./k8s/deploy.yaml'
                            sh 'sed -i "s/{{PROJECT_NAME}}/$proj_name/g" ./k8s/prod-IngressRoute.yaml'
                            withCredentials([string(credentialsId: 'Registry-config-json', variable: 'DOCKER_CONFIG_JSON')]) {
                                sh 'sed -i "s/{{dockerconfigjson}}/$DOCKER_CONFIG_JSON/g" ./k8s/deploy.yaml'
                            }
                            // Realiza o deploy do arquivo de manifesto
                            withKubeConfig([credentialsId: 'kubeconfig']){
                                sh 'kubectl apply -f ./k8s/deploy.yaml'
                                sh 'kubectl apply -f ./k8s/prod-IngressRoute.yaml'  
                            }
                            break
                        // Caso a Branch de Origem seja homolog, será realizado as tarefas de acordo
                        case 'origin/homolog':
                            echo 'BRANCH homolog DETECT!'
                            // Prepara arquivo de manifesto para deploy
                            sh 'sed -i "s/{{BRANCH}}/homolog/g" ./k8s/deploy.yaml'
                            sh 'sed -i "s/{{BRANCH}}/homolog/g" ./k8s/homolog-IngressRoute.yaml'
                            sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deploy.yaml'
                            sh 'sed -i "s/{{PROJECT_NAME}}/$proj_name/g" ./k8s/deploy.yaml'
                            sh 'sed -i "s/{{PROJECT_NAME}}/$proj_name/g" ./k8s/homolog-IngressRoute.yaml'
                            withCredentials([string(credentialsId: 'Registry-config-json', variable: 'DOCKER_CONFIG_JSON')]) {
                                sh 'sed -i "s/{{dockerconfigjson}}/$DOCKER_CONFIG_JSON/g" ./k8s/deploy.yaml'
                            }
                            // Realiza o deploy do arquivo de manifesto
                            withKubeConfig([credentialsId: 'kubeconfig']){
                                sh 'kubectl apply -f ./k8s/deploy.yaml'
                                sh 'kubectl apply -f ./k8s/homolog-IngressRoute.yaml'
                            }
                            break
                        default:
                            echo 'NO ORIGIN BRANCH DETECT!'
                            exit 1
                    }
                }
            }
        }
        // FIM DO BLOCO DO ESTAGIO DE DEPLOY

    }
}