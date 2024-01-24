# Minha Primeira Pipeline Utilizando o Jenkins

<div style="display: flex; justify-content: center;">
<img src="/assets/jenkins_logo_icon.png" alt="Jenkins" width="135px" height="135px">
<img src="/assets/cert-manager-logo-icon.png" alt="Cert Manager" width="135px" height="135px">
<img src="/assets/kubernetes_logo_icon.png" alt="Kubernetes" width="135px" height="135px">
<img src="/assets/traefik_logo_icon.png" alt="Traefik" width="135px" height="135px">
<img src="/assets/oci_logo_ico.png" alt="OCI" width="235px" height="135px">
</div>

Esse repositório foi feito com o intuito de demonstrar meus estudos envolvendo Pipelines Jenkins e Kubernetes.

### Características:

1. Jenkinsfile é gerenciado pelo GitHub via [CSM](https://plugins.jenkins.io/github/).
2. [GitHub hook trigger for GITScm polling](https://plugins.jenkins.io/github/) ativo, assim proporcionando a execução automatica de um Job via Webhook que é acionado após cada Push Event no GitHub.
3. Com intuito de simular um ambiente de produção e outro de homologação, dependendo da branch em que é gerado o Push o Jenkins irá realizar uma ação compatível com o a Branch em questão, contudo, isso não está sendo feito por um projeto do tipo Multi-Branch(No futuro próximo estudo isso).
4. A diferença entre os ambientes é simbólica, apenas o domínio, certificado SSL, as TAGs das Docker Images, além dos nomes de alguns componentes dentro do Kubernetes como o Namespace, pois, o intuito é apenas simulador.
5. Antes da execução da etapa de Deploy terá uma etapa de aprovação,  onde será informado a Branch de origem e a descrição do Commit. 
6. Todos os recursos e ferramentas aqui utilizados estão hospedados na OCI pela utilização dos recursos Always-Free.

### Estágios da Pipeline:

#### 1. Build

Atraves dos pluguins [Docker Pipeline](https://plugins.jenkins.io/docker-workflow/) e [Docker Plugin](https://plugins.jenkins.io/docker-plugin/), é feito o build de uma docker Image, onde levará os arquivos da aplicação.

> A Image receberá uma TAG que corresponda a branch, a fim de simular/representar um versionamento é usado o ID da Build nas tags, assim ficando, registry.addr/project_name:branch_name-id_build.

#### 2. Push

Ainda pela utilização dos plugins mencionados anteriormente será feito um PUSH da Image Buildada no estágio anterior para um repositório remoto.

> O Repositorio Remoto se trata de um [Registry Privado](https://hub.docker.com/_/registry) que subi em meu Cluster Kubernetes.

#### 3. Deploy

Atravez dos pluguins [Kubernetes CLI]([https://plugins.jenkins.io/kubernetes-cli/) e [Kubernetes Plugin](https://plugins.jenkins.io/kubernetes/) será aplicado os arquivos de manifesto kubernetes com todos os recursos necessários para que o projeto rode adequadamente em meu Cluster Kubernetes.

- Recursos:

1. Namespace, com uma nomenclatura que ajuda a saber qual dos ambientes de Prod ou Homolog e a qual aplicação pertence o recurso.
2. Secret, dará acesso ao kubernetes realizar Pull de Images do Registry Privado.
3. Deployment.
4. Service.
5. Horizontal Pod Autoscaler.
6. Middleware, Configurado para realizar o redirecionamento de HTTP para HTTPS.
7. IngressRoute, Dará acesso a Service através do Ingress Controller.

Com todos esses recursos aplicados a aplicação de demonstração estará disponivel a partir [daqui](https://madlabs.com.br/potato) em um ambiente Kuberentes hospedado da OCI, contato com o Traefik como Ingress Controller a fim de realizar o balanceamento das requisições, HTTPS via Let's Encrypt, regra de Rolling Update a fim de evitar indisponibilidades quando a Image for atualizada e um HPA configurado com a finalidade de estudar a escalabilidade Horizontal no Kubernetes


### A Web App:

Como o intuito deste projeto é o estudo de Pipelines e Jenkins a App abordada neste projeto foi criada apenas com o intuito de simbolizar uma entrega contínua de uma aplicação Web, logo, é uma simples página criada a partir em HTML.

### Demonstração:

https://github.com/JohnnyPaixaum/potato-pipeline/assets/34694531/4ed37b9a-e780-4577-af2b-73360c9b018d

- Descrevendo passos realizados:

1. Editado o arquivo da Aplicação.
2. Realizado o git commit e push.
3. Demonstrando o início automático do job no Jenkins.
4. Demonstrando a aprovação para início da etapa de Deploy, contendo na mensagem a Branch de origem e a descrição do Commit.
5. Demonstrado criação dos Pods com a nova versão subindo e substituindo os atuais.
6. Demonstrando mudança da aplicação no navegador após todo o processo realizado pela Pipeline.
