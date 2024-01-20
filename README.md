# Minha Primeira Pipeline Utilizando o Jenkins

<div style="display: flex; justify-content: space-between;">
<img src="/.assets/jenkins_logo_icon.png" alt="Jenkins" width="200px" height="200px">
<img src="/.assets/cert-manager-logo-icon.png" alt="Cert Manager" width="200px" height="200px">
<img src="/.assets/kubernetes_logo_icon.png" alt="Kubernetes" width="200px" height="200px">
<img src="/.assets/traefik_logo_icon.png" alt="Traefik" width="200px" height="100px">
</div>


### Caracteristicas:

1. Jenkinsfile é gerenciado pelo GitHub via CSM.
2. Configurado o [GitHub hook trigger for GITScm polling](https://plugins.jenkins.io/github/), proporcionando a execução de um Job via Webhook que é acionado após cada Push Event no GitHub.
3. Com intuito de simular um ambiente de produção e outro de homologação, dependendo da branch em que é gerado o push o Jenkins irá realizar uma ação compativel com o a branch em questão, contudo, isso está sendo feito dentro do Jenkinsfile atraves de Swith case pela função Script ao invez de utilizar os projetos do tipo Muilti-Branch(No futuro proximo estudo isso).
4. A diferença entre os ambientes é apenas o dominio, certificado SSL e a tag das docker images além dos nomes de alguns componentes dentro do Kubernetes como o Namespace.

### Stagios:

#### 1. Build

Atraves dos pluguins [Docker Pipeline](https://plugins.jenkins.io/docker-workflow/) e [Docker Plugin](https://plugins.jenkins.io/docker-plugin/), é feito o build de uma docker image, onde levará os arquivos da app.

> A Image receberá uma TAG que corresponda a branch e a fim de ter o minimo de versionamento é usado o ID da Build, assim ficando, registry.addr/project_name:branch-id_build.

#### 2. Push

Ainda pela a utilização dos pluguins mencionados anteriormente será feito um push da docker image criada do estagio anterior para um repositorio remoto.

> Essa image será enviado para um [Registry Privado](https://hub.docker.com/_/registry) que subi em meu Cluster Kubernetes.

#### 3. Deploy

Atravez dos pluguins [Kubernetes CLI]([https://plugins.jenkins.io/kubernetes-cli/) e [Kubernetes Plugin](https://plugins.jenkins.io/kubernetes/) será aplicado os arquivos de manifesto kubernetes com todos os componentes necessarios para que o projeto rode adequadamente em meu Cluster Kubernetes.

> - Componentes
>>1. Criação Namespace para o app com uma nomeclatura que diz a qual ambiantes pertece e o nome do app, branch-app.
>>2. Criação da secret que dará acesso ao Registry.
>>3. Criação/Modificação do Deployment.
>>4. Criação/Modificação da Service.
>>5. Criação/Modificação de um Middleware configurado para realizar o direcionamento de HTTP para HTTPS.
>>6. Criação/Modificação de um IngressRoute que dará acesso a service a partir Ingress Controller.

### A App:

Como o intuito desse projeto é o estudo de Pipelines no Jenkins a app foi criada com o intuito de simbolizar a entrega continua de algo mais complexo, logo, não tinha motivos para elaborar uma app mais complexa.

### Ha ação:

