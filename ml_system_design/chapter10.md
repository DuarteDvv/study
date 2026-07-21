# **Infrastructure and Tooling for MLOps**

![alt text](images/need_infra.png)

Infraestrutura é o **conjunto de instalacoes fundamentais que apoiam no desenvolvimento e manutencao de sistemas de ML**. O que é fundamental varia de empresa para empresa mas o que pode funcionar para maioria das empresas (generalizado) pode ser categorizado em 4 camadas: 

![alt text](images/fourlayers.png)

## **Camadas de armazenamento e computacao**

- **Armazenamento:** Sistemas de ML geralmente usam muitos dados e esses dados precisam ser armazenados em algum lugar. Eles podem ser armazenados localmente usando HDs e SSDs nos servidores da empresa ou armazenados em nuvém através de servicos como Amazon S3 ou Snowflake. 
    - **Amazon S3 (Simple Storage Service):** serviço de armazenamento de objetos da AWS. Cada objeto tem uma chave única (path/nome), os dados em si, e metadados... nao é um banco de dados tipo SQL/NoSQL é tipo um HD infinito e distribuido na nuvem, onde você guarda qualquer tipo de arquivo.

    - **Snowflake:** É um data warehouse (armazém de dados) na nuvem, focado em análise de dados estruturados.Você carrega dados estruturados (tabelas) para dentro do Snowflake. Ele separa armazenamento e processamento (compute) — isso permite escalar cada um independentemente (Pode ingerir diretamente de arquivos que estão no S3)

    é comum usar os dois juntos: os dados brutos ficam no S3 (barato, escalável), e o Snowflake lê/carrega esses dados para análises rápidas via SQL

- **Computacao:** Conjunto de recursos computacionais como CPUs e GPUs que executam os jobs/trabalhos pesados do sistema, como por exemplo treino, limpeza e inferencia. A camada de computacao pode ser dividida em unidades menores como threads, instancias de maquinas virtuais, jobs ou pods (kubernets). Para um job ser executado ele precisa:

    - **carregar os dados necessários** na memória da unidade de computacao e se nao houver memória suficiente utilizar algoritmos out-of-core
    - **executar as operacoes** do job (multiplicacoes, adicoes...)

    A unidade de computacao geralmente é caracterizada por 2 métricas principais:
    - **Memória:** Mais memória permite *carregar mais dados, usar modelos maiores* e memórias mais rapidas permite uma *largura de banda de I/O maior*
    - **FLOPS:** Operacoes de ponto flutuante realizadas por segundo que representa *quanto sua unidade de computacao consegue trabalhar*. Normalmente nao é muito confiavel devido a *falta de padronizacao da definicao e que o maximo raramento consegue ser usado*.

    A utilizacao (FLOPS usados / FLOPS maximos) é quase impossivel de ser 100% e é *limitado drasticamente pela velocidade de carregamento dos dados (largura de banda da memoria (limite maximo fisico daquela tecnologia de memória))*. Já que FLOPS é complicado é mais comum usarem memória e numero de nucleos como métricas... a AWS por exemplo usa o conceito de vCPUs que na prática equivale a meia CPU.

### **Public Cloud Versus Private Data Centers**

Com crescimento da computacao em nuvem se tornou muito comum empresas pequenas/medias pagarem provedores de nuvem como AWS e Azure pelo uso de suas maquinas. Isso faz todo sentido para empresas crescendo agora *uma vez que nao precisam se preocupar em montar seus proprios datacenters*. Cloud é especialmente util pela *possibilidade de pagar pelo que usa ao invés de sempre pagar uma quantia fixa*... imagine que seu sistema consegue lidar 90% do tempo com 10 CPUs e 10% precisa de 100 CPUs por causa de picos, se quisermos aguentar os picos com datacenters proprios teriamos que manter 100 CPUs sempre mas com cloud podemos ficar 90% do tempo com 10 CPUs e 10% do tempo com 100 CPUs e pagar pelo uso e *muitas vezes nem é necessário fazer isso manualmente pois existe autoscaling*.

