# **Desenvolvimento do modelo e avaliacao Offline**

## **Escolhendo um modelo de ML** 

A escolha do modelo ideal não viabiliza testar todas as arquiteturas possíveis. Ela é um equilíbrio guiado por restrições técnicas, de negócio e de dados.

- **Dados:** Poucos dados exigem modelos estatísticos simples (ex: SVM); volumes massivos justificam Redes Neurais.

- **Latência e Recursos:** Modelos gigantes exigem hardware caro. Se a inferência deve ser rápida e barata, evite-os.

- **Requisitos de Negócio:** Se o problema exige explicabilidade, evite caixas-pretas. Se exige aprendizado incremental, use modelos compatíveis com Online Learning.

Dicas para escolha de modelo:

- **Comece pelo Simples (Baseline):** Modelos simples são rápidos de treinar, fáceis de interpretar e servem para validar o pipeline de dados de ponta a ponta antes de adicionar complexidade.

- **Cuidado com Estado da Arte (SOTA):** Modelos SOTA acadêmicos buscam bater benchmarks estáticos sem focar em custo, infraestrutura ou dados mutáveis de produção. Raras vezes são viáveis para o mundo real logo de início.

- **Viés de Novidade:** Evite o impulso da equipe de usar a arquitetura "do momento". Um modelo ter batido um benchmark específico não garante que ele funcionará no cenário particular da sua empresa.

- **O Modelo Ideal Muda com o Tempo:** Avalie a Learning Curve (Loss vs. Volume de Dados). Modelos simples ganham com poucos dados, mas Redes Neurais escalam melhor conforme o volume cresce.

- **Custos:** Escolha o modelo pensando no impacto do erro de negócio (ex: quando um Falso Negativo custa mais caro que um Falso Positivo) e no trade-off Custo x Performance.

- **Suposições do Modelo:** Todo algoritmo assume premissas sobre os dados (linearidade, independência, distribuições). Garanta que seus dados não quebrem essas regras.

## **Ensemble**

Ensemble de modelos consiste no uso de mais de varios modelos diferentes (ou o mesmo treinado em dados diferentes) para fazer uma unica previsão... se esses modelos forem independentes a acurácia é matematicamente aumentada, podemos verificar isso calculando as probabilidades de erro dos 3 juntos ou 2 deles juntos e depois olhar o complemento. Ensemble é muito usado em competições mas pode ser complexo usar em produção devido ao custo e tempo de inferencia... a não ser que uma previsão certa tenha um ROI muito alto. Existem 3 tipos principais de ensemble:

- **Bagging (Bootstrap bagging):** Aqui nos temos varios base learners (modelos) que não serão treinados nos mesmos dados... a ideia principal é que o dataset de treino de cada um desses base learners é gerado a partir de bootstraping (com reposição, ou seja, tratamos a amostra como uma população e amostramos dela supondo que ela seja representativa da população real) do dataset original. Assim teremos diversos modelos treinados em amostragens diferentes dos dados pegamos a saida deles e fazemos uma media/contagem... algumas vão ter amostragens melhores, outras piores e outras que tratam problemas ate de desbalanceamento (oversampling intriseco). Em geral, o metodo torna mais estavel metodos muito sensiveis aos dados como redes neurais... random forest é um bom exemplo de bagging pois ela treina diversas arvores de decisão com subsets aleatórios do total de features (ao inves de linhas o aleatório sao as colunas).

![alt text](images/bagging.png)

- **Boosting:** Aqui é um pouco diferente pois queremos treinar novos modelos do conjunto que sejam bons no que o ensemble atual não é bom ... começamos com um modelo simples (Nosso ensemble tem apenas um modelo no começo) e fazemos as previsões. Nos exemplos de treino que o modelo errar nos colocaremos um peso maior de loss e novo modelo do ensemble é treinado com os dados com pesos atualizados... consequentemente ele será melhor nos casos que foram errados e estavam com maior peso... depois fazemos as predições novamente com o ensemble (agora com 2 modelos) e repetimos o processo de dar maior peso para os erros nas iterações seguintes.Bons exemplos de boosting são as XGBoost e o LightGBM que usa aprendizado distribuido e portanto é mais rapido para grandes volumes de dados.

![alt text](images/boosting.png)

