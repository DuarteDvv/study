
# **Conceitos**

Computação Natural -> Algortimos, Simulações de ambientes naturais e biologia aplicada na computação. Trabalha com modelos que são abstrações aproximadas do mundo real.

- **Heuristicas x Meta heuristicas x hyper heuristicas** 

    - *Heuristica* é uma estratégia especifica para resolver um problema que usa conhecimento do dominio e não garante o a solução ótima.

    - *Meta Heuristica (Algoritmos geneticos estão aqui)* estratégias genéricas que orientam como explorar o espaço independente do dominio (framework de epxloração) -> Meta-heurística normalmente explora o espaço de soluções;

    - *Hiper Heuristica* automatiza a escolha, combinação ou criação de heurísticas mais simples para resolver problemas complexos de otimização -> Hiper-heurística normalmente explora o espaço de heurísticas.


- **Coletividade e agentes**: Maioria dos métodos é composto por um conjunto (coletividade) de agentes que interagem entre si e com o ambiente.
- **Paralelismo e distribuição:** Agentes, populações trabalham em paralelo
- **Interação:** Os agentes interagem entre si em 2 tipos:
    - Conexão direta entre eles (como arestas)
    - Estigmergia que é uma relação indireta como alterações no ambiente compartilhado
- **Adaptação:** Capacidade do sistema se adaptar ao ambiente/espaço
    - Aprendizado: Através de experiencia e erros cometidos
    - Evolução: Através de reproduções e mutações dos individuos 
- **Feedback:** Um estimulo no sistema tem capacidade de aumentar ou reduzir o estimulo original
- **Auto-organização:** Capacidade de um sistema produzir estruturas ou padrões organizados a partir de interações locais, sem a necessidade de um controlador central.
- **Complexidade**: Um sistema complexo possui muitos componentes ou interações cujo comportamento coletivo não pode ser facilmente explicado apenas analisando cada componente isoladamente.
- **Emergencia:** Surgimento de propriedades ou comportamentos no nível global do sistema que resultam das interações entre componentes locais. (Uma formiga não tem comportamente de achar algo mas um conjunto de formigas tem)

# **Computação Evolucionária**

## **Problemas de otimização**

- *Espaço de busca S:* São todas as possiveis soluções que o método pode retornar (por exemplo em uma regressão são todos as infinitas combinações de pesos). Esse espaço costumar se gigante ou infinito sendo necessário formas inteligentes de explorar.
- *Função Objetivo f:* mede o quão boa ou ruim uma solução candidata é
- *Região de restrições/factivel R:* Uma subregião contida em S que limita as soluções validas 
- *Representação da solução:* O jeito que uma solução é representada muda completamente seu espaço de busca, se temos um vetor binário o espaço terá combinações diferentes que um vetor continuo.

## **Fitness Landscape** 

È uma visualização do espaço de busca em que conseguimos ver a vizinhança e suas respectivas qualidades em relação a função objetivo. Essa paisagem pode deixar evidente diversos otimos locais e globais e certas caracteristicas podem tornar ainda mais dificil explorar ele:

- Rugosidade do espaço: Muitos maximos locais que dificultam encontrar o maximo global
- Plato no espaço: Região com fitness igual para varias soluções que dificulta explorar vizinhança
- Engano: Um otimo local na direção oposta do global
- Epistasia: Varias variaveis tem correlação entre si e otimizar elas separadas não é possivel

## **Algoritmos Evolucionários**

Algortimos inspirados na evolução biológica e dentro desse conjunto existem existem familias como Algoritmos Genéticos, Programação Genética e PG com gramaticas. O funcionamento básico de um AE é, então:

$$
\text{população inicial}
\rightarrow
\text{avaliação}
\rightarrow
\text{seleção}
\rightarrow
\text{cruzamento/mutação}
\rightarrow
\text{nova população}
$$

Esse ciclo se repete por várias gerações. A cada geração, os indivíduos são avaliados por uma função de fitness; os melhores têm maior chance de participar da reprodução; operadores genéticos geram novos indivíduos; esses novos indivíduos são avaliados novamente.

