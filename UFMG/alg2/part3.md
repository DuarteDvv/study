# **Solução de problemas combinatórios**

## **Força bruta**

Verificar todas as combinações possiveis de soluções (validas ou não) e verificar se é valida com as restrições do problema. O problema aqui é que o numero de possiveis soluções cresce exponencialmente ou fatoralmente. 

- Se temos o problema da mochila, para cada um dos n objetos temos a decisão de pegar ou não o objeto, ou seja, 2 possibilidades. Então temos 2^n possibilidades de soluções porém nem todas são validas, por exemplo se o limite é 10 de peso a combinação [10,2,2] não é valida.


## **Backtracking** 

A ideia aqui é adicionar na busca uma operação chamada pruning, em que apartir do momento que sabemos que as proximas decisões independente de qual seja, vão levar a estados/soluções invalidadas, não precisamos mais explorar esse ramo e portanto cortamos ele.

- Se temos uma mochila de peso maximo 10 e o primeior item que pegamos tem peso 10, sabemos que qualquer outro item que pergarmos levará a um estado invalido.

A ideia em geral é:

1. Construir a solução incrementalmente utilizando alguma implementação de recursão que geralmente é usando DFS com pilha. Então tomamos uma decisão de cada vez por exemplo adicionar um item de cada vez na mochila

2. Verificar a viabilidade de uma solução usando as restrições da intancia do problema. Então por exemplo se a soma do peso dos objetos colocados ate agora é maior que o limite de peso, então não adicionamos mais.

3. Voltar ao ultimo estado anterior valido da arvore de soluções, ou seja, se chegamos a um estado em que não é possivel mais adicionar objetos (peso maximo) qual o ultimo estado valido não explorado que ainda podemos adicionar objetos ? voltamos para ele.

O pior caso de ambos é igual, uma vez que se todos os estados são validos acabamos passando em todos. O caso médio do backtracking é muito menor que o da força bruta.

### **Backtracking Geral**

```text
BACKTRACK(estado):
    se estado representa uma solução completa:
        registrar solução
        retornar

    para cada candidato possível:
        se candidato é viável:
            aplicar candidato
            BACKTRACK(novo estado)
            desfazer candidato
```

$$\text{Backtracking = DFS + construção incremental + poda}$$

### **Exemplos**

#### **8 Rainhas**

Problema em colocar 8 rainhas em um tabuleiro nxn sem que elas se comam

- *Solução 1 (Brute):* Testa todas combinações de escolher n posições do tabuleiro em nxn possibilidades

- *Solução 2 (Backtrack):* Representamos as posições da rainha como um vetor de tamanho n em que o indice é a coluna e o valor da posição é a linha. Sabemos então que não pode ter o mesmo valor em 2 posições então temos sempre alguma permutação do vetor [1,2,3...n] como possivel solução, ou seja, só precisamos gerar todas as permutações. Podemos verificar se estão na mesma diagonal verificando se |c1 - c2| = |l1 - l2|, diferença entre as linhas é igual a das colunas. Depois disso podemos começar o backtracking gerando todas as permutações escolhendo passando por cada coluna e decidindo qual linha colocar a rainha, se essa linha gerar um conflito voltamos na recursão.

#### **Subset Sum**

Queremos saber se existe um subconjunto dentro de um conjunto de numeros que a soma seja S.

- *Solução 1 (Brute):* Gera todos os 2^n subconjuntos, soma e compara com S.

- *Solução 2 (Backtrack):* Passamos por cada elemento do conjunto e temos a decisão de adicionar ou não ele no subconjunto. Adicionamos se a soma S' < S, caso contrário já cortamos o ramo. Se em algum momento a soma passa voltamos a maior soma registrada e não explorada da arvore. Se S' == S então chegamos a um conjunto valido. 

#### **k-coloração de grafos**

Queremos saber se conseguimos colorir um grafo com no maximo k cores. Lembrando que vizinhos n podem ter a mesma cor.