- **Stacking:** Esse é o mais simples em que treinamos varios modelos com o mesmo conjunto de dados, pegamos as predições deles e usamos como features em um modelo final que pode ser simples como simplesmente contar elas ou pode ser complexo como uma regressão linear e aprender padrões de quando dar mais peso para cada modelo base. 

![alt text](images/stacking.png)

## **Monitoramento (experiment tracking) e Versionamento**

È comum fazer experimentos com novas arquiteturas, hyperparametros e dados para testar teorias e encontrar novos resultados que podem ser melhores... cada experimento tem sua curva de loss, curva de learning, logs, arquivos de saida (como modelos torch .pt) e diversos outros artefatos. È importante manter todas as definições e parametros de um experimento para ser possivel recria-los e além disso salvar os outputs/resultados para comparar com outros experimentos. O processo de monitorar o progresso e resultados de um experimento é chamado de **experiment tracking** e o processo de criar logs de todos os detalhes do experimento com objetivo de recriar (Reprodutibilidade) o experimento ou compara-lo é chamado de **versionamento**. Existem diversas ferramentas como MLflow e Weights and bias que originalmente serviam apenas para monitoramento mas depois incluiram versionamento.

### **Experiment Tracking**

Monitorar o modelo durante o treinamento pode ser uma grande parte do processo de treino e é extremamente importanto já que várias coisas podem dar errado durante o treinamento... alguns itens importantes a se monitorar:

- **loss curve da validação e treino:** pode dar indicios de overfitting com treino descendo e validação subindo, underfitting com treino e validação descendo ou ate mesmo alguma estagnação na loss.

- **métricas de performace:** plotar métricas como F1, recall, precision, perplexity pode mostrar caracteristicas como por exemplo um recall baixo significa que a quantidade de falsos negativos esta muito alta.

- **inferencias de teste:** durante o treinamento a cada epoca ou um numero de epocas salvar log do input, predição e rotulo para analisar no futuro se a loss esta coerente com as predições... o modelo pode encontrar atalhos para roubar na loss e a saida não faz sentido algum.

- **velocidade do modelo:** quão rapido o modelo processa as epocas (epocas/segundo), ou seja, quão rapido vai ser o treinamento... isso é importante pois temos uma estimativa de gasto e tempo de treinamento.

- **métricas de hardware:** uso de memória, CPU/GPU ... pode mostrar subutilização (gargalos) do hardware, estouro de memória ou uso exessivo do hardware.

- **parametros e hyperparametros:** monitorar learning rate (quando não é fixo) e norma do vetor gradiente (tamanho do vetor)... pode ser util para verificar se o gradiente não esta crescendo muito (infinito) ou reduzindo muito.

Esses itens (estado atual do treino e modelo) nos **permite comparar experimentos**, entender melhor nosso modelo e encontrar problemas de forma mais facil (debug)... pode ajudar a entender o efeito de pequenas alterações na performace do modelo. Idealmente deveriamos monitorar tudo porém é um esforço cognitivo muito grande que pode nos distrair do que for importante e além disso pode ser pesado para as ferramentas disponiveis atualmente.

### **Versionamento**

Modificar o código ou hiperparâmetros de um experimento promissor e piorar o resultado é comum. Sem versionamento, recuperar o estado anterior que funcionava gera desperdício de tempo. Contudo, enquanto o versionamento de **código** via Git já é padrão, o ecossistema de ML exige versionar também **dados e modelos**.

Por que não usar Git para Dados e Modelos?

- **Volume (Tamanho):** Arquivos de código ocupam kilobytes; datasets e pesos de redes neurais ocupam gigabytes ou terabytes. O Git guarda o histórico completo de modificações localmente, o que tornaria um git clone ou o armazenamento em nuvem inviáveis por falta de espaço em disco.

- **Operações Indefinidas:** No código, usamos git diff e git merge para comparar linhas de texto e resolver conflitos. Não existe um equivalente trivial de diff ou merge textual para tabelas de bilhões de linhas, imagens ou arquivos binários de pesos de modelos (.pth, .pkl).

Para garantir que um experimento seja replicável, ferramentas modernas (Git, DVC e MLflow / Weights & Biases) trabalham juntos:

- **Git:** Versiona Codigo
- **DVC:** Versiona Dados
- **MLflow/Wandb:** Versiona Modelos e faz tracking de experimentos

Versionamento e monitoriamento ajudam muito com a reprodutibilidade mas **não garantem graças ao não determinismo** que alguns métodos possuem e também o hardware... uma mesma semente em computadores diferentes não necessáriamente tera resultados iguais tornando a reprodutibilidade 100% igual impossivel sem saber todos os detalhes do ambiente. Além disso, ao invés de executar infinitos experimentos é importante **entender a teorica e premissas de cada cenário** para reduzir ao maximo o numero de experimentos com conhecimento previo.

### **Debugging ML Models**

Debugar um modelo de ML é desafiador principalmente por 3 motivos: 

- **Bug/Falha silenciosa:** o pipeline inteiro de inferencia pode estar funcionando mas o valor da inferencia estar errado... isso é silencioso tanto para os desenvolvedores como para os clientes que assumem que o resultado esta correto.
- **Validação lenta:** mesmo descobrindo o problema, para resolver é possivel que o modelo terá que ser treinado do zero novamente e talvez também feito deploy... teriamos que esperar todo esse tempo para saber se o bug foi corrigido.
- **Muitos componentes:** o pipeline inteiro é formado por diversos componentes como arquitetura, dados e software... nem sempre a mesma equipe é responsavel por todo e portanto é necessário algum tipo de comunicação entre equipes.

Um modelo pode falhar principalmente por:

- **Premissas erradas:** Cada modelo segue algumas premissas sobre os dados e problema... se elas não são respeitadas é improvavel que o resultado seja bom. Um exemplo disso é usar regressão linear em dados não lineares.

- **Implementação errada:** Erros de implementação em redes neurais em que mexemos manualmente na arquitetura da rede

- **Hyperparametros ou features ruins:** A escolha errada de hyperparametros pode levar o modelo a divergir e de features a ter um overfitting/underfitting

- **Problema nos dados:** Varios problemas podem ser causados pelos dados como data shift, ruido nos dados ou rotulos incorretos.

Não existe formula mágica de como de como se debugar mas algumas recomendações de pessoas experientes... eis 3 delas especiais para redes neurais: 

- **Começar simples e aumentar os componentes:** Começar com redes/modelos pequenos e ir aumentando gradativamente e verificando como isso afeta a performace.

- **Ajustar (fit) em um batch:** Antes de treinar com todos os dados -> pegar um batch pequeno e ajustar o modelo nele e avaliar em cima do mesmo batch, o esperado é que a métrica de qualidade seja total ou muito alta pois o treino e validação são nos mesmos dados. Isso é um teste de sanidade.

- **setar random seed:** Existe muitas partes que possuem aleatoriedade nesses modelos... setar uma seed evita que a diferença de um experimento tenha sido causada exclusivamente pela aleatoriedade.

- **Mais aqui:** https://karpathy.github.io/2019/04/25/recipe/

## **Treino distribuido**

Conforme os modelos vao crescendo em numero de parametros, eles ficam mais dificeis de armazenar e treinar, consequentemente as empresas comecam a se preocupar mais com a escalabilidade desses modelos. É comum ter que treinar modelos com **dados que nem cabem em uma maquina**, isso pode levar a problemas como:

- **Pre-processamento:** processamentos como normalizacao precisam ser out-of-core (consegue ser feito carregando pedacos dos dados em memoria)
- **Batchs pequenos:** se só conseguimos carregar algumas linhas (samples) desses dados em memória o tamanho do batch de treinamento terá que ser menor. Isso faz com que o **treinamento seja mais instavel** (poucas amostras por atualizacao) e que o **hardware de treino seja subutilizado**.
- **Casos extremos:** Em casos extremos em que um modelo neural com seus parametros junto com os dados e as ativacoes intermediarias do treino nao cabem em memória estratégias especificamos sao necessárias como:
    - **gradient checkpointing:** ao invés de armazenar todas as ativacoes (saidas de uma camada depois da funcao de ativacao) de todas as camadas na memória, armazenamos apenas algumas camadas específicas (checkpoints) e deletamos o restante. Quando precisamos de uma ativação descartada durante o passo de volta (Backward Pass), usamos o último checkpoint salvo para recalcular rapidamente aquele trecho do Forward Pass sob demanda. Isso é um **tradeoff de memória e tempo de computacao** porém ao liberar memoria o tamanho do batch pode ser aumentado e reduzir o tempo de computacao ainda mais.

