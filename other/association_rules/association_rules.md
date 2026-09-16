# **Regras de Associação**

Dado um conjunto de transações (ex: itens comprados juntos), o objetivo é extrair regras do tipo $X \Rightarrow Y$: se $X$ ocorre, $Y$ tende a ocorrer também. $X$ e $Y$ são **itemsets disjuntos** ($X \cap Y = \emptyset$).

| ID | Transação |
|---|---|
| 1 | {A, B, C} |
| 2 | {B, C} |


## **Métricas**

### **Suporte**
Frequência relativa com que $X \cup Y$ aparece no total de transações.

$$Support(X \Rightarrow Y) = \frac{\text{frequência}(X \cup Y)}{N}$$

Intuitivamente é o quão comum é essa combinação. Suporte baixo → combinação rara, provavelmente irrelevante.

### **Confiança**
Das transações que contêm $X$, em quantas $Y$ também aparece.

$$Confidence(X \Rightarrow Y) = \frac{Support(X \Rightarrow Y)}{Support(X)}$$

Intuitivamente é equivalente a $P(Y \mid X)$, probabilidade de $Y$ dado que $X$ está presente.

**!!!** um item que aparece em 90% das transações vai ter confiança alta com quase qualquer outro item, sem que exista relação real entre eles, só porque ele é onipresente.

### **Lift**
Quanto a presença de $X$ multiplica a chance de $Y$ aparecer, comparado a $Y$ sozinho.

$$Lift(X \Rightarrow Y) = \frac{Confidence(X \Rightarrow Y)}{Support(Y)}$$

**Intuição:** quantas vezes $P(Y \mid X)$ é maior que $P(Y)$. Métrica mais robusta que a confiança, pois se Y estiver em todas as transacoes seu suporte vai ser muito alto, gerando um lift baixo.

- **Lift > 1**: correlação positiva ($X$ favorece $Y$).
- **Lift = 1**: independência.
- **Lift < 1**: substitutos ($X$ reduz a chance de $Y$, concorrentes).

## **Como as regras são extraídas**

Processo não supervisionado em 2 etapas:

1. **Encontrar itemsets frequentes** conjuntos de itens acima de um `min_support`. Etapa cara (Apriori e FP-Growth atacam essa parte).
2. **Gerar regras** calcular confiança/lift dos itemsets frequentes e filtrar por um limiar.

### **Custo de força bruta (sem algoritmo otimizado)**

$$O(N \cdot 2^d)$$

- $N$ = número de transações.
- $d$ = número de itens distintos.
- $2^d$ = todas as combinações possíveis (power set).

Essa complexidade é devido ao fato de que se temos d itens diferentes, o numero de subconjuntos possiveis é 2^d e para cada subconjunto temos que ir no banco contar quantas vezes ele apareceu nas N transacoes. Logo, a complexidade é O(N * 2^d)

| Itens ($d$) | Combinações | Operações ($N=10^6$) | Status |
|---|---|---|---|
| 10 | 1.024 | ~1 bilhão | Segundos |
| 20 | ~1 milhão | ~1 trilhão | Minutos/horas |
| 30 | ~1 bilhão | ~1 quatrilhão | No limite |
| 50 | ~10¹⁵ | ~10²¹ | Inviável |
| 100 | ~10³⁰ | ~10³⁶ | Impossível |

O crescimento exponencial em $d$ é o motivo de existirem algoritmos como **Apriori** e **FP-Growth**.

## **Apriori**

**Princípio-chave:** se um itemset é infrequente, todo superconjunto dele também é infrequente. Se $\{A\}$ tem suporte baixo, $\{A,B\}$, $\{A,C\}$, $\{A,B,C\}$ obrigatoriamente também terão.

**Isso permite poda (pruning):** ao descartar $\{A\}$ por baixo suporte, o algoritmo elimina de antemão qualquer candidato maior que contenha $A$ como $\{A,B,C\}$, sem sequer testá-los no banco de dados.

### **Passo a passo** 

