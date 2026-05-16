## O Algoritmo FP-Growth (Frequent Pattern Growth)

O FP-Growth é a evolução do Apriori. Ele tem exatamente o mesmo objetivo: encontrar **Itemsets Frequentes** baseados em um `min_support`. No entanto, ele resolve os dois maiores gargalos do seu antecessor usando uma estratégia radicalmente diferente.

A eficiência do algoritmo baseia-se em duas premissas: **"Comprimir o banco de dados em uma estrutura de árvore na memória (FP-Tree... que é basicamente uma arvore Trie)"** e **"Mineração sem geração de candidatos"**. Em outras palavras: em vez de adivinhar combinações e ir ao banco de dados checar se elas existem (como o Apriori faz), ele desenha o mapa exato do banco de dados e extrai as combinações diretamente desse mapa.

**O que isso permite (Velocidade e Escala):** 
O algoritmo só precisa ler o banco de dados original **duas vezes**, independentemente do tamanho do padrão que está procurando. Ele elimina a explosão de memória, pois não gera aqueles milhões de candidatos falsos.

---

### O algoritmo (Passo a Passo)

O algoritmo funciona em duas grandes fases: primeiro ele constrói a árvore e, em seguida, ele minera essa árvore de baixo para cima.

1. **O Filtro e Ordenação (Scan 1):** Assim como no Apriori, ele faz a primeira varredura no banco para contar a frequência individual de cada item ($k=1$). Ele descarta os raros (abaixo do `min_support`). O grande diferencial aqui é: ele pega os sobreviventes e os **ordena do mais frequente para o menos frequente**.

2. **A Construção da Árvore (Scan 2 e ÚLTIMO):** Ele volta ao banco de dados pela segunda e última vez. Para cada transação, ele pega os itens frequentes (na ordem que definimos no passo 1) e os insere em uma árvore de prefixos (a FP-Tree). 
    *   *Exemplo:* Se a transação 1 é `{Pão, Leite}` e a transação 2 é `{Pão, Café}`, a árvore cria um nó forte para `{Pão}` e depois o divide em dois galhos: um para `{Leite}` e outro para `{Café}`. Transações parecidas compartilham o mesmo caminho, o que comprime enormemente os dados.

3. **A Base de Padrões Condicionais (Divisão):** Com a árvore pronta, o banco de dados não é mais usado. O algoritmo olha para a árvore começando pelos itens **menos frequentes** da tabela ordenada por suporte (que ficam geralmente nas folhas da árvore). Para cada item da tabela, ele traça o caminho de volta até a raiz para descobrir quais itens vieram antes dele. Esses caminhos formam a "Base de Padrões Condicionais" daquele item.

4. **A Árvore Condicional (Filtro Interno):** Usando apenas os caminhos descobertos no Passo 3, ele constrói uma "Mini FP-Tree" exclusiva para aquele item que está sendo analisado, descartando novamente o que não atingir o `min_support` dentro daquele contexto menor.

5. **Mineração Recursiva (Conquista):** Ele repete o processo 3 e 4 recursivamente para essas mini-árvores, extraindo os padrões frequentes. Como ele está sempre lidando com fragmentos menores da árvore, o processo é extremamente rápido.


Essa estratégia de "Divisão e Conquista" (Passos 3 a 5) evita que o computador tente validar combinações que nunca existiram, pois a árvore só contém os caminhos de compras que **realmente aconteceram**.

---

### As Vantagens do FP-Growth 

* **Apenas 2 Scans:** Não importa se a regra gerada tem tamanho 3 ou tamanho 20, o banco de dados gigantesco do disco será lido apenas duas vezes. Tudo o resto ocorre na memória RAM.
* **Sem Geração de Candidatos:** Ele não perde tempo testando combinações inúteis. A técnica de extrair caminhos diretos da árvore significa que ele só processa o que já sabe que tem grande chance de ser frequente.
