# Minha Primeira Pipeline Utilizando o Jenkins

<div style="display: flex; justify-content: space-between;">
<img src="/assets/jenkins_logo_icon.png" alt="Jenkins" width="200px" height="200px">
<img src="/assets/cert-manager-logo-icon.png" alt="Cert Manager" width="200px" height="200px">
<img src="/assets/kubernetes_logo_icon.png" alt="Kubernetes" width="200px" height="200px">
<img src="/assets/traefik_logo_icon.png" alt="Traefik" width="200px" height="200px">
</div>

Esse projeto foi com o intuito de demonstrar meus estudos envolvendo Pipelines e Jenkins.

### Caracteristicas:

1. Jenkinsfile é gerenciado pelo GitHub via [CSM](https://plugins.jenkins.io/github/).
2. [GitHub hook trigger for GITScm polling](https://plugins.jenkins.io/github/) ativo, assim proporcionando a execução automatica de um Job via Webhook que é acionado após cada Push Event no GitHub.
3. Com intuito de simular um ambiente de produção e outro de homologação, dependendo da branch em que é gerado o Push o Jenkins irá realizar uma ação compativel com o a Branch em questão, contudo, isso não está sendo feito por um projeto do tipo Muilti-Branch(No futuro proximo estudo isso).
4. A diferença entre os ambientes é simbolico, apenas o dominio, certificado SSL, as TAGs das Docker Images, além dos nomes de alguns componentes dentro do Kubernetes como o Namespace, pois, o intuito é apenas simulador.
5. Tanto nas etapas que representa o ambiente de Produção quanto nas de Homologação, antes de realização do Deploy terá uma etapa de aprovação.
6. Todos os recursos e ferramentas aqui utilizados estão hosperados na OCI pela utilização dos recursos Awayls-Free.

### Stagios da Pipeline:

#### 1. Build

Atraves dos pluguins [Docker Pipeline](https://plugins.jenkins.io/docker-workflow/) e [Docker Plugin](https://plugins.jenkins.io/docker-plugin/), é feito o build de uma docker image, onde levará os arquivos da aplicação.

> A Image receberá uma TAG que corresporá a branch, a fim de simular/representar um versionamento é usado o ID da Build nas tags, assim ficando, registry.addr/project_name:branch_name-id_build.

#### 2. Push

Ainda pela a utilização dos pluguins mencionados anteriormente será feito um PUSH da Image Buildada no estagio anterior para um repositorio remoto.

> O Repositorio Remoto se trata de um [Registry Privado](https://hub.docker.com/_/registry) que subi em meu Cluster Kubernetes.

#### 3. Deploy

Atravez dos pluguins [Kubernetes CLI]([https://plugins.jenkins.io/kubernetes-cli/) e [Kubernetes Plugin](https://plugins.jenkins.io/kubernetes/) será aplicado os arquivos de manifesto kubernetes com todos os recursos necessarios para que o projeto rode adequadamente em meu Cluster Kubernetes.

- Recursos:

1. Namespace, com uma nomenclatura que ajuda a saber a qual dos ambientes de Prod ou Homolog e a qual aplicação pertence o recurso.
2. Secret, dará acesso ao kubernetes realizar Pull de images do Registry Privado.
3. Deployment.
4. Service.
5. Horizontal Pod Autoscaler.
6. Middleware, Configurado para realizar o direcionamento de HTTP para HTTPS.
7. IngressRoute, Dará acesso a service atravez do Ingress Controller.

Com todos esses recursos aplicados a aplicação de demonstração estará disponivel a partir [daqui](https://madlabs.com.br/potato) em um ambiente Kuberentes hospedado da OCI, contato com o Traefik como IngressController a fim de realizar o balanceamento das requicições, HTTPS via LetsEncrypt, regra de RollingUpdate a fim de evitar indisponibilidades quando a Image for atualizada e um HPA configurado com a finalidade de estudar a escalabilidade Horizontal no Kubernetes


### A App:

Como o intuito desse projeto é o estudo de Pipelines e Jenkins a App abordada nesse projeto foi criada apenas com o intuito de simbolizar uma entrega continua de uma aplicação Web, logo, é uma simples pagina criada a partir em HTML.

### Demonstração:

https://github.com/JohnnyPaixaum/potato-pipeline/assets/pipeline_demo.mp4

- Descrevendo passos realizados:

1. Editado o arquivo da Aplicação.
2. Realizado o git commit e push.
3. Demonstrado o inicio automatico do job no Jenkins.
4. Demonstrado a aprovação para incio da etapa de Deploy, contendo na menssagem a Branch de origem e a descrição do Commit.
5. Demonstrado criação dos Pods com a nova versão subindo e substituindo os atuais.
6. Mostrado mudança da aplicação no navegador após todo o processo realizado pela Pipeline.