## O Algoritmo FP-Growth (Frequent Pattern Growth)

O FP-Growth é a evolução do Apriori. Ele tem exatamente o mesmo objetivo: encontrar **Itemsets Frequentes** baseados em um `min_support`. No entanto, ele resolve os dois maiores gargalos do seu antecessor usando uma estratégia radicalmente diferente.

A eficiência do algoritmo baseia-se em duas premissas: **"Comprimir o banco de dados em uma estrutura de árvore na memória (FP-Tree... que é basicamente uma arvore Trie)"** e **"Mineração sem geração de candidatos"**. Em outras palavras: em vez de adivinhar combinações e ir ao banco de dados checar se elas existem (como o Apriori faz), ele desenha o mapa exato do banco de dados e extrai as combinações diretamente desse mapa.

**O que isso permite (Velocidade e Escala):** 
O algoritmo só precisa ler o banco de dados original **duas vezes**, independentemente do tamanho do padrão que está procurando. Ele elimina a explosão de memória, pois não gera aqueles milhões de candidatos falsos.

---

### O algoritmo (Passo a Passo)

O algoritmo funciona em duas grandes fases: primeiro ele constrói a árvore e, em seguida, ele minera essa árvore de baixo para cima.

1. **O Filtro e Ordenação (Scan 1):** Assim como no Apriori, ele faz a primeira varredura no banco para contar a frequência individual de cada item ($k=1$). Ele descarta os raros (abaixo do `min_support`). O grande diferencial aqui é: ele pega os sobreviventes e os **ordena do maior suporte para o menor** deixando em uma Tabela **T** que mapeia os itens com suporte minimo ordenadamente ITEM -> SUPORTE.

2. **A Construção da Árvore (Scan 2 e ÚLTIMO):** Ele volta ao banco de dados pela segunda e última vez. Para cada transação, pegamos os itens da transação, eliminamos os que não estão na tabela T, ordenamos os itens pela ordem da tabela T (maior suporte primeiro) e os insere em uma árvore de prefixos (a FP-Tree). **Exemplo:** Se a transação 1 é `{Pão, Leite}` e a transação 2 é `{Pão, Café}`, a árvore cria um nó forte para `{Pão}` e depois o divide em dois galhos: um para `{Leite}` e outro para `{Café}`. Transações parecidas compartilham o mesmo caminho, o que comprime enormemente os dados.

3. **Conjuntos Condicionais:** Com a árvore pronta, o banco de dados não é mais usado. O algoritmo olha para a árvore começando pelos itens **com menor suporte** da tabela **T** (Basicamente começa pelos itens das folhas). Para cada item **i** da tabela **T**, pegamos todos os caminhos da raiz ate o item **i** juntamente com o peso $W_i$(quantas vezes aquele caminho apareceu nas transações... imagine caminho como um conjunto de itens que estava junto com **i** nas transações). Dessa forma vamos ter todos os conjuntos em que o item apareceu... cada iteração aqui estamos condicionando em conjuntos que tem o item **i**.

    Como a árvore foi construída ordenadamente (do maior suporte para o menor), quando você olha para o item $i$ e pega o caminho dele até a raiz, você está coletando apenas os itens que aparecem antes dele (os que têm suporte maior ou igual a $i$).Isso garante duas coisas:

    - **Sem duplicidade:** Você nunca vai olhar para "trás" ou para os lados procurando itens menos frequentes que $i$, porque eles já serão tratados quando o algoritmo rodar a vez deles (já que ele sobe de baixo para cima).

    - **Compressão dos dados:** O peso $W_i$ (a contagem do nó da folha) dita a frequência exata daquele subconjunto de itens no banco de dados real, sem precisar ler nenhuma linha de texto do arquivo original de novo.

4. **A Árvore Condicional:** Agora com os caminhos condicionais e quantas vezes o caminho apareceu ($W_i$), conseguimos calcular a frequencia de cada item **j** unico nesses caminhos  condicionais e consequentemente conseguimos calcular o suporte de **(i,j)** e já filtrar qualquer **j** que o SUP(i,j) < MIN_SUP. Depois pegamos os caminhos/conjuntos filtrados (sem **j**), ordenamos pela tabela **T** e criamos a nova arvore condicionada a **i**.

5. **Recursividade:** Uma vez que a arvore condicionada foi construida após o filtro, temos as seguintes opções:

    - Se a arvore condicionada tiver apenas um ramo (nó1 -> nó2 -> nó3 ...) podemos afirmar que todas as combinações de itens junto/somados com o item **i** tem suporte maior que o minimo. Pois se um nó é a unica folha e tem suporte minimo então ele apareceu com todos os anteriores (pertencentes ao caminho para raiz) dele o suficiente para ter suporte minimo.

    - Se a arvore condicionada tiver ramificações vamos repetir o passo 3 porém agora estara condicionado não apenas a um item $\bold{i_1}$ e sim também a $\bold{i_2}$ da nova iteração... o processo será repetido recursivamente enquanto houver ramos na arvore.

    Esse processo se repete para todos os items **i** da tabela começando da arvore original.

---

### As Vantagens do FP-Growth 

* **Apenas 2 Scans:** Não importa se a regra gerada tem tamanho 3 ou tamanho 20, o banco de dados gigantesco do disco será lido apenas duas vezes. Tudo o resto ocorre na memória RAM.
* **Sem Geração de Candidatos:** Ele não perde tempo testando combinações inúteis. A técnica de extrair caminhos diretos da árvore significa que ele só processa o que já sabe que tem grande chance de ser frequente.
