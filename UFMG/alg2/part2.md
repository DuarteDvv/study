# **Analise de complexidade**

Dentre os problema computaveis, ou seja, existe um algortimo (sequencia finita de passos) que resolve o problema nem todos são trataveis uma vez que um algortimo com complexidade de tempo O(2^n) deixa de ser util rapidamente enquanto n cresce com nossos computadores atuais.

*Problema x Instancia:*

- Um problema é algo generalizado para qualquer entrada existente N (X é primo ?)
- Uma instancia de um problema é quando colocamos uma valor especifico de entrada (44 é primo ?)

As classes abaixo são definidas para **problemas de decisão**, ou seja, problemas com resposta sim/não. Problemas de otimização viram problemas de decisão adicionando um limite: "qual o menor caminho?" vira "existe caminho de custo ≤ k?".

## **Classe P** 

A classe P contém os problemas que podem ser resolvidos por um algortimo em tempo polinomial, ou seja, esses são os problemas computaveis trataveis.

- Um exemplo é "existe caminho entre os vértices s e t?" (BFS em O(V + E)) ou "este vetor tem elementos repetidos?" (ordenar e comparar vizinhos em O(nlogn))

## **Classe NP**

Um problema está em NP se, dada uma solução candidata, *podemos* verificar em tempo polinomial se ela é válida. Mais precisamente: se a resposta é **sim**, existe um **certificado** (a solução candidata) de tamanho polinomial que prova isso e pode ser verificado em tempo polinomial. È intuitivo concluir que todos os problemas que estão P também estão em NP (P contido em NP) pois se resolvemos o problema em P então com certeza verificamos uma solução em P. A grande pergunta é se NP também esta contido em P, ou seja, P = NP, isso não sabemos e é um problema em aberto.

- Um exemplo é o problema do clique que dado um grafo G e um número k, queremos saber se existe uma clique de tamanho k? Uma clique é um conjunto de vértices em que todo vértice está conectado a todos os demais. Não se conhece algoritmo polinomial que resolva para qualquer entrada (mas também não foi provado que é impossível, essa é justamente a pergunta P = NP) mas dado uma solução candidata conseguimos verificar em tempo quadratico comparando todos com todos. A mesma logica se aplica ao subset sum pois se recebermos um conjunto de numeros conseguimos verificar facilmente se a soma é igual a S.

## **Classe co-NP**

co-NP são os problemas cujo **complemento está em NP**. Ou seja, é o "espelho" de NP: se a resposta é **não**, existe um certificado curto que prova isso e pode ser verificado em tempo polinomial.

- Um exemplos seria o complemento do clique que é verificar se "NÃO existe clique de tamanho k ?". Se a resposta for **não** (existe uma clique), basta mostrar a clique para provar, e isso é verificado em tempo polinomial. Já se a resposta for **sim** (não existe nenhuma), não se conhece um certificado curto: receber um candidado não nos diz que dentre as outras combinações possiveis nenhuma delas é clique, apenas diz que aquela não é.

Cuidado: co-NP **não** é "o que fica fora de NP". Não se sabe se existem problemas de co-NP fora de NP (a pergunta NP = co-NP está em aberto), e P está dentro dos dois (P ⊆ NP ∩ co-NP), já que um problema resolvido em tempo polinomial pode ter a resposta sim ou não verificada simplesmente resolvendo de novo.

```text
 ┌──────────── NP ────────────┐
 │                ┌───────────┼──────────── co-NP ─┐
 │  Clique, 3SAT  │     P     │  ¬Clique, ¬3SAT    │
 │                └───────────┼────────────────────┘
 └────────────────────────────┘

 P ⊆ NP ∩ co-NP   (não se sabe se P = NP ∩ co-NP)
 P = NP?          → em aberto
 NP = co-NP?      → em aberto
```

## **Classe NP-Completo**

Todos os problemas de NP tem a mesma dificuldade ? Essa é uma pergunta importante que abre espaço para um conceito chamado redução de problemas

### **Redução de problemas (Problemas de decisão Sim/Não)**

A ideia de redução de um problema A para um problema B consiste na construição de uma função F() tal que ela receba a entrada para o problema A e retorne uma entrada para o problema B, ou seja, F(in(A)) -> in(B) tal que:

$$
A(x) = \text{sim} \iff B(F(x)) = \text{sim}
$$