### **Algoritmos Genéticos**

Algoritmos Genéticos (GAs) é uma das técnicas mais conhecidas de Computação Evolucionária. A lógica geral é: representar soluções como indivíduos, avaliar sua qualidade, selecionar os melhores, gerar descendentes por cruzamento e mutação e repetir o processo. A intenção é que, geração após geração a qualidade média da população vai aumentar e que eventualmente apareça uma solução suficientemente boa.

*Genótipo (Gene) x Fenótipo (Caracteristica)*

- Genótipo é a representacao que escolhemos para algum individuo/solucao, por exemplo, podemos representar um grafo como uma matriz binária de presenca ou nao de um nó.
- Fenótipo é a estrutura real que o fenótipo codifica que seria um grafo real

O GA navega no espaco de solucoes buscando boas fitness usando 3 operacoes principais: 

- *Selecao:* Aqui a busca é direcionada para regioes mais promissoras da regiao atual (explora vizinhanca)
- *Cruzamento:* Aqui informacoes que ja existem sao combinadas para descobrir combinacoes melhores
- *Mutacao:* Aqui novas informacoes sao geradas e quem sabe descobrir coisas novas boas

Uma busca muito concentrada na vizinhança pode parar em um máximo local e nunca descobrir o global. Daí surge a diferença entre busca local e global. 

- *Busca local:* explora a vizinhança da solução atual. Uma mutacao fraca ou cruzamento de individuos proximos pode gerar novos individuos da vizinhanca.
- *Busca global:* pode explorar regiões muito diferentes do espaço. Uma mutacao forte ou um cruzamento de individuos diferentes pode gerar individuos totalmente novos.

Um GA tenta ter características de busca global justamente porque mantém uma população e usa crossover e mutação, em vez de seguir apenas uma única trajetória. Um ponto central é o equilíbrio entre exploração e explotação:

- *Exploração:* significa experimentar regiões novas do espaço de busca. Se houver exploração demais, o algoritmo se comporta quase aleatoriamente.
- *Explotação:* significa aproveitar regiões que já sabemos que possuem boas soluções. Se houver explotação demais, ele pode convergir cedo demais para um máximo local.

*Pressão seletiva:* Quanto mais preferirmos os melhores indivíduos (explotation), maior a pressão seletiva e mais rapidamente a população tende a se concentrar nas melhores soluções atuais. Isso acelera a evolução, mas pode reduzir a diversidade muito cedo gerando um otimo local.

#### **Fluxo**

O fluxograma do GA pode ser entendido assim:

$$
\text{criar população (Pop. inicial)}
\rightarrow
\text{avaliar fitness}
\rightarrow
\text{selecionar pais}
\rightarrow
\text{crossover/mutação}
\rightarrow
\text{gerar filhos}
\rightarrow
\text{substituir população}
$$
Depois verificamos o critério de parada. Se ele não foi satisfeito, repetimos tudo. Caso tenha sido satisfeito, retornamos o melhor indivíduo.

#### **Operadores de selecao**

A primeira tarefa é dado um conjunto de solucoes escolher quem deve reproduzir 

- *Roleta:* Nesse modelo damos probabilidades para os individuos da populacao baseado nas suas fitness, ou seja, fitness é a proporcao da fitness total de todos os individuos que aquele individuo tem. Se a soma de todos as fitness é 10 e um só tem 5 de fitness sua probabilidade é 50%. É bom notar que o individuo com maior probabilidade nao é obrigatoriamente escolhido mas tem maior chance.
    - Se inicialmente um elemento tem fitness absurdamente maior que os outros tal que cause que a probabilidade seja proxima de 1, vamos escolher o mesmo individuo varias vezes para a reproducao fazendo com que ele domine a proxima geracao
    - Depois de algumas geracoes e a fitness da populacao estabilizar todo mundo vai ter fitness proxima e a escolha vai ser praticamente aleatória e nao sabemos mais quem é promissor ali.

