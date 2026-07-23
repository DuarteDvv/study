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

