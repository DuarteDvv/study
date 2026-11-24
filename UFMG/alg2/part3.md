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

- *Solução 2 (Backtrack):* Construímos o caminho um nó de cada vez: a cada passo escolhemos o próximo nó entre os candidatos, apenas adicionamos se:
    - Se ele não foi adicionado ainda
    - Se o nó anterior aponta para ele 
    - Se for o ultimo nó, se ele se conecta com o primeiro
Caso contrário podamos o ramo.

## **Branch and bound**

No backtracking removemos soluções invalidas em relação as restrições do problema. Aqui a ideia é remover soluções que não tem possibilidade de superar a melhor solução encontrada ate agora.

- Por exemplo, se estamos minimizando uma soma e nossa menor ate agora é 10 e sabemos por alguma estimativa que um determinado ramo vai ter no minimo 11 na solução, por mais que 11 seja valido não precisamos mais explorar aquele ramo pois ele será sempre pior que o nosso melhor ate agora.

Branch (Ramificação) vem da propria arvore de soluções incrementais, enquanto bound (limite) é a estimativa de uma ramo que permite ou não podar ele.

### **Bound (limite)**

O bound de um nó v é a estimativa da melhor solução completa que pode ser gerada a partir de ramos daquele nó (valida ou não)

$$
S(v)=\{\text{soluções completas possíveis a partir de }v\}
$$

### **Problema de minimização**

Em problemas de minimização queremos encontrar a solução valida com valor minimo. Portanto, para todas as soluções possiveis S(v) precisamos de uma estimativa que seja menor ou igual a melhor solução valida de S(v) (nunca maior). Essa estimativa é chamada de lower bound (LB).

$$
LB(v)\leq \min_{s\in S(v)}f(s)
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

- *Solução:* A ideia aqui estimar usando a soma das 2 menores arestas que um nó pode se conectar, uma de saida e outra de entrada, dessa forma sabemos que para aquele nó não existe melhores escolhas. Quando 2 ou mais nós ja foram escolhidos temos que atualizar a soma para utilizar as arestas que conectam eles ao invés das duas menores. Ao restante estimado pode-se usar as 2 menores normalmente pois a decisão não foi tomada ainda. Como cada aresta do ciclo é contada duas vezes (uma em cada extremidade), dividimos a soma por 2:
$$
LB=\left\lceil\frac{1}{2}\sum_{v\in V}(\text{soma das 2 menores arestas de }v)\right\rceil
$$


## **Programação Dinâmica**

Backtracking e branch and bound cortam ramos da arvore de soluções, mas em muitos problemas a arvore tem outro desperdicio: o mesmo subproblema aparece em vários ramos diferentes e é resolvido de novo toda vez. A ideia da programação dinâmica (PD) é resolver cada subproblema uma unica vez, guardar a resposta em uma tabela e reutilizar.

- Na mochila, decidir os itens 3..n com 7 de capacidade livre é o mesmo subproblema, não importa se chegamos nele pegando o item 1 (peso 3) ou o item 2 (peso 3). A força bruta resolve ele 2 vezes, a PD uma.

Para PD funcionar o problema precisa ter 2 propriedades:

1. *Subestrutura ótima:* a solução ótima do problema é formada por soluções ótimas de subproblemas menores. Se a mochila ótima com os itens 1..i pega o item i, então o resto dela é a mochila ótima com os itens 1..i-1 e capacidade W - w_i.

2. *Sobreposição de subproblemas:* os mesmos subproblemas se repetem muitas vezes na recursão. Se não se repetem (como em merge sort, onde cada metade é diferente) é só divisão e conquista e guardar resultados não ajuda.

### **Como montar uma PD**

1. *Definir o estado:* quais parâmetros identificam um subproblema, por exemplo K[i][c] = melhor valor usando os itens 1..i com capacidade c. Essa é a parte mais dificil e a mais importante.

2. *Escrever a recorrência:* como a resposta de um estado depende de estados menores. Geralmente é olhar a ultima decisão (pegar ou não o item i) e escolher a melhor.

