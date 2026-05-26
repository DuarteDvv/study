## **Desenvolvimento do modelo e avaliacao Offline**

### **Escolhendo um modelo de ML** 

Ao inicio de um projeto precisamos escolher qual modelo utilizar para nosso problema... normalmente começamos por algo mais simples e evoluimos essa escolha mas nem sempre temos tempo para testar todas as possibilidades possiveis de modelos/arquiteturas disponiveis. Essas escolhas são guiadas por diversas caracteristicas dos nossos dados, do problema, da disponibilidade de recursos, das restrições da empresa e do tempo disponivel... por exemplo se temos pouco tempo não podemos testar tudo e portanto temos que fazer uma busca guiada. Se nosso problema precisa de um tempo de inferencia baixo não é interessante modelos de redes neurais gigantescos (A não ser que tenha dinheiro e hardware disponivel mas mesmo assim nem sempre é possivel), se nossos recursos são poucos talvez modelos mais simples sejam a melhor escolhe provisória... se temos poucos dados modelos que aprendem com poucos dados, se temos muitos dados modelos que conseguem extrair o melhor disso como redes neurais. Precisamos de online learning ? nem todo modelo é capaz de aprender incrementalmente. Se precisa de explicabilidade talves redes neurais não sejam a melhor opção. Se precisa processar texto provavelmente transformers são a melhor opção.

Dicas para escolha de modelo:

- Evitar ao máximo o estado da arte atual: A maioria das arquiteturas/modelos estado da arte foram desenvolvidos no ambiente academico em que pesquisadores superam benchmarks definidos e estáticos com o unico objetivo de superar métricas de qualidade... independente do tempo, custo ou premiças. Muito dificilmente irá funcionar para uma quantidade maior de usuários... ou nos dados de produção que mudam constantemente. Sempre escolha o mais simples que resolva seu problema... se existe um modelo mais simples e barato ele é a melhor opção.

- Comece sempre usando modelos mais simples: Modelos simples são geralmente mais faceis de se utilizar, mais rapidos e mais interpretaveis... começar com eles nos permite ter um baseline de melhoria para modelos mais complexos e nos permite validar o pipeline de inferencia para identificar erros de forma mais simples antes de adicionar componentes complexos. Nem sempre simples significa o mais fraco de todos... hoje em dia por exemplo é muito facil ter acesso a LLMs pretreinadas e portanto tornando elas faceis de usar...

- Evitar viés humano ao escolher os modelos: Se você ou alguém da equipe esta muito animado com um novo lançamento de um modelo estado da arte ou com um novo benchmark de metodo que demonstrou bons resultados... querer usar/testar esse métodos é natural porém novos metodos são propostos diariamente. È muito improvavel que a nova arquitetura seja melhor em todos os contextos existentes pois existem muitas variaveis possiveis então não é pq bateu em uma comparação em um contexto que no seu vai funcionar.

- O melhor modelo agora pode não ser o melhor modelo no futuro: Se um modelo tem um desempenho pior agora isso não significa que esse modelo com novos dados não pode generalizar melhor no futuro... se plotarmos a learning curve, ou seja, a loss conforme aumentamos os dados de treinamento podemos ver como a generalização do modelo escala com a quantidade de dados. Se estivermos no começo de um sistema e não tivermos dados ainda é provavel que modelos mais simples como SVM vão se sair melhor... porém conforme vamos adquirindo mais dados é possivel que modelos como os de redes neurais generalizem melhor...

- Considerar trade-offs do contexto atual: Em muitos problemas resultados errados tem pesos diferentes... um falso negativo pode ser mais caro que um falso positivo então faz sentido escolher os modelos que sejam melhores nesses detalhes do problema. Outro ponto comum é o custo x performance, talvez trocar o modelo barato pelo caro não compense no final pois a diferença nos resultados não é tão relevante...

- Entender as suposições de cada modelo/arquitetura: Todo modelo é construido em cima de alguma suposições... seja sobre distribuição ou independencia... é importante considerar isso na hora de escolher algo para seu contexto.

### **Ensemble**

Ensemble de modelos consiste no uso de mais de varios modelos diferentes (ou o mesmo treinado em dados diferentes) para fazer uma unica previsão... se esses modelos forem independentes a acurácia é matematicamente aumentada, podemos verificar isso calculando as probabilidades de erro dos 3 juntos ou 2 deles juntos e depois olhar o complemento. Ensemble é muito usado em competições mas pode ser complexo usar em produção devido ao custo e tempo de inferencia... a não ser que uma previsão certa tenha um ROI muito alto. Existem 3 tipos principais de ensemble:

- **Bagging (Bootstrap bagging):** Aqui nos temos varios base learners (modelos) que não serão treinados nos mesmos dados... a ideia principal é que o dataset de treino de cada um desses base learners é gerado a partir de bootstraping (com reposição, ou seja, tratamos a amostra como uma população e amostramos dela supondo que ela seja representativa da população real) do dataset original. Assim teremos diversos modelos treinados em amostragens diferentes dos dados pegamos a saida deles e fazemos uma media/contagem... algumas vão ter amostragens melhores, outras piores e outras que tratam problemas ate de desbalanceamento (oversampling intriseco). Em geral, o metodo torna mais estavel metodos muito sensiveis aos dados como redes neurais... random forest é um bom exemplo de bagging pois ela treina diversas arvores de decisão com subsets aleatórios do total de features (ao inves de linhas o aleatório sao as colunas).

- **Boosting:** Aqui é um pouco diferente pois queremos treinar novos modelos do conjunto que sejam bons no que o ensemble atual não é bom ... começamos com um modelo simples (Nosso ensemble tem apenas um modelo no começo) e fazemos as previsões. Nos exemplos de treino que o modelo errar nos colocaremos um peso maior de loss e novo modelo do ensemble é treinado com os dados com pesos atualizados... consequentemente ele será melhor nos casos que foram errados e estavam com maior peso... depois fazemos as predições novamente com o ensemble (agora com 2 modelos) e repetimos o processo de dar maior peso para os erros nas iterações seguintes.Bons exemplos de boosting são as XGBoost e o LightGBM que usa aprendizado distribuido e portanto é mais rapido para grandes volumes de dados.

- **Stacking:** Esse é o mais simples em que treinamos varios modelos com o mesmo conjunto de dados, pegamos as predições deles e usamos como features em um modelo final que pode ser simples como simplesmente contar elas ou pode ser complexo como uma regressão linear e aprender padrões de quando dar mais peso para cada modelo base. 

### **Monitoramento (experiment tracking) e Versionamento**

È comum fazer experimentos com novas arquiteturas, hyperparametros e dados para testar teorias e encontrar novos resultados que podem ser melhores... cada experimento tem sua curva de loss, curva de learning, logs, arquivos de saida (como modelos torch .pt) e diversos outros artefatos. È importante manter todas as definições e parametros de um experimento para ser possivel recria-los e além disso salvar os outputs/resultados para comparar com outros experimentos. O processo de monitorar o progresso e resultados de um experimento é chamado de **experiment tracking** e o processo de criar logs de todos os detalhes do experimento com objetivo de recriar (Reprodutibilidade) o experimento ou compara-lo é chamado de **versionamento**. Existem diversas ferramentas como MLflow e Weights and bias que originalmente serviam apenas para monitoramento mas depois incluiram versionamento.

#### Experiment Tracking


#### Versionamento






