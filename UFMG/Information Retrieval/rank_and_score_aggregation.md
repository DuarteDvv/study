# **Rank Aggregation**

## **Motivacao**

É um problema onde temos N listas ordenadas, ou seja, ranks gerados por diferentes fontes/modelos que trazem diferentes tipos de informacao, preferencias e confiabilidades. A ideia é apartir deles gerar um unico rank que seja melhor que os N individualmente.

Imagine que temos 3 rankings independentes:
- BM25, que utiliza correspondência lexical
- Dense Retrieval, que utiliza representações vetoriais
- Um terceiro modelo baseado em expansão de consultas

| Posição | BM25 | Dense Retrieval | Expansão |
|---|---|---|---|
| 1º | A | B | B |
| 2º | B | C | A |
| 3º | C | A | D |
| 4º | D | D | C |

Qual deveria ser o ranking final? O documento B aparece em primeiro lugar em dois modelos e em segundo no outro. O documento A também aparece relativamente bem, mas não tão consistentemente.

Uma agregação razoável seria:
$$
B \succ A \succ C \succ D
$$

Onde, $B\succ A$ significa que o documento B está acima de A no ranking. Entretanto, essa resposta não é automática. Precisamos definir uma regra matemática/funcao que determine como agregar as listas e existem diversas regras possíveis, cada uma com propriedades diferentes.

## **Formulacao Matematica**

Temos um conjunto de documentos:

$$
D=\{d_1,d_2,\ldots,d_n\}
$$

e temos m rankers/modelos que geram ranks diferentes:

$$
R_1,R_2,\ldots,R_m
$$

O objetivo é encontrar uma função de agregação $f$ que produza um ranking final:

$$
R^*=f(R_1,R_2,\ldots,R_m)
$$

Podemos ver oobjetivo como:
1. *Encontrar um consenso:* produzir um ranking que concorde o máximo possível com os rankings originais
2. *Maximizar a relevância:* utilizar as informações dos rankings para tentar produzir a melhor ordenação possível para o usuário

Esses objetivos não são equivalentes. Se todos os modelos cometerem o mesmo erro, um ranking consensual também poderá estar errado.

## **Metodos**

### **Borda Count (agregação por posições)**

A ideia é atribuir pontos para os documentos em cada um dos ranks independentemente, em seguida somamos os pontos de todos os ranks e geramos um uma pontuacao final para o documento, ordenamos por essa pontuacao e temos o rank final

#### **Funcao de pontuacao e agregacao de pontos** 

Para gerar um score para cada documento em cada ranking e depois somar, precisamos definir 2 funcoes:

- *Pontuacao nos ranks:* Definir quanto cada posicao daquele ranking ira gerar de score. Isso pode ser simplesmente a propria inversa da posicao (m - pos), ou seja, [4,3,2,1]. Mas também podemos atribuir pesos em outras posicoes gerando algo ponderado.

- *Pontuacao do rank final:* Definir como vamos agregar as pontuacoes dos ranks em uma pontuacao final. Isso pode ser simplesmente a soma/media dos scores nos ranks mas também podemos ponderar com base em valor ou confiabilidade das origens.

#### **Problemas**

O Borda atribui uma importância linear às posições, ou seja, o score do rank decai linearmente com o aumento da posicao. Entretanto em IR as pequenas variacoes nas primeiras posicoes sao muito mais importantes que variacoes nas posicoes inferiores.

Se temos 2 ranks: 
| Documento | Ranking 1 | Ranking 2 |
| :--- | :--- | :--- |
| A | 1º | 100º |
| B | 2º | 50º |

Qual documento deveria ficar na frente? O documento A é o melhor resultado do primeiro sistema, mas aparece em último no segundo. Já B é consistentemente melhor posicionado.

Utilizando Borda Count
A pontuação é:
$$
S(d)=\sum_{j=1}^{2}(101-r_j(d))
$$

Para A:$ S(A)=100+1=101 $

Para B:$ S(B)=99+51=150 $

Logo:
$$
B\succ A
$$

O ponto importante é que a vantagem de A na primeira posição é de apenas 1 ponto, enquanto a vantagem de B no segundo ranking é de 50 pontos. Isso permite que diferenças grandes nas posições inferiores compensem uma vantagem no topo sem que necessáriamente seja relevante.