- *Torneio:* Nessa forma escolhemos k individuos aleatoriamente e pegamos o melhor dos k para reproduzir. Repetimos essa escolha ate formarmos todos os pares de reproducao. 
    - O parametro k controla a pressao seletiva, ou seja, maior k -> maior pressao -> maior probabilidade do melhor da populacao aparecer no torneio e ganhar de todos. Uma vez que o melhor aparece mais vamos ter menor diversidade e consequentemente concentrar a busca em um possivel otimo local. Dessa forma podemos controlar facilmente a pressao seletiva.

#### **Operadores de crossover**

Dado que 2 pais foram selecionados precisamos de uma forma de cruzar eles. Esse crossover é controlado por uma probabilidade de pc (Geralmente alta) em que depois de escolhermos os pais olhamos para probabilidade e decidimos se cruzamos eles ou nao.

- *Corte de um ponto:* Escolhemos aleatoriamente um ponto na representacao, cortamos naquele ponto e trocamos as pontas entre os pais gerando 2 filhos. Ex: AB CD -> A B C D -> AC BD
- *Uniforme:* Cada gene/posicao do filho tem probabilidade de p de vir do pai A e 1 - p de vir do pai B

#### **Operadores de mutacao**

A mutacao acontece em um unico individuo da nova populacao por vez em que dado uma probabilidade pm (que costuma ser baixa) mutamos ou nao aquele individuo.

- *Um ponto:* Escolhemos aleatoriamente um ponto para inverter/trocar
- *Uniforme:* Para cada gene temos uma probabilidade p de inverter ou nao 

Sem mutação, o GA só poderia reorganizar informação genética que já está presente na população. Se certo gene necessário à solução ótima desaparecer completamente, o crossover sozinho não consegue recriá-lo. A mutação permite que essa informação volte a aparecer.

#### **GA Geracional x Steady state**

- **Geracional:** é quando todos os filhos gerados substituem os pais geradores 
- **Steady state:** não ocorre uma substituição completa. Apenas alguns indivíduos são trocados de cada vez. Um exemplo disso é considerar os pais e filhos juntos e pegar os 2 melhores.

#### **Elitismo**

Um parametro k em que decidimos o top k melhores da populacao atual que vao para proxima populacao de qualquer jeito. Isso evita perder otimas solucoes para operacoes aleatórias mas se o k for muito alto a pressao seletiva aumenta muito levando a convergencia rapida.

### **Programação Genética**

No GA o individuo representa uma possivel solução para o problema. Na GP, o objetivo é evoluir um programa/equação, capaz de resolver um tipo de problema. Por isso, um indivíduo contém não apenas dados, mas também funções e operadores, e pode ter tamanho e formato variáveis. Um exemplo seria imagine uma equação: 

$$
f(x)=\sin(x)-0{,}1x+2.
$$

- Em GA estamos interessados em encontrar um individuo que é valor de X que maximize F. 
- Em GP, poderiamos não ter a função e sim dados gerados por ela (x, f(x)) e estamos interessando em encontrar um individuo que é uma função/equação (regressão simbolica) que se aproxime de F usando os dados.

#### **Representação**

A representação mais comum é o formato de arvore em que existem 2 tipos de valores para os nós:

- *Terminais:* Variaveis e constantes que são colocadas nas folhas da arvore (como 1 e x)
- *Função:* Operadores que combinam os nós terminais (como soma e multiplicação) e que são os nós internos

#### **Fluxo**

O mesmo do GA porém com diferentes crossover e mutações

#### **Criando população inicial**

- *Grow*: Aqui criamos arvores que nao necessáriamente sao balanceadas pois sempre que vamos escolher um nó aleatório ele *pode ser um terminal ou uma funcao*. Entao os ramos podem acabar antes de atingir a profundidade maxima caso o nó escolhido seja terminal.
- *Full*: Aqui criamos arvores necessáriamente balanceadas em que todos os nós antes da profundidade máxima sao funcoes e apenas os do ultimo nivel sao terminais
- *Half-and-Half:* Aqui intercalamos as duas formas anteriores

#### **Crossover**