- *Solução 1 (Brute):* Gera todos possiveis grafos de tamanho n coloridos, ou seja, k^n e verifica se é valido.

- *Solução 2 (Backtrack):* Passamos por todos os n nós e decidimos entre k cores para cada um, seguimos no ramo se essa cor escolhida não é igual as dos vizinhos, se for voltamos no grafo. Concluimos quando conseguimos colorir todo mundo sem conflito.

#### **Circuito (!= caminho -> não precisa ser ciclo) Hamiltoniano**

Achar ciclo no grafo que visita todos os nós do grafo e apenas uma vez cada um.

- *Solução 1 (Brute):* Gerar todas as permutações de nós do grafo de tamanho n, ou seja, n! possibilidades e depois verificamos se o i tem aresta para i + 1 ate terminar.

- *Solução 2 (Backtrack):* Para cada nó do grafo passamos e decidimos adicionar ele ou não na solução, apenas adicionamos se:
    - Se ele não foi adicionado ainda
    - Se o nó anterior aponta para ele 
    - Se for o ultimo nó, se ele se conecta com o primeiro
Caso contrário podamos o ramo.

## **Branch and bound**

No backtracking removemos soluções invalidas em relação as restrições do problema. Aqui a ideia é remover soluções que não tem possibilidade de superar a melhor solução encontrada ate agora.

- Por exemplo, se estamos minimizando uma soma e nossa menor ate agora é 10 e sabemos por alguma estimativa que um determinado ramo vai ter no minimo 11 na solução, por mais que 11 seja valido não precisamos mais explorar aquele ramo pois ele será sempre pior que o nosso pior ate agora.

Branch (Ramificação) vem da propria arvore de soluções incrementais, enquanto bound (limite) é a estimativa de uma ramo que permite ou não podar ele.

### **Bound (limite)**

O bound de um nó v é a estimativa da melhor solução completa que pode ser gerada a partir de ramos daquele nó (valida ou não)

$$
S(v)=\{\text{soluções completas possíveis a partir de }v\}
$$

### **Problema de minimização**

Em problemas de minimização queremos encontrar a solução valida com valor minimo. Portanto, para todas as soluções possiveis S(v) precisamos de uma estimativa que seja menor ou igual a melhor solução valida de S(v) (nunca maior). Essa estimativa é chamada de lower bound (LB).

$$
LB(v)\leq \min_{s\in S(u)}f(s)
$$
Se temos:

$$
LB(u)=25
$$

Isso significa que todas as soluções descendentes de $u$ possuem custo de pelo menos 25. Se a melhor solução já encontrada tem custo 20, podemos podar esse ramo de u.


### **Problema de maximização**

Em problemas de maximização queremos encontrar a solução valida com valor maximo. Portanto, para todas as soluções possiveis S(v) precisamos de uma estimativa que seja maior ou igual a melhor solução valida de S(v) (nunca menor). Essa estimativa é chamada de upper bound (UB).

$$
UB(u)\geq \max_{s\in S(u)}f(s)
$$

Suponha que já encontramos uma solução com lucro 100.

Um ramo possui:
$$
UB(u)=80
$$
Isso significa que, mesmo no melhor cenário, esse ramo não produzirá lucro maior que 80.

### **Estimativa**

Bound tem que ser uma estimativa da qualidade daquele ramo e tem que ter algumas caracteristicas:

- *Otimista:* Se queremos minimizar ela tem que ser menor que o real, se queremos maximizar ela tem que ser maior. Isso tudo para evitar perder soluções otimas por causa da estimativa.

- *Uma aproximação barata:* quanto mais preciso essa estimativa for mais caro é para ser calculada pois nos aproximamos mais de ter que calcular a propria solução ótima e portanto menos ganho temos 

Muitas vezes temos que relaxar as restrições do problema para recriar o essa estimativa de forma barata, mas de forma que ela continue valida. A ideia é que o conjunto de soluções do relaxado é um superconjunto do não relaxado, e se a estimativa de A > B no superconjunto, não tem como ela mudar no subconjunto.

