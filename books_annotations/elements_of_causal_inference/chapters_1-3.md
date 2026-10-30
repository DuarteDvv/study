# **Estatistica e modelos causais**

## **Teorica de probabilidade X Estatisticas**

Em probabilidade já conhecemos o processo gerador, ou seja, a distribuicao verdadeira e queremos analisar os seus possiveis resultados através de experimentos com diversos parametros para gerar dados.

- Entretanto, em estatística o processo é contrário, nos temos os resultados e queremos afirmar coisas sobre o processo/distribuicao geradora em que geralmente temos tuplas ((x1,y1)...) chamadas de observacoes/linhas em que assumimos que cada uma delas sao i.i.d, ou seja, independentes e vieram da mesma distribuicao multivariada de origem chamada Pxy. O maior problema na estatistica é que desconhecemos a distribuicao real/original dos dados.
- *Joint Distribution Pxy* descreve a probabilidade de duas ou mais variáveis aleatórias ocorrerem simultaneamente dentro de intervalos ou conjuntos específicos de valores e possuem uma função de probabilidade conjunta p(x, y) = P(X = x, Y = y). Isso é equivalente a P(x intersect y) e todas as operacoes como bayes.
- *Conditional Distribution* é probabilidade de uma variável assumir um valor sabendo que a outra já assumiu um valor específico ($P(Y=y \vert{} X=x) = \frac{p(x,y)}{p_X(x)}$).

### **Revisao operacoes com variavel aleatórias**

#### **Com constante**

- *Média:* 
    - $E(X \pm c) = E(X) \pm c$ (A média desloca-se pelo valor da constante).
    - $E(cX) = c \cdot E(X)$

- *Variância:* 
    - $Var(X \pm c) = Var(X)$ (Somar uma constante não muda a dispersão dos dados).
    - $Var(cX) = c^2 \cdot Var(X)$ (A constante sai da variância elevada ao quadrado).

#### **Com outra variavel**


- *Média (Sempre Linear):* A média da soma ou subtração é sempre a soma ou subtração das médias, independentemente de haver independência ou não:
    - $E(X + Y) = E(X) + E(Y)$
    - $E(X - Y) = E(X) - E(Y)$

- Variância (Depende da relação entre elas)

    - Se X e Y forem INDEPENDENTES:
	    - $Var(X + Y) = Var(X) + Var(Y)$
	    - $Var(X - Y) = Var(X) + Var(Y)$ 
        (Nota: O sinal continua positivo porque a variância mede dispersão, e subtrair duas V.A.s independentes aumenta a incerteza total).
    - Se X e Y forem DEPENDENTES:
        - $Var(X + Y) = Var(X) + Var(Y) + 2 \cdot Cov(X,Y)$
        - $Var(X - Y) = Var(X) + Var(Y) - 2 \cdot Cov(X,Y)$


## **Learning Theory**

Investiga quando um modelo treinado/adaptado com dados empiricos (amostras) consegue se sair bem na inferencia em dados novos. Para isso ele precisa de:

- *Generalizacao:* Independente de quantas amostras (tamanho finito) temos nunca vamos conseguir mapear perfeitamente todas as saidas da funcao geradora dos dados, se agarrar muito a essa amostra pode levar a problemas de generalizacao como overfitting.
- *risco real e risco empirico:* idealmente queremos minimizar o risco/probabilidade real do nosso modelo errar porém para isso seria necessária a distribuicao de origem e portanto usamos o risco empirico buscando funcoes que minimizem ambos risco empirico e verdadeiro.
- *Capacidade:* tem que ser complexo suficiente para aprender os padroes
- *Consistente:* Se mais dados chegarem ele precisa se aproximar mais ainda do menor risco real 

## **Causal Reasoning and Learning**

![alt text](imgs/causal_vs_statistical_learning.png)