Aqui ao inves de trocar metade de um vetor/string trocamos subarvores entre 2 pais.

#### **Mutacao**

Aqui mutacoes podem ser feitas:

- *Um ponto:* trocar alguma funcao ou terminal
- *Expensa:* trocar uma subarvore por uma maior 
- *Reducao:* trocar uma subarvore por uma menor

#### **Quais funcoes sao validas para o conjunto F?**

Essas funcoes tem que respeitar alguns criterios:
1. *Suficiencia:* Elas tem que ser suficientes para resolver o problema
2. *Closure:* A saida dela tem que ser valida como entrada de qualquer outra (Ou seja, n pode dar indefinido ou infinito)
3. *Parcimonia:* Queremos o conjunto minimo suficiente

#### **Introns**

Sao partes do expressao/equacao que nao alteram em nada a saida, ou seja, a presenca dela n faz diferenca. Um exemplo é (X + 0) x 1 + y - y em que no final o resultado será X de qualquer maneira. Intros leva a um fenomeno chamado *BLOAT* em que arvores continuam crescendo durante as geracoes mas possuem exatamente a mesma fitness já que o resultado é o mesmo, isso leva a maior consumo de memória devido aos nós e maior lentidao no calculo de fitness. A solucao mais trivial é:

- Penalizar na fitness a complexidade da expressao, ou seja, teriamos algo como $fitness= erro + \lambda\cdot\text{tamanho da árvore}$ em que o primeiro termo recompensa precisão. O segundo penaliza complexidade. O parâmetro lambda controla o quanto nos importamos com árvores menores. 

### **Programacao genética com gramática**

O problema da programacao genetica anterior é que muitas das operacoes eram aleatórias e isso permitia a construcao de coisas invalidas usando as funcoes e terminais disponiveis. Um exemplo disso é a funcao AND(a,b) que espera 2 valores booleanos mas se usarmos algo 100% aleatório podemos cair em algo como AND(3,3.4) e isso é invalido.

#### **Solucao trivial**

A solucao mais simples para esse problema é restringir o dominio de terminais de cada funcao, ou seja, AND/OR -> Booleanos, +/- -> Reais. 

#### **Gramatica**

A ideia de GP baseada em gramática vai além. Em vez de definir apenas funções e terminais, você define uma gramática completa, com terminais, não-terminais, símbolo inicial e regras de produção. Isso garante fechamento e permite incorporar conhecimento do domínio diretamente no espaço de busca pois com a gramatica podemos definiir por exemplo que nossas estruturas vao sempre ser compostas por uma soma de 2 coisas, ou seja, temos maior controle na evolucao.

Um exemplo simples de gramática seria:
$$
\langle expr\rangle ::= \langle expr\rangle + \langle expr\rangle
\mid
\langle var\rangle
\mid
\langle num\rangle
$$
$$
\langle var\rangle ::= x \mid y
$$
$$
\langle num\rangle ::= 2 \mid 4.
$$

Aqui, $\langle expr\rangle$, $\langle var\rangle$ e $\langle num\rangle$ são não-terminais. Já +, x, y, 2 e 4 são terminais. Em geral a gramatica restringe o espaco de busca em apenas solucoes validas.

#### **Tipos**

Dois tipos de GP baseada em gramáticas:

- *Tipo 1:* A gramática produz indivíduos válidos, e crossover e mutação precisam respeitar as regras da gramatica e consequentemente eles deixam de ser destrutivos.