#### **Estimativa para solução parcial** 

Quando ja temos algumas decisões tomadas, ou seja, estamos descendo na arvore, a estimativa se torna:

Se já atribuímos funções às primeiras \(k\) pessoas, o bound usado na aula pode ser escrito como:
$$
LB(u)=C_{\text{parcial}}(u)+
\sum_{i=k+1}^{n}\min_j C_{ij}
$$

Onde:
- $C_{\text{parcial}}(u)$: custo das atribuições já realizadas.


### **Explorar a arvore de soluções**

Usamos uma fila de prioridade (heap) para escolher o nó para explorar, ou seja, ao inves de DFS pegamos o nó com maior estimativa para maximização e menor estimativa para minimização.

```text
BRANCH_AND_BOUND(raiz):

    best = infinito

    fila = fila_prioridades()
    fila.inserir(raiz)

    enquanto fila não estiver vazia:

        u = fila.remover_menor_bound()

        se LB(u) >= best:
            continue

        se u representa uma solução completa:
            best = custo(u)
            salvar solução
            continue

        para cada filho v de u:

            se v viola alguma restrição:
                continue

            calcular LB(v)

            se LB(v) < best:
                fila.inserir(v)

    retornar melhor solução

```

A complexidade no pior caso continua a mesma mas melhora bastante no caso medio em relação a backtracking e força bruta. A principal diferença entre esse paradigma e guloso é que esse garante o otimo não deixando soluções possiveis sem testar enquanto guloso nem sempre pois pega sempre o local.

### **Exemplos**

#### **Problema da mochila**

Queremos o subconjunto de itens que a soma do peso é menor ou igual um maximo e que maximize o valor total dos objetos

- *Solução:* Aliviamos a premissa que itens não podem ser divididos e calculamos a razão (valor/peso do item), ordenamos por ela e começamos as decisões de adicionar os itens começando dos mais valiosos. A estimativa de um nó é a soma do valor acumulado atualmente com o produto entre o espaço ainda livre com o valor/peso do proximo item mais valioso da lista ordenada, ou seja, a estimativa de v é oq já temos em v somado do que pode ser adiquirido em v com o proximo item o espaçoq que sobra.


#### **Caixeiro Viajante (ciclo hamil. com menor custo)**

Queremos o ciclo hamiltoniano com menor somatório custo 

- *Solução:* A ideia aqui estimar usando a soma das 2 menores arestas que um nó pode se conectar, uma de saida e outra de entrada, dessa forma sabemos que para aquele nó não existe melhores escolhas. Quando 2 ou mais nós ja foram escolhidos temos que atualizar a soma para utilizar as arestas que conectam eles ao invés das duas menores. Ao restante estimado pode-se usar as 2 menores normalmente pois a decisão não foi tomada ainda.


## **Algortimos aproximativos**

A ideia dos algoritmos aproximativos é abrir mão da solução otima por uma solução x% pior que o otimo. Entretanto, é necessário ter uma garantia dessa porcentagem para ele ser confiavel, ou seja, se é 20% temos que provar matematicamente que é sempre 20% pior para qualquer instancia. Sendo I alguma instancia do problema temos que provar que:

### **Minimização** 

Para minimização dizemos que ALG é no máximo c vezes maior que o otimo, ou seja, a solução vai ser sempre menor que OPT*c

$$ALG(I)\leq c\cdot OPT(I)$$

### **Maximização**

Para maximização dizemos que o ALG é c vezes menor que o otimo, ou seja, a solução vai ser sempre maior que OPT/c

$$ALG(I)\geq\frac{OPT(I)}{c}
$$

### **Como encontrar o fator de aproximação**

Para encontrar o fator de aproximação temos que usar limites matematicos:

1. Achamos um limite para a solução otima, sendo limite inferior para minimização e superior para maximização. Uma vez com esse limite, podemos definir coisas como a seguinte supondo minimização:

    - $LB\leq OPT$, ou seja o minimo otimo vai ser sempre maior que um limite LB