3. *Casos base:* os estados triviais, por exemplo K[0][c] = 0 (sem itens não tem valor).

4. *Ordem de preenchimento:* preencher a tabela de forma que quando calculamos um estado, os estados que ele usa já estão prontos.

5. *Reconstrução:* a tabela guarda apenas o valor ótimo, para saber quais decisões levaram a ele voltamos na tabela a partir da resposta verificando qual opção da recorrência foi usada.

Existem duas formas de implementar:

- *Top-down (memoização):* escrevemos a recursão normal do backtracking mas antes de calcular um estado verificamos se ele já está na tabela. Só calcula os estados que realmente aparecem.

- *Bottom-up (tabulação):* preenchemos a tabela iterativamente dos casos base até a resposta. Não tem custo de recursão e é mais facil economizar memoria.

### **PD Geral**

```text
PD_TOP_DOWN(estado):
    se estado é caso base:
        retornar valor base

    se memo[estado] já foi calculado:
        retornar memo[estado]

    melhor = pior valor possivel
    para cada decisão possível em estado:
        melhor = melhor_entre(melhor, custo(decisão) + PD_TOP_DOWN(subestado(decisão)))

    memo[estado] = melhor
    retornar melhor
```

$$\text{Programação Dinâmica = recursão + memoização (ou tabela)}$$

A complexidade é dada por:

$$
O(\text{número de estados}\times\text{custo de cada transição})
$$

Diferente de backtracking e branch and bound, que no pior caso continuam exponenciais, a PD tem complexidade garantida, mas ela pode ser *pseudo-polinomial*: a mochila O(nW) depende do valor numérico de W e não do tamanho da entrada. W é escrito com log W bits, então se W = 2^n a tabela tem tamanho exponencial no tamanho da entrada. Por isso a mochila continua NP-difícil mesmo com PD, e é por isso que o FPTAS precisa reduzir os valores.

### **Exemplos**

#### **Fibonacci**

Calcular o n-ésimo número de Fibonacci F(n) = F(n-1) + F(n-2).

- *Solução 1 (Recursão ingênua):* F(5) chama F(4) e F(3), F(4) chama F(3) de novo e F(2)... cada chamada gera duas, então são O(2^n) chamadas para apenas n subproblemas distintos.

- *Solução 2 (PD):* guardamos F(i) assim que é calculado. O estado é só i, são n estados e cada um custa O(1), logo O(n). No bottom-up nem precisamos da tabela toda, apenas dos 2 ultimos valores.

#### **Subset Sum**

Queremos saber se existe um subconjunto dentro de um conjunto de numeros (inteiros positivos) que a soma seja S.

- *Solução 1 (Brute/Backtrack):* gera os 2^n subconjuntos, podando quando a soma passa de S.

- *Solução 2 (PD):* o estado é D[i][s] = existe subconjunto dos itens 1..i com soma exatamente s? Olhando a ultima decisão, ou não pegamos o item i ou pegamos:
$$
D[i][s]=D[i-1][s]\;\lor\;D[i-1][s-a_i]\quad(\text{se }s\geq a_i)
$$
Com D[0][0] = verdadeiro e D[0][s] = falso para s > 0. São n·S estados com transição O(1), logo O(nS).

#### **Problema da mochila (0/1)**

Queremos o subconjunto de itens que a soma do peso é menor ou igual a W e que maximize o valor total.

- *Solução 1 (Brute):* 2^n subconjuntos.

- *Solução 2 (PD por peso):* o estado é K[i][c] = maior valor usando os itens 1..i com capacidade c.
$$
K[i][c]=\max\big(\underbrace{K[i-1][c]}_{\text{não pega }i},\;\underbrace{v_i+K[i-1][c-w_i]}_{\text{pega }i,\text{ se }w_i\leq c}\big)
$$
Com K[0][c] = 0. São n·W estados com transição O(1), logo O(nW). A resposta é K[n][W].