- *Tipo 2:* Aqui separamos a gramatica da evolucao, o individuo nao é mais a expressao em si mas sim um vetor de bits. Depois esses bits sao lidos em blocos para formar um inteiro, por exemplo bloco 0011 vira 3, e depois dividimos esse numero pelo MOD da quantidade de opcoes na gramatica para decidir qual pegar, por exemplo para gramatica de tamanho 2, $\langle expr\rangle$ | $\langle num \rangle$ dividimos 3 mod 2 que da 1 e pegamos o de indice 1 que é o $\langle num \rangle$.

    Então o processo inteiro pode ser pensado como:
    $$
    \text{bits (genotipo)}
    \rightarrow
    \text{códons}
    \rightarrow
    \text{inteiros}
    \rightarrow
    \text{regras}
    \rightarrow
    \text{programa (fenotipo)}
    $$

    - Se os bits acabarem e nao temos uma expressao completa ainda, ou seja, falta terminais, voltamos ao inicio dos bits e reutilizamos os blocos (codons) e isso se chama *wrapping*.
    - Outro ponto é a degeneracao do codigo genetico devido ao fato de que diferentes vetores de bits podem levar a mesma solucao, ou seja, podem existir mutacoes e crossovers nulos.

#### **Localidade**

Localidade mede se pequenas mudanças no genótipo produzem pequenas mudanças no fenótipo:

- *Se temos alta localidade:* genótipos próximos tem fenótipos próximos
- *Se temos baixa localidade:* pequena mudança no genótipo tem grande mudança no fenótipo

Imagine que encontramos um programa muito bom. Fazemos uma pequena mutação esperando testar uma solução ligeiramente diferente e acabamos com algo totalmente diferente e muito inferior. Isso significa que a busca fica muito mais instavel pois nao temos mais uma nocao de melhoria local.

#### **Structured Grammatical Evolution**

Esse é uma tentativa de usar uma representação mais estruturada. Ate agora o genotipo do GE é algo parecido com um vetor de numeros (depois de transformar os bits em numeros).
$$
[43,118,144,17,\ldots]
$$

sem que cada posição seja explicitamente “o gene do $\langle expr\rangle$” ou “o gene do $\langle op\rangle$”, ou seja, qualquer numero ai pode acabar sendo convertido para qualquer tipo de operador. Na SGE, a representação é mais estruturada:

$$

\begin{array}{c}
\langle start\rangle \rightarrow [\ldots]\\
\langle expr\rangle \rightarrow [\ldots]\\
\langle term\rangle \rightarrow [\ldots]\\
\langle op\rangle \rightarrow [\ldots]
\end{array}
$$
Cada parte do vetor/genótipo fica associada a uma categoria da gramática. Por isso crossover e mutação podem operar de maneira mais estruturada e com maior localidade pois o cruzamento troca partes correspondentes da representação, e os fenótipos filhos continuam respeitando a estrutura da gramática.

## **Diversidade e Co-evolucao**

### **Diversidade**

O problema de algoritmos evolucionários padrões é que eles perdem muita diversidade depois algumas gerações, ou seja, todas as soluções se concentram em um unico pedaço promissor do espaço e a população começa a ficar quase uniforme (cheia de copias). Dai vem o conceito de niching, que vem de nicho ecológico, em que queremos manter grupos diferentes em regiões boas diferentes do espaço e o tamanho do grupo ser proporcional a qualidade da região.Então no geral o objetivo do niching é:

- Reduzir convergencia prematura em otimos locais
- Encontrar varias soluções boas ao invés de uma

#### **Fitness Sharing**

Dado que a fitness original de cada individuo é Fi, a fitness compartilhada é definida pela *divisão Fi / NCi* em que NCi é a soma da similiaridade de i com todos os outros individuos. Ou seja, se ele é igual a muita gente (pouca diversidade) a soma da um valor alto e consequentemente a fitness compartilhada fica baixa, o caso contrário faz ela ser alta. Geralmente existe um parametro especifico theta que diz o raio considerado na vizinhança, ou seja, o raio de individuos que vão compartilhar a mesma feature, todos fora desse raio (distancia > raio, essa distancia pode ser de fenotipo ou genotipo) recebem similiaridade 0.

- Essencialmente queremos privilegiar soluções boas em espaços pouco explorados
- Caro pois no pior caso temos que calcular distancia de todos contra todos que é N^2
- Raio Theta é dificil de escolher 

#### **Crowding** 

Essa funciona de forma diferente, ao invés de modificar a fitness nos evitamos replicação por trocas de individuos semelhantes da população. A ideia é durante a de um filho C com pais A e B, nós medimos a proximidade de C com A e B, escolhemos o pai com maior similiaridade com C. Depois se C é melhor que A trocamos A por C na população, caso contrário mantemos A e descartamos C.

