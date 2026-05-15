## **Regras de Associação**

Seja um conjunto de transações do tipo:

| ID | Transação |
| :--- | :--- |
| **ID1** | {A, B, C} |
| **ID2** | {B, C} |

Em que **A**, **B** e **C** são itens presentes nessa transação. Queremos extrair dessa amostra regras do tipo $X \Rightarrow Y$.

Sejam $X$ e $Y$ **itemsets disjuntos**, ou seja, conjuntos de itens que não têm interseção alguma ($X \cap Y = \emptyset$). Uma regra de associação é um indício aprendido com os dados de que, se $X$ ocorre, então $Y$ também tende a ocorrer.

---

### **Métricas**

Para avaliar a relevância de uma regra, utilizamos três métricas principais:

#### **1. Suporte (Support)**
Indica a frequência relativa com que a combinação de itens ($X \cup Y$) aparece no conjunto total de dados. 

$$Support(X \Rightarrow Y) = \frac{\text{frequência}(X \cup Y)}{N}$$

Quantas vezes X apareceu junto com Y nos dados ? Suporte baixo leva a crer que essa combinacao (X,Y) é um evento raro e provavelmente nao é relevante. Pode ser lido como a **porcentagem de vezes que esse conjunto apareceu nas transacoes**.

#### **2. Confiança (Confidence)**
Mede a proporção de vezes que, em transações contendo o itemset $X$, o itemset $Y$ também aparece.

$$Confidence(X \Rightarrow Y) = \frac{Support(X \Rightarrow  Y)}{Support(X)}$$

Suporte da uniao(X,Y) representa quanta vezes apareceram juntos e o suporte de X quantas vezes X apareceu... a divisao desses dois diz em quanto do suporte de X, o Y também estava presente. Da parcela de vezes que X apareceu, quantas delas Y apareceu junto ? Pode ser lido como **dado que o conjunto X esta presente na transacao qual a probabilidade do Y também estar (Probabilidade de Y dado X, ou seja, P(Y|X))**.

o que acontece se um item estiver em 90% de todas as transacoes ? Qualquer outro item ira ter uma confianca alta com ele (acha que causa).


#### **3. Lift**
Mede o quanto a ocorrência de $X$ influencia a ocorrência de $Y$. É o fator pelo qual a probabilidade de $Y$ aumenta dado que $X$ ocorreu. Lift é uma métrica mais robusta também.

$$Lift(X \Rightarrow Y) = \frac{Confidence(X \Rightarrow Y)}{Support(Y)}$$

*   **Lift > 1:** Indica que os itens são positivamente correlacionados.
*   **Lift = 1:** Indica que os itens são independentes.
*   **Lift < 1:** Indica que os itens são substitutos (quem compra $X$ tem menos chance de comprar $Y$).

Aqui estamos dividindo a probabilidade de Y dado X pela probabilidade de Y ... isso nos retorna quantas vezes P(Y|X) é maior que P(Y). Interpretacao:

- Se lift = 1, os itemsets sao independentes pois a ocorrencia anterior de X nao mudou a probabilidade de Y aparecer.
- Se lift > 1, entao X aparecer junto com Y faz com que Y seja maior lift vezes mais provavel que ele sozinho.
- Se lift < 1, entao X aparecer reduz a probabilidade de Y aparecer, ou seja, provavelmente concorrentes.

Pode ser lido como **quantas vezes X afeta a chance de Y aparecer** ?

### **Extraindo regras de dados**

A geracao de regras de associacao é um processo nao supervisionado dividido em 2 partes:

1. **Encontrar os itemsets frequentes:** Aqui nosso objetivo é encontrar os conjuntos de itens mais frequentes e retornar eles junto com seu respectivo suporte. Exemplo: ({A,B}, 0.5)
2. **Gerar regras de associacao:** Aqui o objetivo é calcular a confianca/lift para os itemsets frequentes e depois filtrar por um limiar que pode ser confianca ou lift.

Os algortimos Apriori e FP-Growth sao para a primeira parte (e mais cara) do problema... mas oq aconteceria se nao usassemos os algortimos ? A complexidade (custo) é dominada por dois fatores: o número de combinações possíveis e o número de transações no banco de dados.

$$O(N \cdot 2^d)$$

Onde:
*   **$N$**: Número total de transações no banco de dados.
*   **$d$**: Número de itens distintos (itens únicos) no vocabulário.
*   **$2^d$**: O "Power Set" (conjunto das partes). É o número total de combinações possíveis de itens.

Para entender o impacto, vamos considerar um banco de dados com **$N = 1.000.000$** de transações e diferentes tamanhos de vocabulário ($d$):

| Itens ($d$) | Combinações ($2^d$) | Operações Necessárias ($N \cdot 2^d$) | Status |
| :--- | :--- | :--- | :--- |
| **10** | 1.024 | ~1 Bilhão | Viável em segundos |
| **20** | ~1 Milhão | ~1 Trilhão | Viável em minutos/horas |
| **30** | ~1 Bilhão | ~1 Quatrilhão | No limite do aceitável |
| **50** | ~1 Quatrilhão | ~10²¹ | **Inviável** (levaria anos) |
| **100** | ~1,26 × 10³⁰ | ~10³⁶ | **Impossível** (excede a idade do universo) |

