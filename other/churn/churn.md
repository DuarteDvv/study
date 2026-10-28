# **CHURN**

## **Definição/Rotulação do Churn para a empresa (O target)**

Precisamos traduzir o "abandono do cliente" para um rotulo binário (1 = churn, 0 = não churn) mas existem diversos tipos de relações entre cliente e empresa:

- **Plano com cancelamento a qualquer hora:** O churn é o cancelamento do plano e provavelmente esta registrado nos dados -> Facil de rotular.

- **Contrato com vencimento:** O churn é a não renovação do contrato e provavelmente esta registrado nos dados -> Facil de rotular

- **Inatividade em varejo:** O churn é o cliente deixar de comprar os produtos da sua empresa de pouco em pouco -> Complicado de rotular pois cada cliente tem um TMC (tempo medio entre compras) e muitas vezes esse tempo medio varia também em produtos quando o varejo tem mais de um produto... o chamado Churn PARCIAL é quando o cliente ainda compra mas esta reduzindo o volume/variedade de produtos pouco a pouco ate abandonar completamente a empresa e acontecer o CHURN completo.

## **Criacao de instancias de treino**

Como é um problema temporal as instancias de treino/teste sao construidas em cima de uma janela deslizante que possui 4 parametros:

- **Ponto de referencia t:** é o ponto que cortamos em nao olhamos mais para frente para calcular features. É equivalente ao dia de hoje em que vamos fazer uma inferencia do modelo. Nenhuma informacao que vem de depois do tempo *t* pode ser usada -> data leakage.

- **Lookback b (Observacao):** é o quanto no passado vamos olhar para calcular as features dinamicas da instancia. Olhamos ate o dia **t - b**.

- **Lookahead a (Previsao):** é o quanto olhamos para frente para saber se o cliente cancelou ou nao, ou seja, é uma janela (que vai ate **t + a**) de rotulacao da intancia de treino.

- **Stride/Sliding (Deslizamento):** Para multiplicar nosso volume de dados e ensinar o modelo a identificar padrões em diferentes épocas do ano, avançamos o ponto $t$ de forma sistemática (ex: a cada 15 ou 30 dias). Assim, um cliente que está na base há 2 anos não gera apenas 1 linha no dataset de treino, mas sim 24 instâncias (se stride for 30 dias) diferentes, cada uma com seu próprio lookback e lookahead. Isso ajuda o algoritmo a entender a evolução do comportamento.

## **Split Treino/Teste**

...


# **Teste A/B** 

## **Problema**

### **Contexto**

Queremos medir o sucesso de estratégias de retenção de clientes (clientes das distribuidoras) dos nossos clientes (distribuidoras). O objetivo é perder menos dos clientes das distribuidoras (reduzir churn) e os afetados são:

- *Clientes das distribuidoras:* Recebem incentivo/intervenção das distribuidoras 
- *Distribuidoras:* Devem gastar e intervir nos seus clientes

### **Definição de churn** 

Nossa abordagem evita definir churn o evento fixo e portanto não existe essa definição

### **Possiveis métricas**

- *Primária:* Taxa de não compra em 60 dias -> proporção de clientes randomizados que não realizaram compras durante os 60 dias de acompanhamento. Quanto menor, melhor.
- *Secundária:* Receita por cliente -> receita total gerada durante o experimento dividida pelo número de clientes randomizados, incluindo aqueles sem compras
- *Terciária:* Custo de intervenção -> quanto gastamos para abordar os clientes

### **Intervenção**

Mensagem no WPP ou ligação (todas as distribuidoras tem que ter a mesma intervenção). Custo pode ser avaliado como a soma dos seguintes fatores:
- Custo da ligação de acordo com o valor/hora da operadora
- Custo do tempo gasto do funcionário/BOT de acordo com salário/preço

### **Clientes elegiveis**

Os clientes elegiveis tanto no treino e inferencia são também aqueles que estarão no teste A/B. O critério é:

- Dada uma data de referencia t, consideramos um cliente ativo se a sua recencia é menor que o P90 dele. Se ele tem apenas uma compra, consideramos ele como novo se a recencia for menor que a mediana dos P90 da distribuidora dele

## **Hipotese e parametros do teste**

### **Tipo e hipotese**

Como nos interessa saber apenas se a métrica do grupo novo B é melhor que o controle A podemos usar um one-tail test com as seguintes hipoteses:

- *Hipotese Nula (H0):* Os B é pior ou igual a A -> B >= A
- *Hipotese Alternativa (H1):* B é melhor que A -> B < A

### **Parametros**

Alguns parametros padrão de testes:

- *Alpha:* 0.05 (5%) 
- *Power(1 - Beta):* 0.80 (80%)
- *Variabilidade histórica da métrica:* ? 
- *MDE(Minimum Detectable Effect/Lift):* ?
- *Tamanho do grupo:* 50/50 se possivel, mas devido ao custo de intervenção nos clientes pode ser que o grupo B tratamento seja menor levandoa 80/20
- *Tamanho da amostra estimada:* ?

- *Teste usado:* z-test para proporções ou algum não paramétrico

## **Design do Experimento**

### **Unidade de randomização**

A unidade poderia ser distribuidoras porém isso teria alguns problemas como por exemplo uma distribuidora ter maior taxa de não compra ou muito mais clientes e além disso temos poucas distribuidoras. A melhor unidade é o cliente final das distribuidoras e provavelmente pegar uma porcentagem dos clientes com maior risco de acordo com o score para intervir.

### **Randomização**

- *Clientes independentes da distribuidora:* Junta todos os clientes sem levar em consideração a distribuidora e separa o grupo controle e tratamento. O problema aqui é que distribuidoras diferentes podem ter tipos muito diferentes de clientes e isso pode aumentar a variabilidade nos grupos se pegarmos mais de uma distribuidora que outra.

- *Clientes estratificados por distribuidora:* Para cada distribuidora escolhemos aleatoriamente X dos seus clientes 

### **Duração**

2-3 meses de experimento

### **Controle e tratamento**

- *Tratamento:* A ideia é o tratamento utilizar nossos scores gerados no comeco do experimento para um horizonte de 60 dias, interpretalos como sinal de risco e intervir nos clientes. A ideia é que clientes que iriam sair deixem de sair com essa intervercao e a taxa de nao compra reduza.
- *Controle:* Duas possibilidades:
    - Na ausencia de um metodo atual, esse grupo será apenas observado e ao final a taxa calculada. 
    - Se já existe algum metodo, a intervencao (do mesmo tipod do tratamento) deve ser feita usando ele para ao final compararmos as métricas.










