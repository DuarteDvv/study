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

# **PSO (Particle Swarm Optmization)** 

PSO é outro algortimo de inteligencia de enxames só que nesse ele funciona de forma diferente do ACO. Aqui temos um conjunto de particulas no espaco em que cada particula representa uma solucao candidata atual, entao se queremos minimizar uma funcao de 2 variaveis nossas particulas sao pontos em 2D (um para cada variavel) em que queremos otimizar esse fitness se movendo no espaco com uma velocidade usando conhecimento global e local.

## **Componentes de uma particula**

Para um problema de D variaveis que queremos otimizar temos para cada particula:

- **Vetor de solucao atual:** Solucao atual de dimensao D
- **Velocidade atual:** Direcao no espaco que atualmente estamos tendendo a avancar para otimizar a fitness
- **Melhor solucao individual:** Vetor de menor fitness ja encontrado por essa particula
- **Melhor solucao global:** Vetor de menor fitness ja encontrado por todas as particulas 

## **Equações do PSO**

### **Atualização da velocidade**

$$
\begin{aligned}
\mathbf{v}_i(t)={}&
\omega\mathbf{v}_i(t-1)\\
&+c_1\phi_1(\mathbf{p}_i-\mathbf{x}_i(t-1))\\
&+c_2\phi_2(\mathbf{p}_g-\mathbf{x}_i(t-1))
\end{aligned}
$$

A equação é composta por três termos: inércia, componente cognitivo e componente social.

- *Inércia:* Representa a tendência da partícula de continuar seu movimento anterior. O parâmetro $\omega$ determina quanto da velocidade anterior será preservado.

- **Componente cognitivo:* A diferença entre a melhor posição individual e a posição atual produz um vetor que aponta em direção ao pBest. Esse vetor indica a direção em que a partícula deve se mover para retornar à sua melhor posição histórica

- *Componente social:* Possui a mesma lógica do componente cognitivo, mas direciona a partícula para a melhor solução conhecida coletivamente.

Os parâmetros $c_1$ e $c_2$ controlam a importância de cada componente. Os fatores $\phi_1$ e $\phi_2$ são taxas de aprendizagem, usualmente sorteadas no intervalo $[0,1]$ no PSO convencional.

### **Atualização da posição**

Depois de calcular a nova velocidade, atualizamos a posição:
$$\mathbf{x}_i(t)=\mathbf{x}_i(t-1)+\mathbf{v}_i(t)
$$

### **Influência dos parâmetros**

#### **Inércia ($\omega$)**

A inércia controla quanto do movimento anterior é preservado. Uma possivel estrategia usando ela é o decaimento linear ao longo das iteracoes. Dessa forma, no inicio teriamos maior exploracao pois a direcao anterior teria muito peso e portanto explorariamos pouco a direcoes melhores, ao final com a inercia pequena focamos em usar mais o conhecimento dos melhores.

#### **Coeficientes cognitivo e social**

Um valor elevado de $c_1$, relativamente a $c_2$, tende a preservar a independência das partículas, enquanto um valor elevado de $c_2$, relativamente a $c_1$, tende a concentrá-las nas melhores regiões conhecidas pelo grupo.

### **Exploration X Explotation**

- *Favorece exploração:* Maior inércia, Maior influência individual relativa, Mutação das partículas

- **Favorece explotation:** Menor inércia, Maior influência social relativa, Concentração nas melhores soluções

O excesso de explotation pode provocar convergência prematura, situação em que as partículas se concentram em um ótimo local e perdem diversidade antes de encontrar o ótimo global

### **Fator de constrição ($\chi$)**

O fator de constrição $\chi$ é uma alternativa a inércia para reduzir o numero de parametros e é calculado a partir dos coeficientes cognitivo e social.

$$
\chi=\frac{2}
{\left|2-\beta-\sqrt{\beta^2-4\beta}\right|}
$$

Onde:
$$
\beta=c_1+c_2
$$

A atualização apresentada no PDF é:
$$
\begin{aligned}
\mathbf{v}_i(t)={}&
\chi\mathbf{v}_i(t-1)\\
&+c_1\phi_1(\mathbf{p}_i-\mathbf{x}_i)\\
&+c_2\phi_2(\mathbf{p}_g-\mathbf{x}_i)
\end{aligned}
$$

A finalidade é controlar a dinâmica das partículas e favorecer a convergência do enxame.

### **Limitação da velocidade**

Outro mecanismo utilizado para controlar o deslocamento das partículas é limitar a velocidade máxima.
$$-V_{\max}\leq v_{ij}\leq V_{\max}
$$

Para cada dimensão da partícula:
$$
v_{ij}\leftarrow
\begin{cases}
V_{\max},&v_{ij}>V_{\max}\\
-V_{\max},&v_{ij}<-V_{\max}\\
v_{ij},&\text{caso contrário}
\end{cases}
$$

A limitação reduz o risco de deslocamentos excessivos, mas não garante, sozinha, que a posição permaneça dentro do espaço de busca. Também pode ser necessário tratar os limites da posição

## **Topologias de comunicacao com vizinhança**