É um **se e somente se**, por isso toda prova de redução tem uma ida e uma volta. Isso pode ser utilizado tanto teoricamente para provar coisas mas também na pratica: se temos um algoritmo para B, resolvemos A transformando a entrada com F e rodando o algoritmo de B. Essa redução é considerada polinomial (A ≤p B) se a função F é polinomial. Além disso, se B tem solução polinomial e A pode ser convertido em B então A também é polinomial.

**Direção da redução:** A ≤p B significa que **B é pelo menos tão difícil quanto A**.

- Se B ∈ P, então A ∈ P (resolve A usando B)
- Se A é NP-difícil, então B é NP-difícil

Portanto, para provar que um problema novo B é difícil, reduzimos **um problema já conhecido como difícil para B** (conhecido → novo), nunca o contrário. Reduzir B → 3SAT só mostraria que B não é mais difícil que 3SAT, o que não prova nada sobre a dificuldade de B.

- *Exemplo convertendo 3SAT para clique:*

    3SAT consiste no problema de dado uma expressão booleana na forma fórmula lógica escrita como uma conjunção/produto (E / ∧) de cláusulas, onde cada cláusula é uma disjunção/somas (OU / ∨) de literais (variáveis ou suas negações), responder se existe combinação de valores 0 e 1 para as variaveis que satisfaz a expressão (expressão = 1). Clique consiste em descobrir se existe um subgrafo completo de tamanho k dentro do grafo original. (produto de somas de tamanho 3)

    - A entrada de 3SAT é um conjunto de variaveis V que assumem valores 0 ou 1 e a expressão
    - A entrada do Clique é um grafo  

    Isso pode ser feito criando um vértice para cada literal/variavel da expressão (repetidas tem diferentes) e depois uma aresta para os pares de vértices que são de **cláusulas** diferentes e não são o inverso um do outro (x e ¬x). Usamos como k do clique o **número de cláusulas** da expressão (o "3" do 3SAT é o número de literais por cláusula, não entra no k).

    Depois temos que mostrar que sempre que quando 3SAT é sim (satisfazivel) existe uma clique de tamanho k nesse grafo e o contrário também:

    *3SAT -> Clique (ida):* Se o 3SAT é sim então para cada cláusula existe pelo menos um literal igual a 1, e escolhemos um deles por cláusula. Dentre esses literais escolhidos nenhum pode ser igual ao negativo do outro, pois uma atribuição não torna x e ¬x verdadeiros ao mesmo tempo. Como estão em cláusulas diferentes e não se contradizem, eles têm arestas entre todos os pares e portanto formam um subgrafo completo de tamanho k (um vértice por cláusula).

    *Clique -> 3SAT (volta):* Se o grafo gerado tem um subgrafo completo de tamanho k, como não existem arestas entre literais da mesma cláusula, a clique tem **exatamente um vértice por cláusula**. Além disso, nenhum par da clique é x e ¬x. Então podemos tornar verdadeiros todos os literais da clique sem contradição (as variáveis que não aparecem recebem qualquer valor), e essa atribuição satisfaz todas as cláusulas.

    *Exemplo:* φ = (x1 ∨ x2 ∨ ¬x3) ∧ (¬x1 ∨ ¬x2 ∨ x3), com 2 cláusulas, então k = 2.

    ```text
    cláusula 1:   x1     x2    ¬x3
    cláusula 2:  ¬x1    ¬x2     x3

    arestas: x1–¬x2, x1–x3, x2–¬x1, x2–x3, ¬x3–¬x1, ¬x3–¬x2
    (só faltam os pares contraditórios x1–¬x1, x2–¬x2, ¬x3–x3)
    ```

    A clique {x1, ¬x2} dá a atribuição x1 = 1, x2 = 0, que satisfaz as duas cláusulas.


### **NP-Completo**

Para um problema B ser considerado nessa classe ele tem que:

1. B ser NP (Verificador polinomial)
2. Para todo problema A dentro de NP, B tem que ser pelo menos tão dificil que eles (A <= B). Isso é chamado de NP-dificil, repare que para ser NP-hard não é necessário ser NP (problema da parada) mas para ser NP-complete é necessário ser NP-dificil. Para mostrar que é NP-Hard precisamos de 3 coisas:
    - Transformação polinomial: converter uma instância de A em uma de B em tempo polinomial.
    - Ida ($A\Rightarrow B$): se a instância de A tem resposta SIM, então a instância transformada de B também tem resposta SIM.
    - Volta ($B\Rightarrow A$): se a instância transformada de B tem resposta SIM, então a instância original de A também tem resposta SIM.