A escalabilidade de um modelo envolve tanto os dados (usados no treino) quanto os parametros (tamanho do modelo).

![alt text](images/gradient_checkpointing.png)

### **Paralelismo de dados**

Quando os seus dados nao cabem em uma unica maquina é quando o paralelismo de dados brilha, a ideia aqui é treinar o modelo utilizando varias maquinas/GPUs que mantem modelos locais em que cada um calcula um gradiente independente e entao juntamos esses gradientes de alguma forma. Isso pode ser feito de 2 formas diferentes:

- **Sincrono:** Aqui se temos **N maquinas/GPUs cada uma delas tem um modelo local e vai receber um pedaco do mini-batch de dados (mini-batch/N)**, computar seu foward de maneira independente e esperar todas as outras terminarem, depois fazem uma computacao da media do gradiente juntas e atualizam seus modelos locais. 

    - **Vantagem:** matematicamente identico ao gradiente calculado em uma maquina sozinha 
    - **Desvantagem:** O elo mais fraco dita o ritmo. Se uma única máquina atrasar ou cair (problema conhecido como Straggler), todas as outras ficam ociosas aguardando a sincronização.

- **Assincrono:** Aqui, as $N$ máquinas também recebem pedaços dos dados, mas o ciclo de computação é totalmente independente. Geralmente coordenado por um **servidor centralizado (Parameter Server)**, cada máquina calcula seu gradiente local e o envia imediatamente para atualizar o modelo global, **sem esperar pelas outras**.

    - **Desvantagem:** Introduz o problema de Stale Gradients (gradientes obsoletos). Enquanto uma máquina lenta estava calculando seus gradientes baseada na versão X do modelo, outras máquinas mais rápidas já atualizaram o servidor central para a versão X+5. Quando a máquina lenta envia seu resultado, ela está tentando atualizar o modelo com informações defasadas, o que torna o treino instável e atrasa a convergência. Por isso na pratica nao é muito usado recentemente com LLMs.

![alt text](images/sgd_sincrono_vs_assincrono.png)

Outros 2 problemas que ambas as abordagens sofrem é:

- **Batch Size gigante:** Se uma maquina tem batch size de 100, entao um cluster com 100 maquinas vai ter batch size de 10000. O resultado disso é que a **quantidade de atualizacoes nos parametros** do modelo (steps_p_epoch = N_dados / Batch_sz) vai ser muito pequeno pequeno e consequentemente o modelo **vai aprender pouco**. Uma tentativa de mitigar é aumentar o learning rate mas isso aumenta o risco do modelo divergir. Além disso, batchs muito grandes aumentam a chance de **Diminishing Returns (Retornos Decrescentes)** que é: colocar mais poder de computação ou aumentar o tamanho do lote (batch size) não acelera o aprendizado do modelo de forma linear para sempre. Chega um ponto em que gastar o dobro de recursos traz um ganho quase imperceptível de performance pois o seu **gradiente esta quase igual ao gerado pelo dataset inteiro (BGD)**.

- **Worker principal sobrecarregado:** O worker principal consome muito mais recursos que os outros seja para sincronizar ou juntar parametros. Isso gera um desbalanceamente que pode ser mitigado de forma burra reduzindo o batch size apenas dele.

### **Paralelismo de modelos**

Paralelismo de dados funciona muito bem quando uma copia do seu modelo (parametros) cabe em uma unica GPU mas isso nem sempre é verdade. Se um **modelo é maior que a capacidade de uma unica GPU ele precisa ser dividido em varias GPUs** e existem varias formas de fazer isso:

- **Divisao das matrizes de pesos (Tensor Parallelism - TP):** Parte da matriz de peso do modelo fica em uma enquanto parte fica em outra (calculos podem ser feitos em paralelo e juntados depois)
- **Divisao em camadas (Naive Inter-Layer Parallelism):** Primeiras camadas ficam em uma GPU e as ultimas em outra GPU. Nao pode ser paralelizado pois a segunda GPU precisa do resultado da primeira e isso faz com que crie uma bolha de ociosidade enquanto espera resultados.
- **Pipeline (Pipeline Parallelism - PP):** Mesma logica de instrucoes de processador. Segue a logica da divisao em camadas porém divide o trabalho de cada GPU em micro trabalhos, a primeira GPU termina o primeiro micro trabalho e envia o resultado para a segunda GPU que comeca a processar o resultado da primeira GPU. A primeira GPU pode ir computando a seu segundo micro trabalho enquanto a segunda GPU trabalha. Isso mitiga o problema da bolha de ociosidade.

![alt text](images/pipeline_paralelism.png)

Outro detalhe importante é que paralelismo de modelo e de dados nao sao mutuamente exclusivos.

## **AutoML**

Consiste na ideia de usar tempo de computacao para encontrar os melhores algoritmos/modelos para um problema real de ML. Ao invés de ter 100 engenheiros de ML gaste 100x mais para encontrar o melhor modelo sozinho.

- **Soft AutoML (Hyperparametros):** A forma mais simples e mais popular de autoML é o **tunagem de hyperparametros** que sao configuracoes que modificam o espaco de busca do modelo durante o treino (diferente de parametros que sao o proprio espaco do modelo). Essa busca pode ser manual mas exitem formas aleatoras, de grid ou otimizacao bayesiana (HyperOPT) que sao bem mais eficientes (alguns hyperparametros sao mais sensiveis e necessitam mais cuidado). Além disso, a tunagem tem que ser feita no grupo de validacao pois se nao iremos overfittar o modelo ao teste.

- **Hard AutoMl (Architeture e Otimizador Aprendido)**: E se ao invés de fazer uma busca apenas no universo dos hyperparamtros fizessemos uma busca na arquitetura de redes neurais ? Isso é chamado de NAS (Neural Architeture Search) em que varios componentes como quantidade de blocos convolucionais. Uma NAS é composta de 3 elementos: 
    - **Espaco de busca:** Blocos de arquitetura que serao combinados
    - **Heuristica de performace:** Nao queremos treinar todas as redes testadas do zero entao precisamos de uma forma rapida de avaliar qualidade.
    - **Estrategia de exploracao:** A forma que os componentes serao combinados, como grid, porém os de ML classico acabam sendo muito caros entao solucoes com aprendizado por reforco e algoritmos geneticos existes.

No processo comum de treino em ML voce tem um modelo e um algoritmo de otimizacao (Adam, SGD) que te ajuda a encontrar os parametros que minimizam uma funcao de custo para um determinado dataset. Uma abordagem proposta foi ao invés de otimizar uma funcao fixa, **colocamos uma rede neural para aprender a ensinar seu modelo (quanto devemos ajustar os pesos)**. Learned Optimizer sao redes que tem o objetivo unico de ensinar outras redes. Existem duas abordagens principais:

- **Otimizacao conjunta:** Treinamos a "Otimizador" e nosso modelo junto do zero. Isso é muito caro e demorado.
- **Meta-learning:** Treinamos o otimizador para otimizar diversos problemas/tarefas diferentes, assim ensinando ele a aprender e consequentemente ter um bom desempenho em qualquer tarefa que de ele. 

## **4 Fases do desenvolvimento de modelos ML**

1. **Heuristicas Basicas:** Aqui testamos as solucoes (baseline fraco) mais simples (if/else e estatistica) e vemos quao bem elas se saem na realidade. Depois, se necessário abordar com ML.

2. **Modelos Simples:** A ideia aqui é comecar com modelos simples e faceis de colocar em producao mesmo que o resultado deles nao seja o melhor eles servem para validar o pipeline e infraestrutura.

3. **Otimizar os modelos simples:** Aqui tentamos chegar ao máximo que o simples consegue oferecer

4. **Modelos mais complexos e lidar com mudancas:** chegamos ao limite dos modelos simples, mas o negócio ainda precisa de métricas melhores para se pagar ou evoluir. Só agora você tem permissão para usar "IAs pesadas". Também precisa monitorar o Data Drift ou decaimento do modelo (o quão rápido o seu modelo fica "burro" ou desatualizado em produção), para planejar a infraestrutura de retreinamento automático.

## **Offline Evaluation**