### **Condorcet (agregação por pares)**

Em vez de atribuir um score diretamente a cada documento, ele pergunta: Entre dois documentos A e B, qual deles é considerado melhor pela maioria dos rankings?

#### **Preferencia**

Para documentos x e y dentro de um ranking A, se a posicao de x < y entao A prefere x em relacao a y. Agora podemos repetir isso para todos os pares de documentos e rankings e contar quantas vezes x é preferido em relacao e y e o contrario também. Ao final olhamos qual é mais preferido em relacao ao outro na maioria dos rankings.

$$
V(d_a,d_b)=
\sum_{j=1}^{m}
\mathbb{1}[r_j(d_a)<r_j(d_b)]
$$

| Modelo | Comparação | Preferido |
| :--- | :--- | :--- |
| BM25 | A em 1º, B em 2º | A |
| Dense | B em 1º, A em 3º | B |
| Expansão | B em 1º, A em 2º | B |

$$
V(B,A)=2,\qquad V(A,B)=1
$$

Logo, a maioria prefere B.

#### **Problema**

Temos um problema em relacao um possivel ciclo de preferencia em que:

$$
A\succ B,\quad B\succ C,\quad C\succ A
$$

A relação de preferência majoritária não é necessariamente transitiva. Portanto, simplesmente ordenar documentos pelas vitórias par a par pode não produzir uma ordem consistente. Existem métodos para resolver esses conflitos, como Copeland e Kemeny.

### **Kemeny Optimal Aggregation (agregar minimizando discordâncias)**

A ideia aqui é comparar 2 rankings e medir sua discordancia, tal que discordancia é o numero de elementos nos ranks que nao tem a mesma posicao. A ideia é criar um rank final que minimize a discordancia entre todos os outros ranks, ou seja, transformamos em um problema de otimizacao.

$$
R_1=(A,B,C)
$$

$$
R_3=(C,B,A)
$$

Todos os três pares estão invertidos.

Logo discordancia é:
$$
d_K(R_1,R_3)=3
$$ 

Portanto queremos encontrar um ranking $R^*$ que minimize a soma das distâncias:
$$
R^*=\arg\min_R\sum_{j=1}^{m}d_K(R,R_j)
$$

#### **Desvantagem computacional**

Encontrar exatamente o ranking de Kemeny é NP-hard em geral e por isso, na prática, são utilizados algoritmos aproximativos, busca local e métodos de otimização combinatória.

### **Reciprocal Rank Fusion (RRF)**

O RRF utiliza duas ideias:

1. Documentos em posições altas devem receber pontuações maiores
2. Um documento encontrado por vários rankings deve acumular contribuições de todos

A função de score é:
$$
S_{\text{RRF}}(d)=
\sum_{j=1}^{m}\frac{1}{k+r_j(d)}
$$

Onde $k>0$ é uma constante e $r_j(d)$ representa a posição do documento no ranking $j$.

#### **Papel de k**

Cada posição contribui com um peso:
$$
w(r)=\frac{1}{k+r}
$$

O parâmetro $k$ controla a velocidade com que a pontuação diminui conforme a posição piora. Assim um $k$ pequeno faz as primeiras posições terem uma vantagem proporcional muito maior. Um $k$ elevado torna as contribuições das posições próximas mais semelhantes. Outra propriedade importante desses metodos é que nao precisam conhecer os scores entao basta conhecer suas ordenações e isso é especialmente útil quando os sistemas utilizam escalas de relevância incompatíveis.

# **Score Aggregation**

## **Motivacao**

Sistemas de recuperação normalmente retornam também scores.

| Documento | BM25 score | Dense score |
| :--- | :--- | :--- |
| A | 18,5 | 0,73 |
| B | 14,2 | 0,91 |
| C | 9,7 | 0,82 |

Será que podemos simplesmente somar os scores? Em geral, nao faz sentido somá-los sem considerar suas escalas pois scores de BM25 e os de similaridade vetorial não têm necessariamente a mesma interpretação ou magnitude.

- Score Fusion preserva as magnitudes dos scores dos modelos
- Rank Fusion utiliza apenas as posições entao se aplicarmos uma transformação estritamente crescente aos scores de um modelo, sua ordenação não muda

Em contrapartida, métodos baseados apenas em posições descartam informações potencialmente úteis sobre a intensidade das diferenças entre os documentos (diferenca entre os scores nao é levada em consideracao)

