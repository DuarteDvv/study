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