Modelos causais possuem mais informacao que modelos estatisticos pois eles permitem analisarmos o efeito de alteracoes em causas. O autor distingue 2 processos diferentes mas complementares:
- Causal Reasoning/Inference: Já sabemos o modelo causal, ou seja, temos o grafo que mapeia as causas nas variaveis. Isso permite fazer operacoes de inferencia causal (Conceito: Prever efeitos de intervencoes em variaveis) através de intervencoes nas causas. 
    - Reasoning é equivalente a probabilidade na inferencia classica uma vez que em ambos já temos os modelos e apenas queremos analisar os resultados gerados por esse modelo
- Causal Learning/Discovery: Nao temos a estrutura real ainda e precisamos estimar essa estrutura usando dados. Esse segundo problema é bem mais dificil uma vez que varios grafos/modelos podem gerar a mesma distribuicao empirica, ou seja, os dados que usamos e mesmo conhecendo a distribuicao real Pxy nao é trivial dizer que X causa Y pois por exemplo nao sabemos a direcao e também pode existir uma cofounder variable nao observada nos dados.
    - Learning é equivalente a statistical learning em que tentamos aprender o modelo real através de dados empiricos gerados por ele.

### **Princípio da causa comum de Reichenbach**

- Antes de ver o principio é bom lembrar: Ser estatisticamente independente significa que a ocorrência de um evento ou o valor de uma variável não altera, influencia ou traz nenhuma informação sobre a probabilidade de outro evento ou variável acontecer

Esse principio diz que se duas variaveis aleatórias sao estatisticamente dependentes, existe uma causa que explica essa dependencia e essa causa pode ser uma das 2 variaveis ou ate mesmo uma terceira variavel. Entretanto, se conhecemos essa variavel causadora e condicionarmos nela, a relacao de dependencia some, ou seja, se fumar causa tosse, se condicionarmos agora em apenas pessoas que fumam, a tosse nao depende mais de fumar pois fumar é fixo e sem variacao. Além disso, dependencias estatisticas podem surgir de ruidos dos proprios dados e erros cometidos por pessoas.

## **Exemplos**

### **Pattern Recognition**

Imagine 2 modelos causais para um problema de digitos:

1. Rotulo -causa-> Imagem: Quem gerou os dados recebeu um rotulo e depois desenhou o digito 
    - A Imagem é gerada por uma funcao F(Rotulo, Erro)
    - Podemos intervir no rotulo e a imagem será alterada mas intervir na imagem n muda o rotulo

2. Intencao -causa-> Rotulo: quem gerou os dados pensou um pouco e decidiou ambos através de um pensamento/intencao
    L-causa-> Imagem
    - A Imagem é gerada por uma funcao G(Intencao, Erro) e o Rotulo por uma funcao H(Intencao, Erro)
    - Podemos intervir no rotulo mas isso nao altera a imagem pois ele nao causou ela

Ambos os modelos podem gerar os mesmos dados observacionais mas as intervencoes possiveis sao bem diferentes. Essa é a motivação dos modelos causais estruturais (SCMs), representar explicitamente os mecanismos que geram as variáveis, ou seja, funcoes e tudo necessário para reproduzir os dados.

### **Gene Perturbation**

Imagine agora 2 modelos causais para um problema de atividade genetica:

1. Gene A -causa-> Fenotipo
    - Se reduzirmos experimentalmente a atividade do gene A a zero, esperamos uma mudança no fenótipo, pois existe uma relação causal direta.
2. Gene C -causa-> Gene B
    L-causa-> Fenotipo
    - Nesse caso, a atividade do gene B está correlacionada ao fenótipo, mas não é sua causa. Consequentemente, intervir no gene B não altera necessariamente o fenótipo.

Os dois casos podem apresentar distribuições observacionais idênticas, embora as intervenções tenham consequências diferentes.
  

![alt text](imgs/genes_intervention.png)

# **Causal Assumptions**

## **Principio de Mecanismos Causais Independentes (ICM)** 

Considere um exemplo com 2 variaveis: T -> temperatura e A -> altitude. Temos 2 possiveis hipoteses de causalidade:

1. A -> T, altitude causa temperatura e portanto a distribuicao conjunta P(a,t) pode ser decomposta em P(t|a) * P(a)
2. T -> A, temperatura causa altitude e portanto a distribuicao conjunta P(a,t) pode ser decomposta em P(a|t) * P(t)

È intuitivo perceber que se uma intervencao na causa for feita em ambos os modelos: No primeiro estamos mudando a altitude e devido a fatores fisicos a temperatura também vai mudar. No segundo estamos mudando a temperatura de uma cidade... isso ira fazer com que a cidade comece a voar ? ou entre debaixo da terra ? Claramente o primeiro modelo é mais representativo e isso esta ligado ao mecanismo fisico de interacao entre altitude e temperatura.

### **Premissas**

$$ 
p(a,t)=\underbrace{p(t\mid a)}_{\text{mecanismo causal   }}\underbrace{p(a)}_{\text{    distribuição da causa}}
$$

Ambos os termos sao distribuicoes de probabilidade em que uma é condicional e outra nao, essas distribuicoes sao responsaveis pela geracao dos dados das variaveis e sao chamadas de mecanismos. O primeiro termo é o mecanismo/regra de geracao do efeito (distribuição do efeito T condicionada à causa A. Sob a hipótese causal A -> T) entre causa X e outras variaveis Y que essa causa afeta. O segundo termo é o mecanismo/regra que gerou a causa, ou seja, como essa causa é gerada ou varia. Dito isso, temos algumas premissas:

- *Autonomia/Invariancia entre mecanismos:* O mecanismo do efeito e o mecanismo que gerou a causa sao autonomos, ou seja, podemos modificar um mecanismo sem necessariamente modificar o funcionamento dos demais. Portanto, podemos mudar a regra de geracao da causa (mudar a cidade e consequentemente as altitudes) sem isso afetar em nada o mecanismo do efeito (mesmas regras fisicas de temperatura).

- *Independência informacional entre causa e mecanismo:* O mecanismo que gera a causa não fornece informações sobre o mecanismo que produz o efeito (São independentes informacionalmente e não estatisticamente, ou seja, saber sobre P(C) não necessáriamente te diz algo sobre P(E|C)). Por exemplo, saber que 80% das cidades tem altitude maior que x nao te diz como essa altitude vai afetar a temperatura e nem se apenas ela vai afetar a temperatura.

- *Erros independente:* No modelo causal estrutural a temperatura é gerada através de um mecanismo de interacao f, com parametros A e Nt, ou seja, f(A, Nt) em que A é a altitude e Nt sao outros fatores que afetam a temperatura. A é gerado através de fatores f(Na). Idealmente Na e Nt sao independentes pois caso contrário quebra a premissa de autonomia.

    - $N_A$ e $N_T$ são variáveis de ruído (noise), também chamadas de variáveis exógenas. Elas representam fatores que influenciam uma variável, mas que não estão explicitamente modelados como suas causas.

## **Modelos Causais e processos fisicos**

### **Tempo**

O tempo fornece uma assimetria natural: causas não podem influenciar acontecimentos no passado. Entretanto, a ordem temporal não é suficiente para estabelecer causalidade. Por exemplo, mesmo que X aconteça antes de Y, isso não prova que X causa Y.

### **Ciclos**

Até aqui, consideramos principalmente estruturas causais sem ciclos:
$$
X\longrightarrow Y
$$

Entretanto, alguns sistemas apresentam retroalimentação (feedback):
$$
X\longrightarrow Y\longrightarrow X
$$

Isso pode ser representado pelas equações estruturais:
$$
X:=f_X(Y,N_X)
$$
$$
Y:=f_Y(X,N_Y)
$$

Nesse sistema, X depende de Y, enquanto Y também depende de X. O problema é que não podemos simplesmente calcular uma variável antes da outra.