A vizinhança determina com quais partículas um indivíduo compartilha informações. Existem dois conceitos de distância: 

- *Geográfica:* Baseada na proximidade das partículas no espaço de busca, podendo utilizar a distância euclidiana.
- *Social:* Baseada nas conexões entre partículas, independentemente de suas posições no espaço de busca.

Uma partícula pode estar distante de outra no espaço de busca, mas ainda assim ser sua vizinha social.

### **Principais topologias**

- *Estrela (global):* Todas as partículas se comunicam entre si.Tem convergência mais rápida e possivelmente prematura pois a melhor solucao fica disponivel para todos.

- *Anel:* Cada partícula se comunica normalmente com dois vizinhos. Cobre melhor o espaco de busca pela limitacao de informacao mas converge mais lentamente.

- *Focal (Wheel):* Existe uma partícula central responsável pela comunicação entre os demais indivíduos. A informação passa pelo ponto focal, em vez de ser compartilhada diretamente entre todas as partículas.

- *Von Neumann:* As partículas são organizadas em uma grade, com quatro vizinhos por partícula. A aula destaca seu bom desempenho empírico em diversos problemas.

## **Algortimo**

```text
Inicializar posições e velocidades aleatoriamente

Avaliar a fitness das partículas
Inicializar pBest de cada partícula
Inicializar gBest do enxame

Enquanto o critério de parada não for satisfeito:

    Para cada partícula:

        Calcular nova velocidade

        Limitar velocidade, se necessário

        Calcular nova posição

        Avaliar fitness da nova posição

        Se a nova posição for melhor que pBest:
            Atualizar pBest

        Se a nova posição for melhor que gBest:
            Atualizar gBest

Retornar gBest
```

### **Metodos hibridos**

- *PSO com seleção:* as partículas são classificadas de acordo com seu desempenho. A metade com pior desempenho é substituída pela melhor metade, preservando as memórias individuais conforme o algoritmo apresentado.

- *PSO com mutação gaussiana:* adicionamos uma perturbação aleatória à posição das partículas após sua atualização. Algumas versões aplicam a mutação apenas à melhor solução global.

# **Otimizacao de Hiperparametros (HPO)**

Objetivo é encontrar automaticamente configurações de hiperparâmetros que produzam modelos de aprendizado de máquina com melhor desempenho

- *Parâmetros* são valores aprendidos durante o treinamento de um modelo como regras em uma arvore ou pesos em modelos neurais
- *Hiperparâmetros* são configurações definidas antes do treinamento que influenciam o processo de aprendizagem ou a estrutura do modelo como profundidade maxima em arvores e numero de camadas em redes

Existem diferentes niveis de automatizacao:

- HPO escolhe hiperparâmetros de um algoritmo.
- Seleção de algoritmos	escolhe qual algoritmo utilizar.
- Uma abordagem combinada escolhe algoritmo e seus hiperparâmetros.
- AutoML escolhe composição e configuração de pipelines completos.

## **Formulacao**

Podemos escrever a função objetivo como:
$$
\Psi(\lambda)=
\frac{1}{|D_{\mathrm{valid}}|}
\sum_{x\in D_{\mathrm{valid}}}
\mathcal{L}\left(x,
\mathcal{A}_{\lambda}(D_{\mathrm{train}})\right)
$$

E o problema de otimização como:
$$\lambda^*=\arg\min_{\lambda\in\Lambda}\Psi(\lambda)
$$ 

Onde:

- $\lambda$: Vetor de hiperparâmetros
- $\Lambda$: Espaço de configurações possíveis
- $\mathcal{A}_{\lambda}$: Algoritmo configurado com $\lambda$
- $D_{\mathrm{train}}$: Conjunto de treinamento
- $D_{\mathrm{valid}}$: Conjunto de validação
- $\mathcal{L}$: Função de perda
- $\Psi(\lambda)$: Perda média na validação
- $\lambda^*$: Configuração ótima

A lógica é: 
1. Primeiro, escolhemos uma configuração de hiperparâmetros $\lambda$.

2. Depois, treinamos o modelo utilizando essa configuração:
$
M_\lambda=\mathcal{A}_\lambda(D_{\mathrm{train}})
$

3. Em seguida, calculamos sua perda média no conjunto de validação:
$
\Psi(\lambda)=
\frac{1}{|D_{\mathrm{valid}}|}
\sum_{x\in D_{\mathrm{valid}}}
\mathcal{L}(x,M_\lambda)
$

4. Finalmente, procuramos a configuração que minimiza essa perda. Como geralmente não conseguimos avaliar todas as configurações possíveis, retornamos a melhor entre as $S$ configurações efetivamente avaliadas:
$$
\hat{\lambda}=
\arg\min_{\lambda\in\{\lambda^{(1)},\ldots,\lambda^{(S)}\}}
\Psi(\lambda)
$$

## **Grid Search**

O Grid Search (busca em grade) discretiza cada hiperparâmetro em um conjunto de valores e testa todas as combinações possíveis.

Por exemplo, considere uma SVM com dois hiperparâmetros:
$$
C\in\{0,1;\ 1;\ 10\}
$$

