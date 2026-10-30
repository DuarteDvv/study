# **Teste A/B** 

## **Problema**

### **Contexto**

Queremos medir o sucesso de estratégias de retenção de clientes (clientes das distribuidoras) dos nossos clientes (distribuidoras). O objetivo é perder menos dos clientes das distribuidoras (reduzir churn) e os afetados são:

- *Clientes das distribuidoras:* Recebem incentivo/intervenção das distribuidoras 
- *Distribuidoras:* Devem gastar e intervir nos seus clientes

### **Definição de churn** 

Nossa abordagem evita definir churn o evento fixo e portanto não existe essa definição

### **Possiveis métricas**

- *Primária:* 
    - *Taxa de compra em 60 dias* -> proporção de clientes randomizados que realizaram pelo menor uma compra durante os 60 dias de acompanhamento. Quanto maior, melhor.
- *Secundária:* 
    - *Receita por cliente* -> receita total gerada durante o experimento dividida pelo número de clientes randomizados, incluindo aqueles sem compras
    - *Compras por cliente* -> numero de compras por cliente

### **Intervenção**

Mensagem no WPP ou ligação (todas as distribuidoras tem que ter a mesma intervenção). Custo pode ser avaliado como a soma dos seguintes fatores:
- Custo da ligação de acordo com o valor/hora da operadora
- Custo do tempo gasto do funcionário/BOT de acordo com salário/preço

## **Hipotese e parametros do teste**

### **Tipo e hipotese**

Como nos interessa saber apenas se a métrica do grupo novo B é melhor que o controle A podemos usar um one-tail test com as seguintes hipoteses:

- *Hipotese Nula (H0):* Os B é pior ou igual a A -> B <= A
- *Hipotese Alternativa (H1):* B é melhor que A -> B > A

### **Parametros**

Alguns parametros padrão de testes:

- *Alpha:* 0.05 (5%) 
- *Power(1 - Beta):* 0.80 (80%)
- *Variabilidade histórica da métrica:* 
    - Variância sem estratificação = 0,229046
    - Variância estratificada usada	= 0,199265

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

- *Tratamento:* A ideia é o tratamento utilizar nossos scores gerados no comeco do experimento para um horizonte de 60 dias, interpreta-los como sinal de risco e intervir nos clientes. A ideia é que clientes que iriam sair deixem de sair com essa intervercao e a taxa de nao compra reduza.
- *Controle:* Duas possibilidades:
    - Na ausencia de um metodo atual, esse grupo será apenas observado e ao final a taxa calculada -> Baseline = 35,93%
    - Se já existe algum metodo, a intervencao (do mesmo tipod do tratamento) deve ser feita usando ele para ao final compararmos as métricas.










