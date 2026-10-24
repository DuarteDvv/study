# **Estatistica e modelos causais**

## **Teorica de probabilidade X Estatisticas**

Em probabilidade já conhecemos o processo gerador, ou seja, a distribuicao verdadeira e queremos analisar os seus possiveis resultados através de experimentos com diversos parametros para gerar dados.

- Entretanto, em estatística o processo é contrário, nos temos os resultados e queremos afirmar coisas sobre o processo/distribuicao geradora em que geralmente temos tuplas ((x1,y1)...) chamadas de observacoes/linhas em que assumimos que cada uma delas sao i.i.d, ou seja, independentes e vieram da mesma distribuicao multivariada de origem chamada Pxy. O maior problema na estatistica é que desconhecemos a distribuicao real/original dos dados.

## **Learning Theory**

Investiga quando um modelo treinado/adaptado com dados empiricos (amostras) consegue se sair bem na inferencia em dados novos. Para isso ele precisa de:

- *Generalizacao:* Independente de quantas amostras (tamanho finito) temos nunca vamos conseguir mapear perfeitamente todas as saidas da funcao geradora dos dados, se agarrar muito a essa amostra pode levar a problemas de generalizacao como overfitting.
- *risco real e risco empirico:* idealmente queremos minimizar o risco/probabilidade real do nosso modelo errar porém para isso seria necessária a distribuicao de origem e portanto usamos o risco empirico buscando funcoes que minimizem ambos risco empirico e verdadeiro.
- *Capacidade:* tem que ser complexo suficiente para aprender os padroes
- *Consistente:* Se mais dados chegarem ele precisa se aproximar mais ainda do menor risco real 

## **Causal Reasoning and Learning**

![alt text](imgs/causal_vs_statistical_learning.png)

Modelos causais possuem mais informacao que modelos estatisticos pois eles permitem analisarmos o efeito de alteracoes em causas. O autor distingue 2 processos diferentes mas complementares:
- Causal Reasoning/Inference: Já sabemos o modelo causal, ou seja, temos o grafo que mapeia as causas nas variaveis. Isso permite fazer operacoes de inferencia causal (Conceito: Prever efeitos de intervencoes em variaveis) através de intervencoes nas causas. 
    - Reasoning é equivalente a probabilidade na inferencia classica uma vez que em ambos já temos os modelos e apenas queremos analisar os resultados gerados por esse modelo
- Causal Learning/Discovery: Nao temos a estrutura real ainda e precisamos estimar essa estrutura usando dados. Esse segundo problema é bem mais dificil uma vez que varios grafos/modelos podem gerar a mesma distribuicao empirica, ou seja, os dados que usamos e mesmo conhecendo a distribuicao real Pxy nao é trivial dizer que X causa Y pois por exemplo nao sabemos a direcao e também pode existir uma cofounder variable nao observada nos dados.
    - Learning é equivalente a statistical learning em que tentamos aprender o modelo real através de dados empiricos gerados por ele.

### **Princípio da causa comum de Reichenbach**

- Antes de ver o principio é bom lembrar: Ser estatisticamente independente significa que a ocorrência de um evento ou o valor de uma variável não altera, influencia ou traz nenhuma informação sobre a probabilidade de outro evento ou variável acontecer

Esse principio diz que se duas variaveis aleatórias sao estatisticamente dependentes, existe uma causa que explica essa dependencia e essa causa pode ser uma das 2 variaveis ou ate mesmo uma terceira variavel. Entretanto, se conhecemos essa variavel causadora e condicionarmos nela, a relacao de dependencia some, ou seja, se fumar causa tosse, se condicionarmos agora em apenas pessoas que fumam, a tosse nao depende mais de fumar pois fumar é fixo e sem variacao. Além disso, dependencias estatisticas podem surgir de ruidos dos proprios dados e erros cometidos por pessoas.

## **Exemplos**

### **Pattern Recognition**

Imagine 2 modelos causais para um problema de digitos:

1. Rotulo -causa-> Imagem: Quem gerou os dados recebeu um rotulo e depois desenhou o digito 
    - A Imagem é gerada por uma funcao F(Rotulo, Erro)
    - Podemos intervir no rotulo e a imagem será alterada mas intervir na imagem n muda o rotulo

2. Intencao -causa-> Rotulo: quem gerou os dados pensou um pouco e decidiou ambos através de um pensamento/intencao
    L-causa-> Imagem
    - A Imagem é gerada por uma funcao G(Intencao, Erro) e o Rotulo por uma funcao H(Intencao, Erro)
    - Podemos intervir no rotulo mas isso nao altera a imagem pois ele nao causou ela

Ambos os modelos podem gerar os mesmos dados observacionais mas as intervencoes possiveis sao bem diferentes. Essa é a motivação dos modelos causais estruturais (SCMs), representar explicitamente os mecanismos que geram as variáveis, ou seja, funcoes e tudo necessário para reproduzir os dados.

### **Gene Perturbation**

Imagine agora 2 modelos causais para um problema de atividade genetica:

1. Gene A -causa-> Fenotipo
    - Se reduzirmos experimentalmente a atividade do gene A a zero, esperamos uma mudança no fenótipo, pois existe uma relação causal direta.
2. Gene C -causa-> Gene B
    L-causa-> Fenotipo
    - Nesse caso, a atividade do gene B está correlacionada ao fenótipo, mas não é sua causa. Consequentemente, intervir no gene B não altera necessariamente o fenótipo.

Os dois casos podem apresentar distribuições observacionais idênticas, embora as intervenções tenham consequências diferentes.
  