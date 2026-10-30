# **Rank Aggregation**

## **Problema**

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

- **Pontuacao do rank final:** Definir como vamos agregar as pontuacoes dos ranks em uma pontuacao final. Isso pode ser simplesmente a soma/media dos scores nos ranks mas também podemos ponderar com base em valor ou confiabilidade das origens.

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

#### **Problema**

Temos um problema em relacao um possivel ciclo de preferencia em que:

$$
A\succ B,\quad B\succ C,\quad C\succ A
$$

A relação de preferência majoritária não é necessariamente transitiva. Portanto, simplesmente ordenar documentos pelas vitórias par a par pode não produzir uma ordem consistente. Existem métodos para resolver esses conflitos, como Copeland e Kemeny.

### **Kemeny Optimal Aggregation (agregar minimizando discordâncias)**

