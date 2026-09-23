# **Analise de sobrevivencia**

Análise de sobrevivência é um conjunto métodos estatísticos para analisar dados em que a variável resposta é o tempo até a ocorrência de um evento, e em que parte das observações é censurada. Um problema de sobrevivencia depende da definição de 3 coisas:

- *evento:* Algo bem definido que acontece com um individuo em algum intervalo de tempo (como churn, compra, morte)
- *origem/inicio:* O ponto em que começamos observar o individuo (começamos a observar o individuo desde o diagnostico ? desde a ultima compra ? desde hoje ?)
- *censura:* Casos em que não observamos o evento e não temos dados suficientes para afirmar se aconteceu ou não, apenas uma informação incompleta que não aconteceu ate x dias.


## **Metricas** 

### **C-index (Harrell e Uno)**

Para todos os pares comparaveis, ou seja, todos os pares não censurados em que conseguimos ter acesso ao evento (proxima compra por exemplo) mede a capacidade do modelo de ordenar corretamente os tempos até o evento, não a probabilidade em si. Pergunta: "entre dois clientes, o modelo prevê tempo menor para quem realmente comprou primeiro?"


$$
C_{\text{Harrell}} = \frac{\displaystyle\sum_{i,j} \mathbb{1}\left[T_i < T_j\right] \cdot \mathbb{1}\left[\hat{\eta}_i > \hat{\eta}_j\right] \cdot \delta_i}{\displaystyle\sum_{i,j} \mathbb{1}\left[T_i < T_j\right] \cdot \delta_i}
$$

O problema do C-index de Harrell é que, quando há muita censura, ele fica enviesado, pares envolvendo indivíduos censurados cedo são simplesmente descartados, e isso distorce a estimativa se a censura não for uniforme ao longo do tempo.

$$
C_{\text{Uno}} = \frac{\displaystyle\sum_{i,j} \mathbb{1}\left[T_i < T_j\right] \cdot \mathbb{1}\left[\hat{\eta}_i > \hat{\eta}_j\right] \cdot \delta_i \cdot \dfrac{1}{\hat{G}(T_i)^2}}{\displaystyle\sum_{i,j} \mathbb{1}\left[T_i < T_j\right] \cdot \delta_i \cdot \dfrac{1}{\hat{G}(T_i)^2}}
$$

- $T_i, T_j$: tempos observados (evento ou censura)
- $\delta_i$: indicador de evento ($1$ = evento observado, $0$ = censurado)
- $\hat{\eta}_i$: score de risco previsto pelo modelo
- $\hat{G}(t)$: estimativa Kaplan--Meier da distribuição de censura (probabilidade de não estar censurado até $t$)


### **IBS (Integrated Brier Score)**