- Então se A é NP-completo e B é NP, se A pode ser convertido polinomialmente para B então B também é NP-completo. Provar o item 2 diretamente para todo problema de NP seria inviável, mas como a redução é transitiva (se C ≤p A e A ≤p B então C ≤p B), basta **uma** redução a partir de um problema já conhecido como NP-completo.
- O primeiro NP-completo, provado diretamente e não por redução, vem do teorema de Cook-Levin (Cook em 1971, Levin de forma independente em 1973), que mostra que SAT é NP-completo. A prova codifica a execução de qualquer máquina de Turing não determinística em tempo polinomial como uma fórmula booleana, que é satisfazível se e somente se a máquina aceita.

A partir do SAT se monta uma árvore de reduções (as que aparecem nestas notas):

```text
SAT ──(Cook-Levin)
 └─▶ 3SAT ─┬─▶ Clique ──▶ Vertex Cover ─┬─▶ Dominating Set
           │                            └─▶ Set Cover
           └─▶ 3-Coloração
```

## **Análise de espaco (PSPACE)**

Classificamos os problemas computacionais principalmente pelo tempo necessário para resolvê-los. Entretanto, tempo não é o único recurso importante: também podemos analisar a quantidade de memória (espaço) utilizada por um algoritmo. Um algoritmo pode precisar de tempo exponencial para resolver um problema, mas utilizar apenas espaço polinomial. Isso acontece porque a memória pode ser reutilizada durante a execução, diferentemente do tempo.

### **Classe PSPACE e NPSPACE**

Assim como definimos a complexidade de tempo O(f(n)), podemos definir a complexidade de espaço considerando quantas células de memória uma Máquina de Turing utiliza durante a computação. Dizemos que um problema A pertence a SPACE(f(n)) se existe um algoritmo determinístico que resolve A utilizando espaço O(f(n)).

- *PSPACE:* conjunto de problemas que podem ser resolvidos por um algoritmo determinístico utilizando espaço polinomial.
- *NPSPACE:* conjunto de problemas que podem ser resolvidos por um algoritmo não determinístico utilizando espaço polinomial.

Pelo Teorema de Savitch, temos:

$$
PSPACE=NPSPACE
$$

O teorema estabelece que um algoritmo não determinístico que utiliza espaço f(n) pode ser simulado deterministicamente utilizando espaço O(f(n)^2), desde que f(n) satisfaça as condições usuais do teorema. Como o quadrado de um polinômio continua sendo um polinômio, conclui-se que PSPACE e NPSPACE são equivalentes. Entao nao faz sentido mais diferenciar ambos e podemos apenas falar PSPACE. Compare com tempo, em que a pergunta equivalente (P = NP?) continua em aberto.

#### **Relação entre P, NP e PSPACE**

É intuitivo concluir que:

$$
P\subseteq NP\subseteq PSPACE
$$

Isso porque um algoritmo que executa em tempo polinomial não pode acessar mais do que um número polinomial de células durante sua execução. Afinal, acessar uma nova célula consome pelo menos um passo de processamento.

- Para demonstrar que $NP\subseteq PSPACE$, podemos utilizar o problema 3SAT, que sabemos ser NP-completo em que a solucao brute-force por mais que seja exponencial em tempo apenas precisa de um vetor binário de N bits, ou seja, é polinomial em espaco. Isso vale para todo NP porque **todo problema de NP reduz para o 3SAT em tempo polinomial**, e uma redução polinomial só consegue gerar uma instância de tamanho polinomial. Então qualquer problema de NP pode ser resolvido transformando a entrada e rodando o brute-force do 3SAT em espaço polinomial. (Um argumento alternativo e mais direto: para qualquer problema de NP, testamos todos os certificados possíveis um por um, reaproveitando o mesmo espaço polinomial.) Portanto,*utilizar tempo exponencial não significa necessariamente utilizar espaço exponencial*. Um algoritmo pode executar uma quantidade enorme de operações reutilizando uma quantidade relativamente pequena de memória.

- Também vale PSPACE ⊆ EXPTIME (com espaço polinomial só existem 2^poly configurações distintas da máquina, então ela para ou repete configuração em tempo exponencial). A hierarquia completa é P ⊆ NP ⊆ PSPACE ⊆ EXPTIME. Sabe-se que P ≠ EXPTIME, então pelo menos uma dessas inclusões é estrita, mas não se sabe qual.

