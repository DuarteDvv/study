## Framework para ML system design

Esclarecer requisitos ---> Modelar o problema em uma tarefa de ML ---> Preparação dos dados ---> Desenvolvimento do modelo ---> 
Avaliação ---> Desenvolvimento e Deployment ---> Monitoramento e Infraestrutura 


#### Esclarecer requisitos

Aqui definimos os requisitos do sistemas em:

- objetivo de negócio: Como a empresa espera que o sistema afete o negocio da empresa ? Afetar as vendas ? O consumo diário ? Lucro ? 
- Features que o sistema precisa suportar: Que tipo de coisas o sistema que estamos construindo precisa suportar ? Likes ? Comentários ?
- Fontes de dados: Quais as fontes de dados que vamos usar ? quantidade ? estruturada ? rotulada ? 
- Restrições: Alguma restrição de latência ? de hardware ? Cloud ou local ? Predições tem que ser rapidas ? precisão ou tempo ?
- Escalabilidade: Quantos usuários vamos ter ? items ? 

#### Modelar o problema em uma tarefa de ML

Aqui definimos o objetivo de ML (objetivo que o sistema pode maximizar) através do objetivo de negócio (business), ou seja, a métrica que queremos maximizar na empresa através de machine learning (como taxa de cliques por exemplo)... depois definimos a entrada e saida do sistema (vai entrar uma imagem e sair uma probabilidade ?)... depois com base na entrada e saida escolhemos a categoria de problema que estamos lidando (Classificação Multiclasse ? Regressão ?).

#### Preparação dos dados

Agora precisamos preparar e garantir inputs de qualidade para nosso sistema:

- Engenharia de dados: Definimos e estudamos as fontes dos dados, definimos nosso modelo de dados / tipo de banco de dados (relacional, documentos...), entendemos e exploramos nossos tipos de dados e definimos nosso ETL (ou ELT), ou seja, quais nossas fontes, transformações necessárias e onde usar.

- Engenharia de Features: Aqui usamos conhecimento especifico do dominio para extrair features de dados brutos e nessas features fazer transformações para torna-las usaveis por modelos... lidar com dados faltantes e incomuns ... escalar as features conforme necessário e fazer encoding de categoricas.

#### Desenvolvimento do modelo

Aqui começamos o desenvolvimento iterativo do modelo mas primeiro é necessário escolher qual modelo/arquitetura utilizar no problema: O processo tipico é escolher um baseline simples que não necessáriamente precisa ser um modelo. Depois fazer experimentos com modelos simples e que treinam rapido como bayes... depois começar a utilizar modelos mais complexos e se necessário melhorar as previções ensemble é uma otima escolha. No momento da escolha é importante entender diferentes aspectos dos modelos como quantidade de dados que ele precisa, velocidade de treino, numero de hyperparametros, possibilidade de continual learning, requisitos de hardware e interpretabilidade. Não existe almoço gratis (theres no free lunch) ...

Depois de escolhido precisamos treinar esse modelo mas para isso precisamos de dados (datasets), um função de loss, decidir entre fine-tuning x treinar desde o inicio (from scratch) e se é necessário treinar o modelo de forma distribuida. Para construir o dataset geralmente existem 5 etapas: Coleta de dados brutos, seleção de features, escolha de metodo de amostragem (se a quantidade de dados for grande), divisão dos dados (treino-*teste) e lidar com problemas nos dados como desbalanceamento de classes... Agora com os dados temos que escolher a loss function que se aplica ao problema (geralmente a loss function esta ligada ao algoritmo também), depois da loss decidir entre finetuning e treino do zero também depende de recursos e do problema... enquanto treino distribuido dependo do tamanho do modelo escolhido ou dos dados.