Uma possibilidade é procurar um ponto de equilíbrio por iteração:
1. Escolhemos um valor inicial para Y.
2. Calculamos X a partir desse valor.
3. Utilizamos o novo X para calcular Y.
4. Repetimos o procedimento até que os valores parem de mudar.

Entretanto, a convergência não é garantida pois o sistema pode não possuir equilíbrio, possuir múltiplos equilíbrios ou não convergir pelo método iterativo.

### **Intervencoes possiveis**

O princípio dos mecanismos independentes pressupõe que podemos intervir em um mecanismo sem necessariamente modificar os demais. Na prática, algumas intervenções afetam vários mecanismos simultaneamente. Por exemplo, aumentar a temperatura de um sistema físico pode alterar também sua pressão e outras propriedades. Para determinar quais intervenções são realmente independentes, muitas vezes precisamos conhecer a estrutura causal do sistema. Porém, essa estrutura é justamente aquilo que estamos tentando descobrir. Assim, compreender os mecanismos físicos pode ser necessário para justificar nossas hipóteses causais.

# **Cause-Effect Models**

Conceitos basicos para o caso em que o modelo tem apenas duas variaveis

## **Structural Causal Models**

Um SCM com duas variáveis é definido por:

$$
\begin{aligned}
C&:=N_C\\
E&:=f_E(C,N_E)
\end{aligned}
$$

Em que C é a causa, E é o efeito, Nc e Ne sao as variaveis exogenas que influenciam essas variaveis (ruido) e f é o mecanismo/funcao que transforma a causa em efeito. No grafo causal vamos ter 2 vértices (C, E) e uma aresta direcionada de C para E. 

- O SCM descreve como as variáveis são geradas, e não apenas como estão estatisticamente relacionadas. Para gerar dados com esse modelo causal comecamos gerando C usando uma amostragem de Nc, depois condicionado em C e no ruido Ne geramos E para no fim termos uma distribuicao de probabilidade P(C,E). 

- Temos dois tipos de variáveis:
    - Endógenas C: são determinadas pelas equações estruturais. Podemos observá-las total ou parcialmente.
    - Exógenas $N_C,N_E$: representam fatores externos ao modelo, cujos valores individuais geralmente desconhecemos e chamamos de ruido

- Repare que SCMs nao é o grafo isoladamente mas a soma do grafo + todas as relacoes/equacoes causais definidas, ou seja, 2 SCMs com equacao E:=... diferentes e mesmo grafo ainda sao diferentes.



## **Interventions (Intervenções)**

Uma intervenção consiste em modificar externamente o mecanismo que gera uma variável, mantendo os demais mecanismos inalterados, no caso de uma intervenção ideal e localizada. Eis um exemplo de modelo causal em que definimos os mecanismos de como as variaveis sao geradas:

$$
C:=N_C
$$

$$
E:=4C+N_E
$$

onde:
$$
N_C,N_E\overset{iid}{\sim}\mathcal N(0,1)
$$

### **Distribuição antes da intervenção**

Como $C=N_C$:

$$
C\sim\mathcal N(0,1)
$$

E, como $E=4C+N_E$, temos:
$$
E\sim\mathcal N(0,17)
$$

Por quê? Usando a independência dos ruídos e o fato de $Var(c*X)=c^2 * Var(X)$:  $ \operatorname{Var}(E)=16\operatorname{Var}(C)+\operatorname{Var}(N_E) =16+1=17 $

Portanto, a distribuição original do efeito é $E\sim\mathcal N(0,17)$.

### **Intervenção sobre a causa**
Agora fazemos:
$
do(C:=2)
$

Substituímos o mecanismo original $C:=N_C$ por $C:=2$.

O mecanismo de $E$ permanece inalterado:
$
E=4(2)+N_E=8+N_E
$

Logo:
$
E\mid do(C:=2)\sim\mathcal N(8,1)
$

A intervenção na causa modificou a distribuição do efeito. 

### **Intervenção sobre o efeito**
Agora fazemos:
$
do(E:=2)
$

