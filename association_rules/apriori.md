## O Algoritmo Apriori

O Apriori é o algoritmo clássico para encontrar **Itemsets Frequentes** em dados transacionais. Ele não extrai as regras diretamente; o trabalho dele é varrer os dados de forma inteligente para encontrar quais conjuntos de itens superam o limiar de **Suporte Mínimo** (`min_support`) que é um parametro do algortimo. 

A eficiência do algoritmo baseia-se em: **"Se um itemset é infrequente, todos os seus superconjuntos (conjuntos que possuem ele) também serão obrigatoriamente infrequentes."** Em outras palavras: Se nos nossos dados o suporte de {A} é baixo, é impossivel que qualquer conjunto com A ({A,B}, {A,B,C}, {A,C}) tenha suporte alto. 

**O que isso permite (Poda / Pruning):** 
Se o algoritmo descobre que `{A}` não atinge o suporte mínimo, ele **elimina** sumariamente qualquer candidato futuro que contenha `{A}` (como `{A,B}`, `{A,C}`, `{A,B,C}`). Ele nem sequer vai ao banco de dados contar esses conjuntos maiores.

---

### O algoritmo (Passo a Passo)

O algoritmo funciona construindo conjuntos de tamanho $k$, começando do $k=1$ até não encontrar mais nada.

1. **O Filtro Inicial ($k=1$):** O algoritmo começa analisando os itens individualmente. Ele faz uma varredura completa nas transações e descarta tudo o que for raro (abaixo do `min_support`). O resultado é uma lista de itens "sobreviventes" que aparecem com a frequência mínima exigida.

2. **A Tentativa de achar itemsets maiores:** Com base nos itens que sobreviveram à etapa anterior, o algoritmo tenta formar grupos um pouco maiores (tamanho $k+1$). Exemplo: Se `{Pão}` e `{Leite}` são frequentes isoladamente, ele cria o candidato `{Pão, Leite}` para o próximo nível.

3. **A Poda / Pruning:** Antes de gastar tempo lendo o banco de dados novamente, o algoritmo aplica o **pruning** da seguinte forma: ele verifica se todos os "pedaços" (subconjuntos) desse novo grupo eram frequentes na etapa anterior. Se você está testando o grupo `{Pão, Leite, Café}`, mas já sabe que `{Pão, Café}` foi descartado por ser raro no nível anterior, você **elimina** o grupo inteiro agora mesmo. Não se gasta processamento contando algo que, matematicamente, não tem chance de ser frequente.

4. **Scan:** O algoritmo volta ao banco de dados, mas **apenas** para contar a frequência dos candidatos que sobreviveram a Poda. O resultado sao conjuntos que possuem suporte suficiente nas transações.

5. **Ciclo:** O processo se repete aumentando o tamanho dos grupos ($k=2 \rightarrow 3 \rightarrow 4...$) usando os sobreviventes do nível anterior como base. O ciclo encerra quando:

    - Não for mais possível formar novos candidatos a partir dos itens frequentes atuais.
    - Nenhum candidato novo conseguir atingir o suporte mínimo no scan.


A Poda (Passo 3) evita que o computador tente contar bilhões de combinações inúteis, agindo como um filtro que barra candidatos ruins antes mesmo de eles chegarem à fase cara de leitura do banco de dados.

---

### Os Gargalos do Apriori (Por que precisamos do FP-Growth?)

* **Múltiplos Scans:** Para encontrar um padrão frequente de tamanho 10, o algoritmo precisa ler o banco de dados inteiro 10 vezes.
* **Custo de Geração de Candidatos:** O passo de tentar achar itemsets maiores gera candidatos que podem não ser frequentes. Se você tem 10.000 itens frequentes, o Apriori gera ~50 milhões de candidatos de tamanho 2 para testar. Isso pode estourar a memória RAM.