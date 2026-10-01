# **Analise de complexidade**

Dentre os problema computaveis, ou seja, existe um algortimo (sequencia finita de passos) que resolve o problema nem todos são trataveis uma vez que um algortimo com complexidade de tempo O(2^n) deixa de ser util rapidamente enquanto n cresce com nossos computadores atuais.

*Problema x Instancia:*

- Um problema é algo generalizado para qualquer entrada existente N (X é primo ?)
- Uma instancia de um problema é quando colocamos uma valor especifico de entrada (44 é primo ?)

## **Classe P** 

A classe P contém os problemas que podem ser resolvidos por um algortimo em tempo polinomial, ou seja, esses são os problemas computaveis trataveis.

- Um exemplo são algortimos de ordenação que funcionam para qualquer entrada em tempo polinomial

## **Classe NP**

Um problema está em NP se, dada uma solução candidata, *podemos* verificar em tempo polinomial se ela é válida. È intuitivo concluir que todos os problemas que estão P também estão em NP (P contido em NP) pois se resolvemos o problema em P então com certeza verificamos uma solução em P. A grande pergunta é se NP também esta contido em P, ou seja, P = NP, isso não sabemos e é um problema em aberto.

- Um exemplo é o problema do clique que dado um grafo G e um número k, queremos saber se existe uma clique de tamanho k? Uma clique é um conjunto de vértices em que todo vértice está conectado a todos os demais. Não conseguimos resolver em P para qualquer entrada mas dado uma solução candidata conseguimos verificar em tempo quadratico comparando todos com todos. A mesma logica se aplica ao subset sum pois se recebermos um conjunto de numeros conseguimos verificar facilmente se a soma é igual a S.

## **Classe co-NP**

co-NP são problemas em que uma resposta candidata *NÃO pode* ser verificada em tempo polinomial, ou seja, são os problemas complementares aos problemas dentro de NP. 

- Um exemplos seria o complemento do clique que é verificar se "NÃO existe clique de tamanho k ?", receber um candidado não nos diz que dentre as outras combinações possiveis nenhuma delas é clique, apenas diz que aquela não é.

## **Classe NP-Completo**

Todos os problemas de NP tem a mesma dificuldade ? Essa é uma pergunta importante que abre espaço para um conceito chamado redução de problemas

### **Redução de problemas (Problemas de decisão Sim/Não)**

A ideia de redução de um problema A para um problema B consiste na construição de uma função F() tal que ela receba a entrada para o problema A e retorne uma entrada para o problema B, ou seja, F(in(A)) -> in(B) e como consequencia só existe solução para A se existe solução F(in(A)). Isso pode ser utilizado tanto teoricamente para provar coisas mas também na pratica transformando problemas sem solução em outros que tenham. Essa redução é considerada polinomial se a função F é polinomial. Além disso, se B tem solução polinomial e A pode ser convertido em B então A também é polinomial.

- *Exemplo convertendo 3SAT para clique:*

    3SAT consiste no problema de dado uma expressão booleana na forma fórmula lógica escrita como uma conjunção/produto (E / ∧) de cláusulas, onde cada cláusula é uma disjunção/somas (OU / ∨) de literais (variáveis ou suas negações), responder se existe combinação de valores 0 e 1 para as variaveis que satisfaz a expressão (expressão = 1). Clique consiste em descobrir se existe um subgrafo completo de tamanho k dentro do grafo original. (produto de somas de tamanho 3)

    - A entrada de 3SAT é um conjunto de variaveis V que assumem valores 0 ou 1 e a expressão
    - A entrada do Clique é um grafo  

    Isso pode ser feito criando um vértice para cada literal/variavel da expressão (repetidas tem diferentes) e depois uma aresta para os pares de vértices que são de conjunções (produto) diferentes e não são o inverso um do outro. Usamos o tamanho do SAT que é 3, como k no clique.

    Depois temos que mostrar que sempre que quando 3SAT é sim (satisfazivel) existe uma clique de tamanho 3 nesse grafo e o contrário também:

    *3SAT -> Clique:* Se o 3SAT é sim então para cada termo da conjunção existe pelo menos um valor igual a 1, dentre esses termos nenum deles pode ser igual ao negativo do outro pois isso torna sem solução. Ao transformar em um grafo eles vão ter arestas para os outros pois não são sua negação e portanto eles formam um subgrafo completo de tamanho 3 pois existe 3 conjunções.

    *Clique -> 3SAT:* Se o grafo gerado tem um subgrafo de tamanho 3 completo, então significa que esses vértices estão em conjunções diferentes e que eles não são a negação um do outro.


### **NP-Completo**

Para um problema B ser considerado nessa classe ele tem que:

1. B ser NP (Verificador polinomial)
2. Para todo problema A dentro de NP, B tem que ser pelo menos tão dificil que eles (A <= B). Isso é chamado de NP-dificil, repare que para ser NP-hard não é necessário ser NP (problema da parada) mas para ser NP-complete é necessário ser NP-dificil.

- Então se A é NP-completo e B é NP, se A pode ser convertido para B então B também é NP-completo.
- O primeiro NP-completo não provado por transição é o Cook-Levin (1970) que mostra que SAT é NP-completo.