2. Usamos esse limite para relacionar com o algoritmo aproximativo para descobrir coisas como:

    - $ALG\leq2LB$, ou seja, o aproximativo vai ser sempre menor que 2x o limite LB

3. Concluimos coisas relacionando aproximativo e otimo

    - $ALG\leq2LB\leq2OPT$, ou seja, o aproximativo vai ser sempre no maximo 2x pior que o otimo


### **Exemplos**

#### **Vertex Cover**

O problema aqui é encontrar o menor conjunto C' de nós que tem conexão pelo menos uma vez com todas as arestas do grafo G. O algortimo aproximativo mais simples é:

1. Escolha uma aresta (a,b)
2. Adicione a e b no conjunto 
3. Remova todas as arestas que a e b tem 
4. Repita o processo

Coincidentemente o resultado desse algoritmo é um matching que é um conjunto de arestas que não compartilham extremidades. Se |M| são as arestas do matching então para ter um vertex cover é necessário pegar pelo menos um dos vértices dessas arestas e portanto |C'| >= |M|. Se |M| são arestas então cada uma tem 2 nós, logo |M| x2 >= |C|, logo |C'| x2 >= |C|, ou seja, o numero de nós dessa aproximação é sempre no maximo 2 o otimo.


#### **Caixeiro Viajante**

A solução mais simples é a usando vizinhos mais proximos em que vamos construindo pegando sempre o vizinho mais proximo do ultimo selecionado, no final temos temos uma solução. O problema é que ela pode ser pouco ruim para algumas instancias e outras muito ruim e isso não pode ser quantificado.

Quando nosso peso da aresta é uma métrica como distancia temos algumas garantias a mais como não negatividade, simetria e desigualdade triangular ($d(u,v)\leq d(u,w)+d(w,v)$). Com essas garantias é possivel criar algortmos melhores...

- **Twice-around-the-tree:** A ideia é 
    1. gerar uma arvore geradora minima T
    2. duplicar as arestas para deixar todos os nós com numeros pares 
    3. caminhar no grafo e achar um ciclo euleriano 
    4. eliminamos repetições de nós e achamos um hamiltoniano H

    Se removemos uma aresta de um ciclo hamiltoniano ele vira uma arvore geradora minima. Sabemos que a arvore geradora que criamos é minima em custo e portanto qualquer ci gerado por ela vai ser maior ou igual a ela, ou seja, c(T) <= c(H'), quando duplicamos as arestas agr c(T) vira 2c(T). Sendo c(H) o custo do ciclo descoberto pelo algortimo, sabemos que ele é <= 2c(T), logo c(H) <= 2c(H').

- **Christofides:** Usa a mesma logíca do anterior mas evita duplicar todas as arestas e sim apenas as necessárias. Isso faz com que a garantia suba de 2x para 1.5x. 

Não existe fator de aproximação para o TSP geral, apenas para a versão usando métrica que respeita a desigualdade triangular. Podemos provar por contradição:

Imagine que temos um algoritmo que funciona para o TSP geral, podemos pegar um grafo G, criar um novo grafo G' completo em que toda aresta que existia em G tinha peso 1 e toda aresta que não existia em G tem peso c|V| sendo V os vértices do grafo, ou seja, o otimo tera custo igual a |V|. Se executarmos o algortimo aproximativo e a solução retornada c(S) <= c|V| então existe solução, se c(S) >= c|V| não existe solução. O problema é que conseguimos identificar a existencia ou não do ciclo hamiltoniano em tempo polinomial e isso implica que P=NP, logo, impossivel.

#### **Balanceamento de carga**

Temos várias máquinas e queremos distribuir tarefas entre elas, minimizando o tempo que a máquina mais carregada levará para terminar. O algortimo mais simples é guloso:

- *Greedy-Balance:* Cada tarefa vai para a máquina que estiver menos carregada naquele momento.

    - Primeira: o ótimo não pode ser menor que a carga média:
    $$
    OPT\geq\frac{\sum t_j}{m}
    $$
    - Segunda: o ótimo não pode ser menor que a maior tarefa:
    $$
    OPT\geq\max t_j
    $$
    Agora considere a máquina que terminou mais carregada. Seja $t_j$ a última tarefa colocada nela.

    Sua carga final é:
    $$
    ALG=\underbrace{T_{\text{antes}}}_{\leq OPT}+
    \underbrace{t_j}_{\leq OPT}
    $$
    A carga anterior é no máximo $OPT$ porque, quando recebeu a última tarefa, essa era a máquina menos carregada.

    $$ALG\leq2OPT$$


#### **Problema dos k-centros**

Nesse problemas temos varios pontos e queremos construir k centros e cada ponto é associado ao centro mais proximo, o objetivo é minimizar as distancias entre os pontos e seus respectivos centros. No fim queremos minimizar o tamanho do maior raio/distancia entre um ponto e seu centro mais proximo.

Uma solução ingenua gulosa seria:
1. Encontrar a melhor posição para um centro 
2. Inicializar outro centro
3. Repetir o processo para os k centros

Isso pode dar errado pois com pontos [0,10], o primeiro centro cairia na posiçao 5 e o segundo em um dos 2 pontos, logo o menor raio encontrado sera 5 mas o otimo é 0.

- *Algoritmo Farthest-First:* A ideia do algortimo é bem simples, o proximo centro será posicionado sempre no ponto com maior distancia para todos os outros centro já colocados. Ele ter fator 2 pode ser provado com desigualdade triangular.


#### **Set Cover**

Set cover queremos encontrar o menor conjunto de subconjuntos que contem todos os elementos de um universo X. A solução gulosa é:

- Escolher sempre o subconjunto que contem maior quantidade de elementos desconhecidos

Ela tem um fator de aproximação ALG <= (1 + ln(X))OPT que varia com X.


### **PTAS e FPTAS**

Ate agora todos os algoritmos tinham garantias fixas e predefinidas mas e se fosse possivel parametrizar isso ? e escolhermos a qualidae desejada e o tradeoff entre custo.

#### **PTAS**

Um PTAS permite escolher $\varepsilon$ e garante, para problemas de maximização:

$$
ALG\geq(1-\varepsilon)OPT
$$

Para cada $\varepsilon$ fixo, seu tempo é polinomial no tamanho da entrada.

#### **FPTAS**

Um FPTAS faz a mesma coisa, mas seu tempo também precisa ser polinomial em:
$$
\frac1\varepsilon
$$
Por exemplo:
$$
O\left(\frac{n^3}{\varepsilon}\right)
$$
Essa é a diferença fundamental.


#### **FPTAS da mochila**


Existe a solução de programação dinâmica para a mochila com complexidade:

$$
O(nW)
$$

Onde $W$ é a capacidade da mochila. Mas e se em vez de perguntar qual valor máximo conseguimos com determinado peso, perguntamos qual o menor peso necessário para conseguir determinado valor.

Essa formulação possui complexidade:
$$
O(nV)
$$
Onde:
$$
V=\sum_i v_i
$$

Mas existe um problema: se os valores dos itens forem muito grandes, a tabela será enorme mas reduzir os valores pode ajudar 
$$
[1050,2030,3070]
$$

Podemos transformar esses valores em números menores, por exemplo:

$$
[10,20,30]
$$

A programação dinâmica fica muito mais rápida mas perdemos precisão Podemos utilizar o parâmetro $\varepsilon$

$$\mu=\frac{\varepsilon v_{\max}}n
$$
E transforma cada valor:
$$v_i'=\left\lfloor\frac{v_i}{\mu}\right\rfloor$$

Onde $v_{\max}$ é o maior valor entre os itens que cabem individualmente na mochila. Ao arredondar cada valor para baixo, perdemos menos que $\mu$ unidades por item.

O algoritmo consegue garantir:
$$ALG\geq(1-\varepsilon)OPT$$