### **Classe PSPACE-Completo**

Da mesma forma que definimos os problemas NP-completos, podemos definir os problemas PSPACE-completos. Para um problema B ser PSPACE-completo, precisamos mostrar duas condições:
1. $B\in PSPACE$, ou seja, existe um algoritmo determinístico que resolve o problema utilizando espaço polinomial.
2. Para todo problema $A\in PSPACE$, existe uma redução polinomial de A para B. Isso significa que B é PSPACE-difícil.

Portanto:
$$
PSPACE\text{-completo}=PSPACE\cap PSPACE\text{-difícil}
$$

O problema QSAT (também chamado TQBF) foi demonstrado PSPACE-completo por Stockmeyer e Meyer em 1973. Ele é uma generalização do SAT com quantificadores alternados:

$$
\exists x_1 \forall x_2 \exists x_3 \ldots \; \varphi(x_1, x_2, x_3, \ldots)
$$

e pergunta se essa fórmula é verdadeira. Dá para pensar nele como um **jogo** entre dois jogadores: "existe uma jogada minha (x1) tal que, para toda resposta do adversário (x2), existe uma jogada minha (x3)… que me faz ganhar (φ = 1)?". O SAT é o caso especial em que só existem quantificadores ∃. Por isso muitos jogos generalizados (em tabuleiros n × n) são PSPACE-completos.

QSAT está em PSPACE porque podemos avaliá-lo recursivamente: para ∃x, testamos x = 0 e depois x = 1 (basta um ser verdadeiro); para ∀x, testamos os dois (ambos precisam ser verdadeiros). A recursão tem profundidade n (uma variável por nível) e o espaço usado em um ramo é reaproveitado no outro, então o espaço total é polinomial, embora o tempo seja 2^n.

Consequentemente, se quisermos provar que outro problema \(B\) é PSPACE-completo, podemos utilizar o mesmo raciocínio das reduções para NP-completude:
- Demonstrar que $B\in PSPACE$.
- Construir uma redução polinomial $QSAT\leq_p B$.
Isso funciona porque QSAT já é PSPACE-completo, portanto todos os problemas em PSPACE podem ser reduzidos a ele.


## **Exemplo de reduções**

Para demonstrar que um problema B é NP-completo, podemos reduzir um problema A já conhecido como NP-completo para B. A seguir estão alguns exemplos de reduções polinomiais.

### **1. Clique → Vertex Cover**
**Clique** de tamanho k consiste em achar um subgrafo completo de tamanho pelo menos k 

**Vertex Cover** consiste em descobrir se existe um conjunto de no máximo k vértices que cobre todas as arestas de um grafo, ou seja, toda aresta possui pelo menos uma extremidade nesse conjunto.

**Redução:** dado um grafo G = (V, E) e um valor k do problema Clique, construímos seu grafo complementar G', mantendo os vértices e invertendo as arestas (onde existia aresta, removemos; onde não existia, adicionamos).

