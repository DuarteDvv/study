# **Data Distribution Shifts and Monitoring**

## **Causas de sistemas de falhas em Sistemas de ML**

Em geral nos preocupamos com 2 tipos principais de métricas no sistema:

- **Operacionais:** Mertricas relacionadas com latencia e vazao (troughtput) - O modelo esta respondendo ? em tempo razoavel ?

- **ML:** Metricas relacionadas a qualidade do que esta sendo respondido - O retorno do modelo esta correto ? Quao correto ?

Problemas gerados em **requisitos operacionais geralmente sao faceis de identificar** pois o sistema falha completamente e vem acompanhados de timeouts, mensagens de memoria insuficiente ou acessos errados na memoria. Identificar problemas **gerados nas metricas de ML sao mais complexos** pois exigem **monitoramento humano** constante no comportamento do modelo em producao para identificar possiveis **erros silenciosos** que nao sao refletidos nas metricas de qualidade. Existem 2 tipos de falhas:

### **Falhas de software**

- **Falhas de dependencias:** Acontece quando o codigo quebra pois alguma dependencia do codigo muda drasticamente, quebra ou deixa de ser mantido. Isso também pode ser generalizado para quando sua dependencia é o sistema de outras empresas como AWS pois se o servidor deles cair o seu também cai.
- **Falhas de deploy:** Quando versoes antigas de binarios sao colocados em producao ao inves da correta
- **Falhas de Harware:** Quando as CPUs, GPUs e etc falham por excesso de calor ou algo 

Lidar com esses problemas requer conhecimentos mais de engenharia de software que especificos de ML.

### **Falhas especificas de ML**

Por mais que a grande maioria dos problemas sao de software, os problemas de ML sao muito perigosos e dificeis de identificar e corrigir.

- **Dados na producao diferentes de dados no treino:** Quando dizemos que o modelo aprendeu com os dados de treino estamos dizendo que *ele aprendeu a distribuicao dos dados de treino o suficiente para gerar predicoes corretas em dados nao vistos no treino*. Geralmente usamos o conjunto de teste para representar esses dados nunca vistos que estarao em producao... o problema disso é que isso vem com **duas premissas importantes**:

    - **As distribuicoes sao iguais (Treino representa perfeitamente o real):** Quando temos teste e treino podemos garantir na hora de separar os conjuntos que eles sigam uma distribuicao proxima, levando a uma melhor generalizacao. Quando estamos em producao os dados vem de uma **distribuicao desconhecida/escondida (underlying) infinita e sem limitacoes**. Nos dados coletados para treino exisitem **diversos vieses (bias) de amostragem e selecao**, além de processamento e tempo humano ter sido gasto para gerar os dados de treino. Isso pode nos levar ao problema de **train-serving skew** (inclinacao/vies de treino-producao) em que o modelo vai bem em desenvolvimento mas mal em producao pois os dados divergem.

    - **A distribuicao é estacionária:** Mesmo que o dataset de treino seja perfeito e represente bem a distribuicao atual, a **distribuicao vai mudar com o tempo levando a degradacao do modelo aos poucos conforme o que ele aprendeu nao se aplica mais ao que existe**. Essas mudancas podem ser rapidas ou lentas e serem causadas por diversas razoes incluindo eventos no mundo ou o clima mudando. Um ponto importante aqui é **saber distinguir mudancas reais na distribuicao** ou nao apenas bugs/inconsistencias do sistema.

- **Casos de borda:** Casos de borda sao exemplos extremos e inusitados de input que levam o modelo a cometer erros muito grandes. É exatamente esses casos que tornam tao dificil a implementacao de modelos em areas de risco como medicina e carros autonomos mas nao apenas areas criticas pois um chatbot que nao lida bem com casos de borda por exemplo pode em algum momento ser extremamente racista e sexista. A ideia princial entao é sempre estudar e entender a cauda onde existem os casos raros que podem gerar problemas muito grandes.

    - **Outlier:** Um exemplo que difere muito dos outros
    - **Edge Case:** Um exemplo que performa significativamente pior que os outros (Um edge case nao necessáriamente é um outlier). Ex: Uma pessoa atravessando a rua sem olhar pode ser um outlier mas nao é um caso de borda pois o modelo vai identificar a pessoa e parar.

No treino podemos simplesmente remover os outliers para ajudar o modelo a generalizar melhor mas na inferencia real é necessário lidar de alguma forma com esses inputs.

