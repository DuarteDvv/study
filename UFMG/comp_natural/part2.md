# **ACO (Ant Colony Optmization)**

Algoritmo inspirado no comportamento de formigas que usa algo chamado feromonios para criar uma comunicacao indireta entre as formigas atraves do ambiente (estigmergia), ou seja, uma passagem de informacao. Aqui se usa uma ideia de retroalimentacao positiva em que quanto mais usado uma caminho é, mais atrativo para outras formigas ele se torna. No algortimo cada formiga é um agente que constroi solucoes de forma incremental em um grafo que representa o problema, um caminho nesse grafo representa uma solucao valida e cada aresta nesse grafo tem uma quantidade de feromonio.

Durante a construcao incremental de uma solucao a escolha de qual aresta seguir depende de 2 fatores:

- **Feromonio das arestas candidatas:** Conhecimento global adiquirido através das outras formigas 
- **Heuristica do problema:** Conhecimento local adiquirido do proprio problema

A melhor aresta nao é sempre escolhida pois é um processo probabilistico

## **Exemplo TSP**

O objetivo do problema do caixeiro viajante é encontrar um caminho de custo mínimo que visite todas as cidades exatamente uma vez e retorne à cidade inicial. No ACO, cada formiga constrói um caminho completo, escolhendo uma cidade por vez.

A função heurística poderia ser:
$$
\eta_{ij}=\frac{1}{d_{ij}}
$$

Em que $d_{ij}$ é a distância entre as cidades $i$ e $j$. Por que utilizar o inverso da distância? Porque queremos que arestas menores tenham maior atratividade. Entretanto, escolher sempre a cidade mais próxima não garante o menor caminho global. É justamente por isso que também utilizamos o feromônio. A heurística representa informação local, enquanto o feromônio representa aprendizado global pois melhores solucoes globais vao ter mais feromonios.

## **Como escolher qual aresta seguir ?**

Para escolher qual das arestas possiveis que saem de i seguir decidimos a probabilidade de cada uma delas usando a seguinte equacao:

$$
p_{ij}=
\frac{\tau_{ij}^{\alpha}\eta_{ij}^{\beta}}
{\displaystyle\sum_{k\in N(i)}
\tau_{ik}^{\alpha}\eta_{ik}^{\beta}}
$$

Onde:

- $p_{ij}$	Probabilidade de ir de $i$ para $j$
- $\tau_{ij}$	Feromônio da aresta
- $\eta_{ij}$	Qualidade heurística da aresta
- $\alpha$	Influência do feromônio
- $\beta$	Influência da heurística
- $N(i)$	Conjunto de vizinhos elegíveis (não visitados)

A logica da equacao é simples, o valor da aresta é definido pelo produto do feromonio pela heuristica ponderados pelos seus respectivos pesos. Depois transformamos esse peso em uma probabilidade normalizando pela soma de todos os pesos das arestas que saem de i. Ao final sorteamos um numero e amostramos dessa distribuicao para escolher a aresta... as arestas de maior peso/atratividade tem mais chance.

## **Como atualizar o feromonio durante a otimizacao ?**

Depois que todas as formigas constroem suas soluções, atualizamos o feromônio em duas etapas: evaporação e depósito

### **Evaporação**

$$ \tau_{ij}\leftarrow(1-\rho)\tau_{ij} $$

Onde $\rho$ é a taxa de evaporação:
$$
0<\rho\leq1
$$

Se $\rho=0,2$, por exemplo, significa que 20% do feromônio existente será removido. A evaporação permite que o algoritmo esqueça informações antigas e reduz o risco de convergência prematura para soluções ruins.

### **Depósito**

Cada formiga deposita feromônio nas arestas que utilizou para construir sua solução.

$$
\Delta\tau_{ij}^{k}=
\begin{cases}
\dfrac{1}{f(S_k)},& (i,j)\in S_k\\[6pt]
0,&\text{caso contrário}
\end{cases}
$$

Onde $f(S_k)$ é o **custo total da solução** construída pela formiga $k$. Por que utilizar o inverso do custo? Porque, em um problema de minimização, quanto menor o custo da solução, maior deve ser a recompensa. Uma formiga que encontrou um caminho de custo 10 deposita o dobro do feromônio de outra que encontrou um caminho de custo 20.

### **Equação completa**

Combinando as duas etapas para atualizar uma unica aresta:
$$
\tau_{ij}(t+1)=
(1-\rho)\tau_{ij}(t)
+\sum_{k=1}^{m}\Delta\tau_{ij}^{k}
$$

O primeiro termo representa a memória preservada da iteração anterior. O segundo representa o conhecimento adquirido pelas formigas na iteração atual.


### **Exploration × Exploitation**

No ACO, a escolha probabilística permite exploração, enquanto o reforço de boas soluções através do feromônio favorece intensificação. O equilíbrio é importante porque o algoritmo pode reforçar excessivamente uma solução subótima, dificultando a descoberta de uma solução melhor.


### **Métodos de atualização do feromônio**

- **Tradicional:** Todas as formigas depositam feromônio.
- **Elitista:**	Utiliza a melhor solução encontrada até o momento para reforçar o feromônio, ou seja, as arestas da melhor solucao vao receber um feromonio extra.
- **Rank:**	As soluções são ordenadas e recebem pesos conforme sua classificação.
- **Max-Min:** Somente uma formiga deposita feromônio, e os valores possuem limites inferiores e superiores.

#### **Max-Min**
Dê atenção especial a esse método, pois recebe um slide exclusivo na aula. No Max-Min, impomos a restrição:
$$
\tau_{\min}\leq\tau_{ij}\leq\tau_{\max}
$$

Isso significa que nenhuma aresta pode se tornar excessivamente atrativa ou perder completamente sua atratividade por falta de feromônio, considerando um limite inferior positivo. Apenas a melhor formiga da iteração ou a melhor formiga encontrada desde o início deposita feromônio.

Inicialmente:
$$
\tau_{ij}(0)=\tau_{\max}
$$

A inicialização favorece a exploração, enquanto o reforço seletivo favorece a intensificação nas iterações seguintes.

## **Algoritmo**

```text
Inicializar feromônio de todas as arestas
Inicializar melhor solução

Enquanto não atingir o limite de iterações:

    Para cada formiga:

        Selecionar cidade inicial

        Enquanto existirem cidades não visitadas:
            Calcular probabilidades de transição
            Sortear próxima cidade
            Adicionar cidade à solução

        Retornar à cidade inicial
        Calcular custo total da solução
        Atualizar melhor solução encontrada

    Evaporar feromônio de todas as arestas
    Depositar feromônio conforme as soluções

Retornar melhor solução encontrada
```

# PSO 