Substituímos a equação $E:=4C+N_E$ por $E:=2$.

Mas a equação da causa permanece:
$
C:=N_C
$

Portanto:
$C\mid do(E:=2)\sim\mathcal N(0,1)$

Intervir na causa altera o efeito, mas intervir no efeito não altera a causa.

### **Intervenção versus observação**
Uma das distinções mais importantes do capítulo é:
$$
P(C\mid E=2)\neq P(C\mid do(E:=2))
$$

Observar $E=2$ fornece informações sobre $C$, porque $E$ depende de $C$. Entretanto, forçar $E=2$ não modifica $C$, pois estamos substituindo o mecanismo responsável por gerar E com base nas causas C, ou seja, nao podemos mais dizer nada sobre C pois o mecanismo que gerou E foi nossa intervencao. Essa diferença explica por que condicionamento estatístico e intervenção causal não são equivalentes.

### **Hard e Soft Interventions**

- *Hard intervention:* Substitui a equação estrutural por um valor fixo, como $E:=4$.
- *Soft intervention:* Modifica o mecanismo, mas pode preservar a dependência em relação às causas. Por exemplo trocar $E:=4C+N_E$ por: $E:=2C+\widetilde N_E$.

## **Counterfactuals**

Um contrafactual consiste em determinar o que teria acontecido com uma unidade específica (instancia como paciente), sobre a qual já possuímos informações, caso uma intervenção diferente tivesse sido realizada.

- O que teria acontecido com esta mesma unidade se tivéssemos tomado uma decisão diferente?

### **Exemplo**

- 99% melhoram com o tratamento e pioram sem ele.
- 1% apresentam o comportamento oposto.
O modelo é:

$$
T:=N_T
$$
$$
B:=TN_B+(1-T)(1-N_B)
$$

Onde:
- T=1: paciente recebe tratamento.
- T=0: paciente não recebe tratamento.
- B=1: perda da visão.
- B=0: recuperação.
- N_B=1: paciente pertence ao grupo raro de 1%.

### **Observamos um paciente específico**

Suponha que um paciente recebeu tratamento e perdeu a visão: $ T=1, B=1 $

Substituindo na equação:
$
1=1\cdot N_B+0\cdot(1-N_B)
$

Logo: $ N_B=1$

Descobrimos que ele pertence ao grupo raro.

### **Fazemos a pergunta contrafactual**

O que teria acontecido se esse mesmo paciente não tivesse recebido tratamento?

Agora fazemos: $ do(T:=0) $

Mas mantemos a informação obtida sobre o paciente: $ N_B=1 $

Substituindo: $ B=0\cdot 1+(1-0)(1-1) = 0$

Portanto, o modelo conclui que esse paciente teria se recuperado sem o tratamento.

### **Observacao, intervenção e contrafactual**

Um contrafactual também envolve uma intervenção, mas acrescenta uma etapa anterior que é usar as evidências observadas para inferir informações sobre a unidade. Logo:

- *Intervenção:* modificamos o mecanismo causal e estudamos suas consequências na distribuicao dos efeitos, ou seja, o resultado aqui é a distribuição do efeito sob a intervenção. O resultado aqui é uma distribuicao pois aqui usamos distribuicoes do ruido e nao valores exatos de individuos.
    - $\underbrace{P(Y\mid do(X=3))}_{\text{Intervenção}}$

- *Contrafactual:* primeiro utilizamos observações para atualizar nosso conhecimento sobre um individuo depois modificamos hipoteticamente o mecanismo e calculamos o resultado alternativo.
    - $\underbrace{P(Y_3\mid X=2,Y=7)}_{\text{Contrafactual}}$

    - O procedimento contrafactual consiste em três etapas:
        1. *Abdução:* usar as observações para atualizar as distribuições dos ruídos, ou seja, usamos dados para estimar a variavel exogena (ruido) que nao observamos (permite preservar as características ocultas do mesmo indivíduo). Essa estimacao pode ser um unico valor ou uma distribuicao também.
        2. *Ação:* modificar a equação estrutural por meio de uma intervenção em causas
        3. *Predição:* calcular o resultado no cenário alternativo na variavel efeito