Consiste essencialmente no processo de avaliar seu modelo antes de ir para produção (deploy).

### **Baselines**

Baselines são importantes para ter uma noção de **quão bem nosso modelo esta comparado a soluções** mais simples, humanos ou outras soluções robustas:

- **Random Baseline:** Comparando com um modelo que a saida tem probabilidade definida como 50% para cada classe.
- **Heuristica Simples:** Comparar com alguma heuristica simples sem usar ML (como prever sempre a classe mais comum) 
- **Humano:** Saber quão bem um humano consegue fazer a tarefa
- **Soluções existentes:** Comparar com soluções existentes como arquiteturas diferentes ou modelagens diferentes. Nem sempre um modelo tem que ser melhor que outro em 100% das coisas mas coisas como custo ou tempo de inferencia também importam.

### **Metodos de avaliação**

Na academia muitas vezes queremos focar apenas nas métricas de performace. Em produção outros problemas também são importantes de se avaliar como rubustes, justiça e calibração.

- **Testes de perturbação:** O modelo se sair bem com dados de treino não significa que ele vai sair bem com dados reais. Dados de treino comportados podem não representar tão bem dados reais cheios de ruidos que podem confundir o modelo. Um exemplo disso seria treinar com audios limpos e de alta qualidade mas não levar em consideração que na vida real existem audios com baixa qualidade e cheios de ruidos como musica de fundo.

- **Testes de invariância:** O output do modelo não pode mudar se uma caracteristica sensivel do input mudar. Por exemplo se o sexo de uma pessoa alterar a predição não pode ser diferente pois isso signfica um viés no modelo. Tirar features como essas é interessante quando possivel mas nem sempre é ... tirar pessoas negras de foto ?.

- **Testes de direção:** Testes para verificar se a direção de uma predição esta coerente com a direção de uma feature. Por exemplo se uma feature m² aumentar a previsão de preço tem que aumentar também.

- **Calibração de modelo:** Um modelo é calibrado quando o numero de vezes que ele retorna um score 0.7 é exatamente a proporção de vezes que ele acertou (70% das vezes que o score é 0,7 ele tem que acertar). Caso, contrário ele esta descalibrado. Isso pode ser visualizado com calibration plots (que score predito e quantas vezes com o score predito ele realmente acertou) em que a linha diagonal é o modelo perfeitamente calibrado. Para estar calibrado, se você pegar todas as vezes que ele previu 0.1, a classe positiva deve acontecer em exatamente 10% dos casos (e a classe negativa em 90%).


![alt text](images/calibration_plots.png)

- **Mensurar confiança:** Uma forma de avaliar cada unica predição do modelo em questão de que confiança é necessária para usar aquela predição ? se estamos em ambiente medico provavelmente só queremos usar predições com confiança maior que 99%, abaixo disso podemos descartar ou chamar um especialista (human-in-the-loop).

- **Avaliação Slice-based:** Essa validação em suma significa **separar seus dados em subconjuntos (seguindo alguma logica) e avaliar seu modelo em cada subconjunto**. Mas afinal qual o ponto disso ? A ideia aqui é que um padrão observado em varios grupos separados pode mudar ou sumir quando esses grupos são combinados. Isso é chamado de **paradoxo de simpson** e um exemplo interessante foi na universidade de berkley em que olhando para todo o conjunto mulheres tinham aprovação de 33% enquanto homens 44% mas olhando de forma separada por departamento as mulheres eram mais aprovadas em 4/6 departamentos. Esse efeito pode levar a dois problemas opostos:

    - **Modelo com performace diferente em cada grupo:** Isso pode significar que o modelo tem algum tipo de viés que privilegia algum grupo.

    - **Modelo com performace igual em cada grupo:** Aqui o problema já é tratar grupos da mesma forma quando existem grupos que são mais criticos e deveriam ser privilegiados.

    Ok, sabemos do problema mas como encontrar os grupos ?

    - **Heuristicas:** usar conhecimento do dominio e do objetivo da empresa para divisão dos grupos
    - **Analise de erro:** Encontrar padrões nos erros do modelo
    - **Frameworks:** bibliotecas que disponibilizam metodos automaticos

    (A qualidade dos dados nesses grupos criticos tem que ser garantida)




