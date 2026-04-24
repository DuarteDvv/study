## Desenvolvimento do modelo e avaliacao Offline

### Escolhendo um modelo de ML 

Ao inicio de um projeto precisamos escolher qual modelo utilizar para nosso problema... normalmente começamos por algo mais simples e evoluimos essa escolha mas nem sempre temos tempo para testar todas as possibilidades possiveis de modelos/arquiteturas disponiveis. Essas escolhas são guiadas por diversas caracteristicas dos nossos dados, do problema, da disponibilidade de recursos, das restrições da empresa e do tempo disponivel... por exemplo se temos pouco tempo não podemos testar tudo e portanto temos que fazer uma busca guiada. Se nosso problema precisa de um tempo de inferencia baixo não é interessante modelos de redes neurais gigantescos (A não ser que tenha dinheiro e hardware disponivel mas mesmo assim nem sempre é possivel), se nossos recursos são poucos talvez modelos mais simples sejam a melhor escolhe provisória... se temos poucos dados modelos que aprendem com poucos dados, se temos muitos dados modelos que conseguem extrair o melhor disso como redes neurais. Precisamos de online learning ? nem todo modelo é capaz de aprender incrementalmente. Se precisa de explicabilidade talves redes neurais não sejam a melhor opção. Se precisa processar texto provavelmente transformers são a melhor opção.

Dicas para escolha de modelo:

- Evitar ao máximo o estado da arte atual: A maioria das arquiteturas/modelos estado da arte foram desenvolvidos no ambiente academico em que pesquisadores superam benchmarks definidos e estáticos com o unico objetivo de superar métricas de qualidade... independente do tempo, custo ou premiças. Muito dificilmente irá funcionar para uma quantidade maior de usuários... ou nos dados de produção que mudam constantemente. Sempre escolha o mais simples que resolva seu problema... se existe um modelo mais simples e barato ele é a melhor opção.

- Comece sempre usando modelos mais simples: Modelos simples são geralmente mais faceis de se utilizar, mais rapidos e mais interpretaveis... começar com eles nos permite ter um baseline de melhoria para modelos mais complexos e nos permite validar o pipeline de inferencia para identificar erros de forma mais simples antes de adicionar componentes complexos. Nem sempre simples significa o mais fraco de todos... hoje em dia por exemplo é muito facil ter acesso a LLMs pretreinadas e portanto tornando elas faceis de usar...

- Evitar viés humano ao escolher os modelos: Se você ou alguém da equipe esta muito animado com um novo lançamento de um modelo estado da arte ou com um novo benchmark de metodo que demonstrou bons resultados... querer usar/testar esse métodos é natural porém novos metodos são propostos diariamente. È muito improvavel que a nova arquitetura seja melhor em todos os contextos existentes pois existem muitas variaveis possiveis então não é pq bateu em uma comparação em um contexto que no seu vai funcionar.

- O melhor modelo agora pode não ser o melhor modelo no futuro: Se um modelo tem um desempenho pior agora isso não significa que esse modelo com novos dados não pode generalizar melhor no futuro... se plotarmos a learning curve, ou seja, a loss conforme aumentamos os dados de treinamento podemos ver como a generalização do modelo escala com a quantidade de dados. Se estivermos no começo de um sistema e não tivermos dados ainda é provavel que modelos mais simples como SVM vão se sair melhor... porém conforme vamos adquirindo mais dados é possivel que modelos como os de redes neurais generalizem melhor...

- Considerar trade-offs do contexto atual: Em muitos problemas resultados errados tem pesos diferentes... um falso negativo pode ser mais caro que um falso positivo então faz sentido escolher os modelos que sejam melhores nesses detalhes do problema. Outro ponto comum é o custo x performance, talvez trocar o modelo barato pelo caro não compense no final pois a diferença nos resultados não é tão relevante...

- Entender as suposições de cada modelo/arquitetura: Todo modelo é construido em cima de alguma suposições... seja sobre distribuição ou independencia... é importante considerar isso na hora de escolher algo para seu contexto.

### Ensemble