Exemplo com W = 5 e itens (peso, valor): 1 = (2, 3), 2 = (3, 4), 3 = (4, 5).

| i \ c | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---:|---:|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 | 0 | 0 | 3 | 3 | 3 | 3 |
| 2 | 0 | 0 | 3 | 4 | 4 | **7** |
| 3 | 0 | 0 | 3 | 4 | 5 | **7** |

Por exemplo K[2][5] = max(K[1][5], 4 + K[1][2]) = max(3, 4 + 3) = 7. Para reconstruir: K[3][5] = K[2][5], então o item 3 não foi pego; K[2][5] ≠ K[1][5], então o item 2 foi pego e vamos para K[1][2]; K[1][2] ≠ K[0][2], então o item 1 foi pego. Solução {1, 2} com valor 7.

- *Solução 3 (PD por valor):* invertemos a pergunta, o estado é P[i][x] = menor peso necessario para conseguir valor exatamente x usando os itens 1..i.
$$
P[i][x]=\min\big(P[i-1][x],\;w_i+P[i-1][x-v_i]\big)
$$
Com P[0][0] = 0 e P[0][x] = ∞ para x > 0. A resposta é o maior x tal que P[n][x] <= W. Complexidade O(nV) com V = Σv_i. Essa é a versão usada no FPTAS da mochila.

#### **Caixeiro Viajante (Held-Karp)**

Queremos o ciclo hamiltoniano com menor somatório de custo.

- *Solução 1 (Brute):* testa as (n-1)! ordens de visita.

- *Solução 2 (PD):* o que importa para continuar um caminho parcial não é a ordem em que os nós foram visitados, apenas quais foram visitados e onde estamos agora. O estado é C(S, j) = menor custo de um caminho que sai do nó 1, visita exatamente os nós de S e termina em j ∈ S.
$$
C(S,j)=\min_{i\in S\setminus\{j\},\,i\neq1}\big(C(S\setminus\{j\},i)+d(i,j)\big)
$$
Com C({1, j}, j) = d(1, j). A resposta é fechar o ciclo:
$$
OPT=\min_{j\neq1}\big(C(V,j)+d(j,1)\big)
$$
São 2^n·n estados com transição O(n), logo O(n²2^n). Continua exponencial, mas é muito menor que n! (para n = 20: ~4·10^8 contra ~10^17).


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

Coincidentemente o resultado desse algoritmo é um matching que é um conjunto de arestas que não compartilham extremidades. Seja C* a cobertura ótima e C o conjunto retornado pelo algoritmo. Como as arestas de M não compartilham extremidades, qualquer vertex cover (inclusive o ótimo) precisa de pelo menos um vértice distinto para cada aresta de M, portanto |C*| >= |M|. O algoritmo adiciona exatamente os 2 nós de cada aresta de M, logo |C| = 2|M| <= 2|C*|, ou seja, o numero de nós dessa aproximação é sempre no maximo 2 vezes o otimo.


#### **Caixeiro Viajante**

A solução mais simples é a usando vizinhos mais proximos em que vamos construindo pegando sempre o vizinho mais proximo do ultimo selecionado, no final temos temos uma solução. O problema é que ela pode ser pouco ruim para algumas instancias e outras muito ruim e isso não pode ser quantificado.

Quando nosso peso da aresta é uma métrica como distancia temos algumas garantias a mais como não negatividade, simetria e desigualdade triangular ($d(u,v)\leq d(u,w)+d(w,v)$). Com essas garantias é possivel criar algortmos melhores...