- *Condicionamento observacional:* simplesmente olhar a distribuicao atual, ou seja, não alteramos nenhuma equação estrutural e somente selecionamos observações em que X=3
    - $ \underbrace{P(Y\mid X=3)}_{\text{Observação}}$

## **Representação canônica de SCMs**

Um SCM: $E:=f_E(C,N_E)$

Quando fixamos $N_E=n_E$, ou seja, fixamos um valor exato de ruido obtemos uma função determinística: $E=f_E(C,n_E)$

Portanto, podemos interpretar o ruído como um funcao que mapeia para a equacao com aquele ruido: $E:=n_E(C)$

Essa é a *representação canônica*, em que cada valor do ruído corresponde a uma função que determina o efeito para todos os possíveis valores da causa.

### **Exemplo**

Considere $C,E\in\{0,1\}$, com ruídos independentes de C

**SCM A:**
$$
E:=N_E,\qquad N_E\sim Ber(0{,}5)
$$

O ruído seleciona as funções constantes 0 ou 1.

- 50% dos indivíduos: $(E_0,E_1)=(0,0)$.
- 50% dos indivíduos: $(E_0,E_1)=(1,1)$.

**SCM B:**

$$
E:=C\oplus N_E,\qquad N_E\sim Ber(0{,}5)
$$

O ruído seleciona as funções identidade ou negação (XOR).

- 50% dos indivíduos: $(E_0,E_1)=(0,1)$.
- 50% dos indivíduos: $(E_0,E_1)=(1,0)$.


Ou seja, ambos apresentam as mesmas distribuições observacionais e intervencionais: $ P(E_0=1)=P(E_1=1)=0{,}5 $

Porém, seus contrafactuais são diferentes:

- **SCM A:** mudar \(C\) nunca altera \(E\) para o mesmo indivíduo.
- **SCM B:** mudar \(C\) sempre altera \(E\) para o mesmo indivíduo.

Dois SCMs podem gerar as mesmas distribuições observacionais e intervencionais, mas contrafactuais diferentes

- **Intervenções:** dependem das distribuições marginais $P(E_0)$ e $P(E_1)$, em que $E_c$ é o efeito sob $do(C=c)$
- **Contrafactuais:** podem depender da distribuição conjunta $P(E_0,E_1)$, que descreve como os resultados potenciais se relacionam para o mesmo indivíduo

Portanto, conhecer todas as distribuições intervencionais não é necessariamente suficiente para determinar os contrafactuais.

## **Problemas**

1. Dado o modelo gerar 200 observações independentes da distribuição conjunta de (X,Y)
    $$
    X:=Y^2+N_X
    $$

    $$
    Y:=N_Y
    $$

    com:
    $$
    N_X,N_Y\overset{iid}{\sim}\mathcal N(0,1)
    $$

    - Resposta:

        ```py

            import numpy as np

            rng = np.random.default_rng(42)

            ny = rng.normal(0, 1, 200)
            nx = rng.normal(0, 1, 200)

            y = ny
            x = y**2 + nx
        ```
2. Dado 2 modelos, uma única distribuição intervencional permite distinguir os dois modelos ?

    Modelo 1:
    $$
    X:=Y+N_X,\qquad Y:=N_Y
    $$

    Portanto:
    $$
    Y\longrightarrow X
    $$

    Modelo 2:
    $$
    X:=M_X,\qquad Y:=X+M_Y
    $$

    Portanto:
    $$
    X\longrightarrow Y
    $$

    - Resposta:

        Se fixarmos X = x, sendo x um valor fixo qualquer, no modelo 1 Y:= Ny enquanto no 2 Y:= x + My, porém fazendo dessa maneira pode acontecer de Ny por sorte assumir um valor igual a x + My.

  