Cloud parece escalar infinitamente mas tem limites claros impostos normalmente para cada usuário. Além disso, o preco da cloud aumenta constantemente e conforme a empresa cresce os gastos com cloud podem chegar em ate 50% dos gastos totais da empresa. *Comecar a usar a cloud dos provedores é muito rapido e facil mas parar é complicado devido as dependencias criadas e o custo de equipamentos e engenheiros*. Existem duas abordagens principais para sair da nuvem:

- **Hibrido:** Usar cloud junto com infraestrutura propria e ir aumentando a capacidade da infra da empresa de pouco em pouco.
- **Multicloud:** As vezes por imprevistos como compra de empresas e estrategias uma empresa acaba com mais de um provedor de cloud... isso também pode reduzir a dependencia em apenas um provedor.

## **Ambiente de desenvolvimento**

O ambiente de desenvolvimento é extamente onde os desenvolvedores trabalham, se esse lugar for improvisado ou pouco padronizado muito *tempo será perdido com trivialidades e problemas de compatibilidade*. Os principais componentes de um ambiente de desenvolvimento sao:
- **Ferramentas de versionamento:** *Git* para codigo, *DVC* para dados e *Wandb/MLflow* para exprimentos e artefatos
- **CI/CD:** Suite de testes automatizados através de *github actions*

IDEs e Notebooks sao outros dois componentes muito comuns para escrita de codigo e analises de dados... notebooks sao especialmente interessantes por *manterem o estados da execucao das celulas entao se a etapa 1 é pesada e o codigo falhou na etapa 2 apenas a 2 precisa ser reexecutada*. Papermil é uma extensao interessante de notebooks que permite parametrizar executacao dos notebooks e executar uma instancia com parametros diferentes em paralelo.

### **Padronizacao do ambiente de desenvolvimento**

Nao padronizar um ambiente deixa aberto para diversos problemas acontecerem:
- *pacotes/bibliotecas* com versoes diferentes 
- versoes diferentes da *linguagem* sendo usadas
- *hardwares novos ou muito antigos* que nao os softwares nao tem compatibilidade

Todos esses problemas convergem para levar o dev environment para instancias na nuvem em que usamos *IDEs diretamente na nuvem ou IDEs locais conectadas a maquinas na nuvem através de SSH*. Pois isso facilita o trabalho remoto, aumenta a seguranca e reduz o gap entre entre dev e production.

### **Containers**

Em desenvolvimento trabalhamos em uma maquina fixa entao uma vez que configurada para executar a aplicacao nao precisa mais. Agora em producao instancias de *servicos sao alocadas dinamicamente pelo autoscaling de acordo com a necessidade e cada nova instancia precisa ser configurada do zero sempre e ser exatamete igual as outras*. Quem resolve isso é o conceito de Containers usandos em ferramentas como Docker e Podman, conceitos importantes sobre:

- **Dockerfile:** É uma receita do zero de como criar seu ambiente de producao passo a passo com todas as etapas

![alt text](images/dockerfile.png)
- **Image Docker:** É o resultado do Dockerfile construido
- **Containers:** Instancias funcionando da imagem criada apartir da Dockerfile
- **Container Registry:** Imagens podem ser construidas do zero com dockerfile ou reutilizadas do registro do docker que armazena imagens otimizadas disponibilizadas por empresas.

Geralmente **varios containers sao executados simultaneamente para uma mesma aplicacao** pois eles permitem que:
- *Dependencias conflitantes* funcionem isoladamente
- *Permite executar containers diferentes em maquinas de diferentes custos* ...se uma etapa do pipeline como features precisa de muita memória e roda rapido e uma etapa de treino precisa de muita GPU e demora. Sabendo que maquinas com GPU e muita RAM sao extremamente mais caras podemos executar a primeira etapa em uma maquina mais barata com memória e a segunda em uma apenas com o necessário de GPU.

Quando lidamos com mais de uma container fica muito complexo lidar com isso manualmente e por isso existem ferramentas de **orquestracao de containers:**
- **Docker compose:** Orquestrador leve que gerencia containers em uma unica maquina 
- **Kubernetes(K8s):** Quando queremos containers em multiplas maquinas o Kube cuida disso criando uma rede entre elas para comunicacao e escalona automaticamente para manter a disponibilidade do sistema.

## **Gerenciamento de recursos**