- **Feedback Loop degenerado:** Também conhecido como vies de popularidade, bolhas de filtro e camaras de eco... o problema do loop degenerado é **quando a inferencia do modelo afeta no feedback do usuário que geralmente é usado para treinar novos modelos**. Imagine um sistema de recomendacao que diz que A é minimamente melhor que B para o usuário, o usuário entao clica mais em A pois esta em primeiro. Isso no futuro vira rotulo um novo modelo ser treinado e consequentemente A agora esta mais na frente ainda de B. Outro caso interessante é em selecao de curriculos, se treinamos um modelo para filtragem de curriculos de acordo com os funcionarios atualmente contratados, uma feature em comum X pode fazer com que mais funcionarios com X sejam contratados e depois novamente o modelo será treinado com mais funcionarios com feature X.

    - **Detectando o problema:** Em producao esse problema pode ser identificado conforme as **predicoes vao ficando muito homogeneas com tempo** mas queremos conseguir identificar esse problema antes de enviar o modelo para producao mas só temos o feedback do usuário em producao ? algumas abordagens:
        - **Métricas de diversidade:** Medimos a diversidade das predicoes do modelo para varios usuários usando métricas como **cobertura de cauda e a diversidade de itens** nas recomendacoes. Se a diversidade de itens é baixa significa que o modelo esta retonando as mesmas coisas as mesmas coisas para todo mundo, ou seja, as **predicoes sao muito homogeneas (sinal de loop)**.
        - **Desempenho em buckets:** Dividimos os itens em buckets de interacao, ou seja, **grupos de itens muito comuns e grupos de itens raros** (basicamente dividir a distribuicao em pedacos) e medimos o desempenho do modelo em cada bucket. Se o desempenho dele é melhor em itens comuns entao ele provavelmente esta enviesado.

    - **Mitigando o Problema:** existem duas abordagens principais de mitigacao:

        - **Randomizacao:** A ideia aqui é mitigar o vies **colocando itens aleatorios nas recomendacoes para conseguir feedback nao viesado** (Tiktok faz isso colocando videos aleatorios para certos usuários e pegando o feedback deles antes de colocar o video para mais pessoas). O problema dessa abordagem é que é um trade-off pois custa experiencia do usuário e existem alternativas melhores como contextual bandits. 

        - **Posicional Features:** Como nosso problema é a probabilidade do clique ser afetada pela posicao, podemos **dar ao modelo essa informacao de posicao através de features como um booleano de 'primeiro-colocado'** e agora durante o treinamento o primeiro colocado vai ter um pesinho a mais por causa da flag = 1, mas durante a inferencia todas as flags estarao como 0 e isso ira balancear o modelo como se estivessemos colocando um pouco do vies naquela flag e depois tiramos. Outra abordagem melhor é **treinar dois modelos**, um modelo que preve a probabilidade do usuário ver e considerar o item dado a posicao dele (usando algum proxy como aparecer na tela) e o segundo modelo preve a probabilidade do usuário clicar no item sem levar em consideracao a posicao. Assim **separamos o efeito qualidade do item e o efeito de vies de popularidade**.

## **Data Distribution Shifts**

Consiste no fenomeno em que a distribuicao target (inferencia/real) muda com o tempo fazendo com que a distribuicao source (treino) fique desatualizada.

### **Tipos de Data Shifts**

Em aprendizado supervisionado podemos dizer que nossos dados de treino seguem uma distribuicao **P(X,Y)** que é a distribuicao de probabilidade das features X e o rotulo Y aparecerem juntos. Os modelos em ML tentam se aproximar da distribuicao **P(Y|X)**, probabilidade do rotulo dado as features. A probabilidade da sample pode ser decomposta de duas formas: **P(X,Y) = P(Y|X)P(X) = P(X|Y)P(Y).**

- **Covariate Shift:** A distribuicao de rotulos dado um exemplo (P(Y|X)) nao mudou mas a distribuicao de exemplos P(X) mudou. **Um exemplo disso é fazer oversampling ou coletar mais amostras (vies) de uma doenca rara, a regra de ter a doenca (P(Y|X)) nao mudou mas agora a quantidade de amostras com a doenca P(X) mudou**.

- **Label Shift:** A distribuicao dos dados dado um rotulo especifico (P(X|Y)) nao muda mas a probabilidade de P(Y) muda. Por exemplo a quantidade de **churn (P(Y)) aumentou na empresa mas o padrao de clientes que churnam e nao churnam continua o mesmo (P(X|Y))**.

- **Concept Shift:** A distribuicao dos dados P(X) nao muda mas a probabilidade do rotulo dado esse X (P(Y|X)) muda drasticamente. Acontece quando efeitos externos efetam o rotulo sem afetar os dados, por exemplo a **valorizacao de um bairro afeta os precos (Y) das casas sem que a casa (X) mude**.

### **General Data Distribution Shifts**