- **Twice-around-the-tree:** A ideia é 
    1. gerar uma arvore geradora minima T
    2. duplicar as arestas para deixar todos os nós com numeros pares 
    3. caminhar no grafo e achar um ciclo euleriano 
    4. eliminamos repetições de nós e achamos um hamiltoniano H

    Seja H' o ciclo hamiltoniano ótimo. Se removemos uma aresta de H' ele vira uma arvore geradora (não necessariamente minima). Como T é a arvore geradora de menor custo, c(T) <= c(H' - e) <= c(H'). Quando duplicamos as arestas, o percurso euleriano custa 2c(T). Ao eliminar nós repetidos pegamos "atalhos" (u -> w no lugar de u -> v -> w), e pela desigualdade triangular isso nunca aumenta o custo. Sendo c(H) o custo do ciclo descoberto pelo algortimo, c(H) <= 2c(T) <= 2c(H').

- **Christofides:** Usa a mesma logíca do anterior mas evita duplicar todas as arestas e sim apenas as necessárias. Isso faz com que a garantia suba de 2x para 1.5x. 

Não existe fator de aproximação para o TSP geral, apenas para a versão usando métrica que respeita a desigualdade triangular. Podemos provar por contradição:

Imagine que temos um algoritmo c-aproximativo para o TSP geral, podemos pegar um grafo G, criar um novo grafo G' completo em que toda aresta que existia em G tem peso 1 e toda aresta que não existia em G tem peso c|V| + 1 sendo V os vértices do grafo.

- Se G tem ciclo hamiltoniano, o otimo tera custo igual a |V|, e o aproximativo retorna c(S) <= c|V|.
- Se G não tem, qualquer ciclo em G' usa pelo menos uma aresta pesada, logo c(S) > c|V|.

Olhando apenas o custo retornado conseguimos identificar a existencia ou não do ciclo hamiltoniano (NP-completo) em tempo polinomial, o que implicaria P=NP. Logo, esse algoritmo não existe a menos que P=NP.

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
    A carga anterior é no máximo $OPT$ porque, quando recebeu a última tarefa, essa era a máquina menos carregada, e a menor carga é no máximo a média: $T_{\text{antes}}\leq\frac{\sum t_j}{m}\leq OPT$. Já a última tarefa satisfaz $t_j\leq\max t_j\leq OPT$.

    $$ALG\leq2OPT$$


#### **Problema dos k-centros**

Nesse problemas temos varios pontos e queremos construir k centros e cada ponto é associado ao centro mais proximo, o objetivo é minimizar as distancias entre os pontos e seus respectivos centros. No fim queremos minimizar o tamanho do maior raio/distancia entre um ponto e seu centro mais proximo.

Uma solução ingenua gulosa seria:
1. Encontrar a melhor posição para um centro 
2. Inicializar outro centro
3. Repetir o processo para os k centros

Isso pode dar errado pois com pontos [0,10], o primeiro centro cairia na posiçao 5 e o segundo em um dos 2 pontos, logo o menor raio encontrado sera 5 mas o otimo é 0.

- *Algoritmo Farthest-First:* A ideia do algortimo é bem simples, o proximo centro será posicionado sempre no ponto cuja distancia ao seu centro mais proximo (entre os já colocados) é a maior possivel (max-min). Ele ter fator 2 pode ser provado com desigualdade triangular.


#### **Set Cover**

Set cover queremos encontrar o menor conjunto de subconjuntos que contem todos os elementos de um universo X. A solução gulosa é:

- Escolher sempre o subconjunto que contem maior quantidade de elementos ainda não cobertos

Ela tem um fator de aproximação ALG <= (1 + ln(X))OPT que varia com X.


#### **Hitting Set**

Nesse problema temos subconjuntos de um universo X, queremos encontrar o menor conjunto C tal que C tenha pelo menos um representante de cada grupo. 

Podemos transformar nosso problema em um set cover.

- Passo 1: cada hiperaresta do Hitting Set se torna um elemento do universo do Set Cover.
$$
X=\{S_1,S_2,\ldots,S_m\}
$$
Onde m=|E|

Passo 2: para cada vértice $v\in V$, construímos um conjunto:
$$
C_v=\{S_i\in E:v\in S_i\}
$$
Ou seja, $C_v$ contém todas as hiperarestas que possuem o vértice $v$

No exemplo, com $V=\{a,b,c,d\}$ e hiperarestas $S_1=\{a,b\}$, $S_2=\{b,c\}$, $S_3=\{c,d\}$:
$$
C_a=\{S_1\}
$$
$$
C_b=\{S_1,S_2\}
$$
$$
C_c=\{S_2,S_3\}
$$
$$
C_d=\{S_3\}
$$

Agora temos uma instância de Set Cover! Escolher um conjunto $C_v$ significa selecionar o vértice $v$ no Hitting Set. Além disso, cobrir todo o universo $X$ equivale a atingir todas as hiperarestas

#### **Subset Sum versão de otimização (maior soma <= B)** 

O problema do subset sum para otimização consistem em achar o subconjunto que a soma seja a maior possivel mas não ultrapasse uma valor limite B. O de decisão queria exatamente B, enquanto esse não.

- O algoritmo aproximativo aqui consiste em ordenar os itens em ordem decrescente, e ir alocando do maior para os menores, adicionamos no conjunto se somar esse item não ultrapassa B, se ultrapassar pulamos ele e continuamos ate os itens acabarem.

- *Fator 2:* se todos os itens couberem, a solução é ótima. Senão, seja $a_j$ o primeiro item rejeitado. A soma $S$ já contém $a_1\geq a_j$, logo $S\geq a_j$, e como $a_j$ não coube, $S+a_j>B\geq OPT$. Então $2S>OPT$, ou seja, $ALG\geq OPT/2$.

#### **Conjunto independente maximo**

O problema consiste em encontrar o maior cojunto de nós tal que nenhum deles tenha arestas um para o outro. 

- O algortimo aproximativo é bem parecido com o de vertex cover mas ao invés ao invés de escolhermos uma aresta e removermos ela, tiramos um nó e todos os seus vizinhos ate não sobre nada no grafo.

- *Cuidado:* em grafos gerais esse problema não tem fator de aproximação constante. Se o grafo tem grau máximo $\Delta$, cada nó escolhido remove no máximo $\Delta+1$ nós, então $ALG\geq\frac{n}{\Delta+1}\geq\frac{OPT}{\Delta+1}$.

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


Existe a solução de programação dinâmica para a mochila (ver seção de Programação Dinâmica) com complexidade:

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

### **Exemplo**

Transformando os valores pegando o piso de valor original / $\mu$ com $\mu$ = 1,92k, a qualidade da solução é dada por $\mu = \varepsilon \cdot v_{max} / n$ -> $\varepsilon = \mu \cdot n / v_{max}$ = 1,92k · 4 / 12,8k = 0.6, ou seja, a solução aqui será ALG >= 0.4 OPT, encontrara pelo menos 40% do otimo.

| Item | Valor $v_i$ | Valor transformado $v_i'$ | Peso $w_i$ |
|---|---:|---:|---:|
| 1 | 5,5k | 2 | 2t |
| 2 | 12,8k | 6 | 5t |
| 3 | 3,5k | 1 | 1t |
| 4 | 6,25k | 3 | 3t |

Os valores transformados não têm unidade, são "quantos $\mu$ cabem no valor", por exemplo $\lfloor 12,8k / 1,92k\rfloor = \lfloor 6,67\rfloor = 6$.

Dado que a capacidade é W=9t, usamos a PD por valor (ver Programação Dinâmica) sobre os valores transformados:

$$
P[i][x]=\min\big(P[i-1][x],\;w_i+P[i-1][x-v_i']\big)
$$

$P[i][x]$ é o menor peso para conseguir valor transformado exatamente $x$ usando os itens 1..i. A soma dos valores transformados é $V' = 2+6+1+3 = 12$, então $x$ vai de 0 a 12.

**Passo 0 (nenhum item):** só é possivel ter valor 0 com peso 0, todo o resto é impossivel (∞).

| i \ x | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 0 | 0 | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ |

**Passo 1 (item 1: $v'=2$, $w=2$):** cada célula copia a linha de cima (não pega o item) ou soma $w=2$ à célula 2 posições para trás na linha de cima (pega o item).
- $P[1][2]=\min(P[0][2],\;2+P[0][0])=\min(∞,\;2)=2$
- Todas as outras ficam iguais à linha 0, pois $P[0][x-2]=∞$ para $x\neq2$.

**Passo 2 (item 2: $v'=6$, $w=5$):** olhamos 6 posições para trás na linha 1.
- $P[2][6]=\min(∞,\;5+P[1][0])=5$ (só item 2)
- $P[2][8]=\min(∞,\;5+P[1][2])=5+2=7$ (itens 1 e 2)
- O resto copia a linha 1.

**Passo 3 (item 3: $v'=1$, $w=1$):** olhamos 1 posição para trás na linha 2.
- $P[3][1]=\min(∞,\;1+P[2][0])=1$
- $P[3][2]=\min(2,\;1+P[2][1])=\min(2,\;∞)=2$
- $P[3][3]=\min(∞,\;1+P[2][2])=3$
- $P[3][6]=\min(5,\;1+P[2][5])=\min(5,\;∞)=5$
- $P[3][7]=\min(∞,\;1+P[2][6])=6$
- $P[3][8]=\min(7,\;1+P[2][7])=\min(7,\;∞)=7$
- $P[3][9]=\min(∞,\;1+P[2][8])=8$
- $x=4,5,10,11,12$ continuam ∞.

**Passo 4 (item 4: $v'=3$, $w=3$):** olhamos 3 posições para trás na linha 3.
- $P[4][3]=\min(3,\;3+P[3][0])=\min(3,\;3)=3$
- $P[4][4]=\min(∞,\;3+P[3][1])=4$
- $P[4][5]=\min(∞,\;3+P[3][2])=5$
- $P[4][6]=\min(5,\;3+P[3][3])=\min(5,\;6)=5$
- $P[4][7]=\min(6,\;3+P[3][4])=\min(6,\;∞)=6$
- $P[4][8]=\min(7,\;3+P[3][5])=\min(7,\;∞)=7$
- $P[4][9]=\min(8,\;3+P[3][6])=\min(8,\;8)=8$
- $P[4][10]=\min(∞,\;3+P[3][7])=9$
- $P[4][11]=\min(∞,\;3+P[3][8])=10$
- $P[4][12]=\min(∞,\;3+P[3][9])=11$
- $x=0,1,2$ copiam a linha 3.

**Tabela final:**

| i \ x | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 0 | 0 | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ |
| 1 | 0 | ∞ | 2 | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ |
| 2 | 0 | ∞ | 2 | ∞ | ∞ | ∞ | 5 | ∞ | 7 | ∞ | ∞ | ∞ | ∞ |
| 3 | 0 | 1 | 2 | 3 | ∞ | ∞ | 5 | 6 | 7 | 8 | ∞ | ∞ | ∞ |
| 4 | 0 | 1 | 2 | 3 | 4 | 5 | 5 | 6 | 7 | 8 | **9** | 10 | 11 |

**Resposta:** procuramos na ultima linha o maior $x$ com $P[4][x]\leq W=9$. $P[4][12]=11$ e $P[4][11]=10$ passam do limite, $P[4][10]=9$ cabe. Logo o melhor valor transformado é 10.

**Reconstrução:** voltamos de $P[4][10]$ verificando qual opção do min foi usada:
- $P[4][10]=9\neq P[3][10]=∞$, então o item 4 foi pego, vamos para $P[3][10-3]=P[3][7]=6$
- $P[3][7]=6\neq P[2][7]=∞$, então o item 3 foi pego, vamos para $P[2][7-1]=P[2][6]=5$
- $P[2][6]=5\neq P[1][6]=∞$, então o item 2 foi pego, vamos para $P[1][6-6]=P[1][0]=0$
- $P[1][0]=P[0][0]=0$, então o item 1 não foi pego

O conjunto é {2, 3, 4}: peso 5 + 1 + 3 = 9t e valor real 12,8k + 3,5k + 6,25k = 22,55k. Testando os subconjuntos que cabem em 9t, esse também é o ótimo real (o segundo melhor é {1, 2, 3} com 21,8k), bem acima da garantia de 40%.