1. Conta o suporte de cada item individual, ou seja, subconjuntos de tamanho 1 ($k=1$), descarta os raros.
2. Combina os sobreviventes para formar subconjuntos candidatos de tamanho k+1 ($k+1$).
3. **Poda:** antes de testar um candidato, verifica se todos os seus subconjuntos foram frequentes no nível anterior. Se algum subconjunto foi descartado, o candidato inteiro é eliminado sem nem consultar o banco.
4. Varre o banco de dados **apenas** para contar os candidatos que sobreviveram à poda.
5. Repete aumentando $k$, até não haver mais candidatos possíveis ou nenhum atingir o suporte mínimo.

### **Gargalos**

- **Múltiplos scans:** achar um padrão de tamanho 10 exige ler o banco 10 vezes.
- **Explosão de candidatos:** 10.000 itens frequentes geram ~50 milhões de candidatos de tamanho 2 — risco de estourar a memória.

## **FP-Growth**
 
Mesmo objetivo do Apriori (itemsets frequentes por `min_support`), mas em vez de gerar candidatos e checá-los no banco, ele **compacta todas as transações numa única árvore em memória (FP-Tree)** e extrai os itemsets frequentes direto da estrutura dessa árvore.
 
### **A ideia de compressão, com exemplo**
 
Pense em 5 transações, já filtradas (itens raros descartados) e ordenadas pelo suporte de cada item (A ≥ B ≥ C):
 
| Transação | Itens |
|---|---|
| T1 | A, B, C |
| T2 | B, C |
| T3 | A, B |
| T4 | A, B, C |
| T5 | A, C |
 
Ao inserir essas transações numa árvore, **transações que começam igual compartilham o mesmo galho**, e só se separam no ponto em que realmente diferem:
 
- T1, T3, T4 e T5 começam com A → todas entram por um **único nó A**, que acumula contagem 4 (em vez de existirem 4 nós A separados).
- Dessas, T1, T3 e T4 também têm B → o galho se divide em A→B (contagem 3).
- De A→B, só T1 e T4 têm C → A→B→C (contagem 2); T3 para em A→B.
- T5 (A, C, sem B) precisa de um caminho próprio: A→C.
- T2 (B, C, sem A) não cabe em nenhum galho de A, então cria um segundo galho a partir da raiz: B→C.

**Por que isso comprime:** em vez de guardar as 5 transações inteiras lado a lado, a árvore guarda cada prefixo repetido **uma única vez**, com um contador, o padrão comum a várias transações vira um único caminho, não cinco.
 
### **Por que isso reduz as leituras do banco a 2**
 
- **1º scan:** conta a frequência de cada item, descarta os raros, ordena os sobreviventes por suporte (tabela `T`), precisa ler o banco uma vez para saber essa ordem (Essa tabela nos da a ordenacao por suporte de todos os subconjuntos de tamanho 1 (itens)).
- **2º e último scan:** insere cada transação (já filtrada e ordenada por `T`) na árvore, como no exemplo acima.
A partir daqui, **o banco de dados nunca mais é consultado**: toda a mineração, encontrar itemsets de tamanho 2, 3, 4... acontece só navegando pela árvore que já está pronta em memória. 
 
### **Como a mineração usa a árvore (sem gerar candidatos)**
 
Para achar tudo que aparece junto com um item `i`, basta **somar os caminhos da raiz até os nós `i`**, ponderados pela contagem de cada caminho. No exemplo acima, para minerar o item C: os caminhos que terminam em C são A→B→C (peso 2), A→C (peso 1) e B→C (peso 1). Somando por item: A aparece com C em `2+1=3` transações, B aparece com C em `2+1=3`, a árvore já entrega essas contagens prontas, sem precisar reler nada.
 
**O processo é recursivo:** começa pelo item de menor suporte em `T`, monta uma **árvore condicional** só com os caminhos e itens relevantes para ele, filtra pelo `min_support`, e repete a mineração dentro dessa árvore menor (agora condicionada a mais de um item), sempre navegando em memória, nunca voltando ao disco.