$$
\sigma\in\{0,01;\ 0,1;\ 1\}
$$

O Grid Search avaliará as nove combinações possiveis. Se cada hiperparâmetro d possui uma quantidade diferente de valores possiveis temos a seguinte quantidade de possibilidades:

$$N=\prod_{i=1}^{d}k_i
$$

Sua principal vantagem é que é simples, facil de paralelizar e pode ser muito util quando existe conhecimento especializado para delimitar os parametros. Entretanto, seu custo é exponencial, gasta computacao em regioes claramente ruins de forma cega e é limitado a encontrar apenas as melhores solucoes dentro da grade discretizada.

## **Random Search**

O Random Search (busca aleatória) seleciona configurações aleatoriamente dentro do espaço de hiperparâmetros. Em vez de testar todas as combinações possíveis, definimos um orçamento de $N$ avaliações.

#### **Por que o Random Search pode ser melhor que o Grid Search, mesmo sendo aleatório?**

A dimensionalidade efetiva representa a quantidade de dimensões que realmente influenciam significativamente a função objetivo. *Na realidade poucos hiperparâmetros são realmente relevantes para determinado dataset, e os hiperparâmetros relevantes podem mudar de um dataset para outro.* A busca aleatória pode permitir explorar diversas regioes e com alta probabilidade encontrar pelo menos uma solucao boa conforme amostramos mais e conforme a proporcao da dimensionalidade efetiva aumenta.

## **Bayesian Optimization (BO)**

Tanto Grid Search quanto Random Search são classificados como métodos cegos (blind methods), pois não utilizam os resultados anteriores para orientar a escolha das próximas configurações. A Otimização Bayesiana procura utilizar as avaliações anteriores para *escolher de maneira inteligente a próxima configuração* de hiperparâmetros.

Ela é especialmente adequada para funções objetivo:
- Caixa-preta (black-box)
- Computacionalmente caras
- Multimodais, ou seja, com múltiplos ótimos locais
- Possivelmente ruidosas

### **O que é uma função caixa-preta?**

Uma função caixa-preta é uma função cuja expressão analítica não conhecemos e cujas derivadas não estão diretamente disponíveis.

Por exemplo:
$$
f(\lambda)=\text{erro de validação da rede neural}
$$

Não temos uma equação explícita relacionando com pesos e constantes o número de camadas, o learning rate e os demais hiperparâmetros ao erro final. Para descobrir $f(\lambda)$, precisamos efetivamente treinar e avaliar o modelo.

Se avaliar a função objetivo for barato, podemos testar muitas configurações. Entretanto, se cada avaliação demorar horas, precisamos reduzir a quantidade de avaliações. É nesse cenário que a Otimização Bayesiana se torna interessante.

### **Componentes da Otimizacao Bayesiana**

È composto por 2 componentes principais:

- *Surrogate Model:* O proprosito aqui é estimar o desempenho de uma determinada combinação de solução (hiperparametros) e junto ter a incerteza dessa estimativa.
    - Quão boa deteminada combinação é ? quanto de certeza temos nisso ?

- *Aquisition Function:* Decide para onde seguir na exploração do espaço
    - Qual a proxima combinação/solução devemos testar ?

![alt text](imgs/BO.png)

A grande vantagem é que avaliar o modelo surrogate para ter uma estimativa de qualidade é muito mais barato do que treinar o modelo e conseguir esse valor real. Então podemos escolher regiões especificas de maior potencial para treinar e alimentar os modelos com valores reais e assim reduzir incertezas de outras regiões.

### **Algortimo da Otimização Bayesiana**

1. Inicialmente executamos o modelo com algumas soluções iniciais (Hiperparametros) e conseguimos uma amostra diversa de varios pontos no espaço e suas respectivas losses/perdas reais (ou seja, incerteza 0 nesses pontos pois são reais da função objetivo). O objetivo aqui é conhecer um pouco outputs da função objetivo para aprender a estimar.

2. Ajustamos o modelo surrogate nessas observações para ele aprender a estimar a função objetivo e a incerteza das estimativas.

3. Utilizamos a função de aquisição para determinar o proximo ponto/solução devemos explorar. Essa decisão é baseada na estimativa de qualidade do sorrogate e da incerteza associada.

4. Treinamos o modelo real com hiperparametros escolhidos pela aquisição, ou seja, conseguimos o valor real da função objetivo para aquela instancia com potencial.

5. Adicionamos esse novo resultado real da função objetivo junto das soluções iniciais executadas. Atualizamos/treinamos o sorrogate com essa nova informação e repetimos o processo por N iterações em que N é o orçamento.

```text
Realizar algumas avaliações iniciais
Armazenar configurações e resultados

Enquanto houver orçamento de avaliações:

    Ajustar modelo substituto
    Estimar função objetivo e incerteza das previsões

    Construir função de aquisição

    Encontrar configuração que maximiza
    a função de aquisição

    Treinar modelo com essa configuração
    Avaliar seu desempenho real

    Adicionar resultado às observações
    Atualizar melhor configuração encontrada

Retornar melhor configuração encontrada
```

### **Processo Gaussiano (GP)**