## **Metodos**

### **CombSUM**

Uma abordagem é normalizar os scores e depois somá-los.
$$
S_{\text{CombSUM}}(d)=
\sum_{j=1}^{m}\widetilde s_j(d)
$$

Onde $\widetilde s_j(d)$ é o score do modelo $j$, após uma normalização apropriada.

Uma possibilidade seria utilizar min-max pois essa normalização coloca os scores observados entre 0 e 1, desde que o máximo seja diferente do mínimo. Ela não garante calibração probabilística nem que as diferenças numéricas tenham o mesmo significado entre modelos.

### **CombMNZ**

O CombMNZ introduz um fator de recompensa para documentos identificados por múltiplos sistemas

$$
S_{\text{CombMNZ}}(d)
=
c(d)\sum_{j=1}^{m}\widetilde s_j(d)
$$

Onde c(d) conta o número de sistemas que contribuem com um score positivo para o documento. Dessa forma, documentos presentes em vários resultados recebem uma recompensa adicional.

# **Rank Aggregation supervisionado**

Podemos aprender como os rankings devem ser combinados. Suponha que tenhamos dados de treinamento contendo consultas, documentos e julgamentos de relevância.

Podemos aprender pesos diferentes para cada ranking:

$$
S(d\mid q)=
\sum_{j=1}^{m}
w_j(q)\frac{1}{k+r_j(d\mid q)}
$$

Essa é uma forma de Weighted RRF. Outra possibilidade é aprender um modelo que recebe as posições e, opcionalmente, os scores dos sistemas e aprende um novo score final. Isso esta conectado a Learning to Rank.

# **Reranking e Listas incompletas** 

## **Listas incompletas**

Assumimos que todos os sistemas retornam exatamente os mesmos documentos. Isso nao necessáriamente é verdade em sistemas de busca.
$$
R_1=(A,B,C)
$$
$$
R_2=(D,B,E)
$$

O documento A não aparece em $R_2$, enquanto D não aparece em $R_1$

Como devemos interpretar essa ausência? Depende do algoritmo

- No RRF, uma convenção comum é considerar que documentos ausentes não recebem o score daquele ranking
- No Borda, podemos atribuir posições após o último resultado recuperado ou empregar uma variante específica para listas parciais
- No Condorcet e no Kemeny, precisamos definir como comparar documentos que não aparecem simultaneamente nas listas

Ausência não significa necessariamente irrelevância. Um documento pode simplesmente não ter entrado no top-$k$ produzido por determinado sistema.

## **Reranking vs Rank Agg vs Learn To Rank (LTR)**

- No rank aggregation, combinamos evidências provenientes de múltiplos rankings.

- No reranking, utilizamos um procedimento adicional para reordenar um conjunto de documentos candidatos. Um exemplo é um Cross-Encoder que recebe a consulta e cada documento e produz novos scores de relevância.

    Os dois processos também podem aparecer juntos:
    $$
    \text{BM25 + Dense}
    \xrightarrow{\text{RRF}}
    \text{Candidatos}
    \xrightarrow{\text{Cross-Encoder}}
    \text{Ranking final}
    $$

    Nesse caso, o RRF ajuda a formar e ordenar um conjunto inicial de candidatos, enquanto o Cross-Encoder realiza uma avaliação mais detalhada da relevância de cada documento. O reranker normalmente só consegue reordenar documentos já recuperados. Portanto, se nenhum recuperador encontrar um documento importante, o reranking não conseguirá recuperá-lo por conta própria.

- O Learning to Rank é uma abordagem de aprendizado de máquina em que aprendemos uma função de ranking a partir de dados de treinamento.

    Considere um conjunto de exemplos:
    $$
    \mathcal T=\{(q_i,d_i,\mathbf x_i,y_i)\}_{i=1}^{N}
    $$

    Onde:
    - $q_i$: consulta.
    - $d_i$: documento.
    - $\mathbf x_i$: características do par consulta-documento.
    - $y_i$: julgamento de relevância.

    O modelo aprende uma função:
    $$
    s_\theta(q,d)=f_\theta(\mathbf x(q,d))
    $$

    O treinamento procura ajustar $\theta$ para que documentos mais relevantes sejam posicionados acima dos menos relevantes.