A nova instância será (G', |V| - k).

- **Ida:** se G possui uma clique C de tamanho k, seus vértices não possuem arestas entre si em G'. Então toda aresta de G' tem pelo menos uma ponta fora de C. Portanto, os outros |V| - k vértices (V \ C) cobrem todas as arestas de G'.
- **Volta:** se G' possui um Vertex Cover de tamanho |V| - k, os k vértices restantes não podem possuir arestas entre si em G', logo formam uma clique em G.

A transformação é polinomial, e Vertex Cover pertence a NP, pois podemos verificar se todas as arestas estão cobertas em tempo polinomial.

**Conclusão:** Clique ≤p Vertex Cover.

### **2. Vertex Cover → Dominating Set**

**Dominating Set** consiste em descobrir se existe um conjunto de no máximo k vértices tal que todos os vértices do grafo pertencem a esse conjunto ou são adjacentes a algum vértice dele.

**Redução:** dado um grafo G = (V, E) de Vertex Cover, construímos G' mantendo os vértices e arestas originais, mas adicionando um novo vértice para cada aresta (u, v), conectado às suas duas extremidades.

Mantemos o mesmo k.

- **Ida:** se existe um Vertex Cover de tamanho k em G, seus vértices dominam todos os novos vértices, pois cada aresta original possui pelo menos uma extremidade selecionada. Também dominam os vértices originais não isolados: se u não está no cover, toda aresta (u, w) tem w no cover, e w é vizinho de u.
- **Volta:** se existe um Dominating Set de tamanho k em G', podemos substituir qualquer vértice novo selecionado por uma de suas extremidades. Assim, todos os novos vértices continuam dominados, significando que todas as arestas originais estão cobertas.

A construção é polinomial, e Dominating Set pertence a NP, pois podemos verificar se todos os vértices estão dominados em tempo polinomial.

**Observação:** essa construção pressupõe que o grafo original não possui vértices isolados. Podemos removê-los antes da redução, pois não influenciam Vertex Cover.

**Conclusão:** Vertex Cover ≤p Dominating Set.

### **3. 3SAT → 3-Coloração**

**3-Coloração** consiste em descobrir se conseguimos colorir os vértices de um grafo utilizando no máximo três cores, de modo que vértices adjacentes tenham cores diferentes.

**Redução:** dada uma expressão 3SAT, construímos um grafo utilizando três cores representadas por T (verdadeiro), F (falso) e A (auxiliar).

A construção acontece em etapas:

1. Criamos um triângulo com vértices T, F, A, garantindo três cores diferentes.
2. Para cada variável x, criamos dois vértices x e ¬x, conectados entre si e ao vértice A. Assim, obrigatoriamente um será verdadeiro e o outro falso.
3. Para cada cláusula, criamos uma estrutura (gadget) que só permite uma 3-coloração se pelo menos um literal da cláusula for verdadeiro.

**Como funciona o gadget da cláusula:** a peça básica é um **OR de 2 entradas** a e b: criamos os vértices x, y e o, com as arestas x–a, y–b e o triângulo x–y–o.

```text
  a       b          (entradas: vértices de literais, cor T ou F)
  │       │
  x ───── y
   ╲     ╱
     o               (saída)
```

- Se a = b = F, então x e y não podem ser F, logo usam T e A (um cada, pois x–y é aresta). Como o é vizinho de x e de y, **o é obrigado a ser F**.
- Se alguma entrada é T, existe uma coloração com o = T (ex: a = T, b = F → x = F, y = A, o = T).

Para a cláusula (a ∨ b ∨ c), encadeamos dois ORs: o1 = OR(a, b) e o2 = OR(o1, c), e ligamos a saída o2 aos vértices F e A do triângulo base, o que **obriga o2 a ser T**. Se os três literais fossem F, a saída seria forçada a F, gerando contradição. Logo a cláusula só é colorível se algum literal for T. São 6 vértices novos por cláusula.

- **Ida:** se 3SAT é satisfazível, podemos colorir os vértices das variáveis conforme seus valores e completar a coloração das cláusulas, pois cada cláusula possui pelo menos um literal verdadeiro.
- **Volta:** se o grafo admite uma 3-coloração, cada cláusula precisa possuir pelo menos um literal colorido com T (senão o gadget forçaria a saída a F). Definindo x = verdadeiro se e somente se o vértice x tem a cor T, os valores associados às variáveis satisfazem a expressão original.

Como a construção é polinomial e podemos verificar uma coloração examinando todas as arestas, 3-Coloração pertence a NP.

**Conclusão:** 3SAT ≤p 3-Coloração.

### **4. Vertex Cover → Set Cover**

**Set Cover** consiste em descobrir se podemos escolher no máximo k conjuntos de uma família de conjuntos de modo que sua união contenha todos os elementos do universo.

**Redução:** dado um grafo G = (V, E) de Vertex Cover, construímos uma instância de Set Cover da seguinte maneira:

- Cada aresta do grafo representa um elemento do universo.
- Para cada vértice v, criamos um conjunto S_v contendo todas as arestas incidentes em v.
- Mantemos o mesmo valor k.

- **Ida:** se existe um Vertex Cover de tamanho k, os conjuntos associados aos vértices escolhidos contêm todas as arestas. Portanto, formam um Set Cover.
- **Volta:** se existem k conjuntos que cobrem todas as arestas, os vértices associados a esses conjuntos formam um Vertex Cover, pois cada aresta possui pelo menos uma extremidade selecionada.

A transformação é polinomial, e Set Cover pertence a NP, pois conseguimos verificar em tempo polinomial se a união dos conjuntos escolhidos cobre todo o universo.

**Conclusão:** Vertex Cover ≤p Set Cover.