#### **Problema de nichos**

O problema de nichos é que cruzar soluções de nichos/picos diferentes pode gerar soluções muito ruins e devido a isso o proposto foi permitir cruzamento apenas com individuos do mesmo nicho

### **Co-Evolução**

Aqui os individuos não são avaliados individualmente por uma fitness F(i) e sim em conjunto em relação a toda a populaçao F(i,j,k...), ou seja, a fitness é relativa ao ambiente evolutivo e qualidade global da populaçao. Um exemplo disso é pensar na fitness de um individuo A que é melhor que todo mundo, agora se surgir na população uma solução melhor, a fitness de A muda sem ele mudar.

#### **Competitiva**

Temos 2 ou mais populações evoluindo juntas para um mesmo objetivo e tentando sempre superar as outras, isso faz com que elas evoluam juntas para sempre serem o melhor possivel. A avaliação pode ser feita de diferentes formas como um contra todos, todos contra o melhor ou um contra k aleatorios.

#### **Cooperativa**

A ideia aqui é dividir um grande problema modularizavel em subproblemas e cada população evolui um subproblema mas elas são avaliadas em conjunto durante a evolução. Entretanto, existe um problema que é o fato da fitness ser relativa ao resto, então se A é melhor que B em t1 e depois ambos A e B pioram mas A continua melhor, a fitness permanecera a mesma.

## **Otimização Multiobjetivo**





## **Experimentação de algortimos genéticos**

O desempenho desses algoritmos pode mudar muito de acordo com a seed usada mesmo com hyperparametros iguais, uma vez que todas as operações da evolução envolvem aleatóriedade. Isso leva ao primeiro ponto importante:

- Sempre *executar o algortimo várias vezes e com seeds diferentes para ter uma estimativa da varianca dos resultados*. Um intervalo de confiança pode gerado também para quantificar essa incerteza em um intervalo. Além disso, essas sementes do experimento devem ser armazenadas para tornar o experimento reprodutivel.

O segundo ponto é:

- Antes de qualquer coisa precisamos *comparar com a solução aleatória*, se o algoritmo sofisticado não supera o aleatório então existe algum problema na representação, fitness ou algortimo.

O terceiro ponto é a busca de hyperparametros:

- Se a busca de hyperparametros estiver sendo feita a mão, sempre variar apenas um enquanto mantem os outros fixos. Se isso não for feito qualquer alteração positiva ou negativa dos resultados podera ter sido causada por qualquer uma das alterações e não saberemos qual. Idealmente podemos usar metodos de otimização bayesiana para buscar os hyperparametros como por exemplo Optuna.

O quarto ponto é nunca olhar apenas para melhor individuo da população:

- Durante as gerações temos que analisar a diversidade da população, fitness e os efeitos do crossover/mutação na qualidade da geração. Um exemplo seria monitorar o max fitness, min fitness, alguns quartis e variancia para ter uma noção de como esta a distribuição desses individuos. 
  - Se todos estiverem muito parecidos as linhas do grafico (max,min,median fitness) vão sempre muito proximas e isso é sinal de convergencia prematura e falta de diversidade. Nesses casos pode ser interessante:
    - diminuir pressão seletiva através de parametros como o k do torneio
      - mutações mais fortes ou mais comuns (maior P_mut)
      - introduzir especies (nichos que evoluem de forma independentes)
      - usar fitness sharing ou crowding
    - Se temos uma variancia muito grande significa que existem individuos na população que são muito diferentes, ou seja, de uma região no espaço totalmente diferente que ainda não foi muito explorada na população.
    - A diversidade é muito importante mas quando existe mais que deveria ela faz com que o algoritmo não chegue a convergir e nenhum resultado bom aparece (explotation x exploration).

O ultimo ponto é:

- Ao executar e mudar muitos hyperparametros e nada dar certo talvez o problema na verdade seja a representação ou a fitness.



- 













 
