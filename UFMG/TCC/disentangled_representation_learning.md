# **Representation Learning** 

Fonte (2013): https://arxiv.org/abs/1206.5538

A qualidade de um modelo depende fortemente de como os dados são representados, muitas vezes fazemos isso manualmente no pipeline de trabalho e isso é chamado de feature engineering em que construimos novas features baseadas na antigas. Em deeplearning majoritariamente queremos que esse processo aconteça automaticamente durante a otimização do nosso modelo, ou seja, queremos que o modelo aprenda uma transformação f: x -> h tal que x são as features e h uma nova representação de tamanho variado que seja melhor e ajude o modelo na sua tarefa.

*Exemplo:*

- Uma imagem é uma matriz de pixels que assumem valores 0-255 caso seja preto e branca ou tuplas(RGB) caso sejam coloridas. Dentro dessa imagem existem diversas caracteristicas que representam ela como iluminação, objetos, cores e orientações mas essas caracteristicas estão todas misturadas dentro dessa matriz de pixels. Ai entra uma nova representação que deixe mais evidente caracteristicas importantes para a tarefa.

## **O que é uma boa representação?**

O autor do artigo fala de algumas premissas desejadas nessa representação como:

- *Suavidade (smoothness):* entradas semelhantes deveriam gerar representações semelhantes e proximas no espaço
- *Fatores de explicabilidade (Explanatory Factors):* Para gerar a entrada diversos fatores interagiram e uma boa representação deveria identificar ou separar esse fatores
    - Esses fatores podem ser hierarquicos com fatores gerando outros e isso é uma premissa que deep learning usa em suas camadas em que geralmente camadas mais externas são coceitos mais abstratos.
    - Esses fatores também podem ser compartilhados em diferentes tarefas dependendo da similiaridade
- *Esparsidade:* Apenas uma pequena parte dos fatores da entrada são importantes e essa representação tem que saber remover isso zerando

Existem outras premissas mas essas são as principais na minha opinião.

## **Distribuida != Desentrelaçada(Disentangled)**

Uma *representação distribuida* significa que ela é um conjunto/combinação de varias unidades, uma mesma unidade esta dentro de varios fatores e combinações diferentes dessas unidades geram conceitos diferentes. Um exemplo é o embeddings em que temos um vetor de numeros reais (unidades), diferentes combinações geram diferentes locais no espaço.

- Mas isso não significa que ela é *desentrelaçada* pois não sabemos nada sobre seus fatores.

## **Disentangling Factors of Variation**

Além de distribuidas e invariantes seria interessante que a representação separasse fatores de variação uns dos outros, assumindo a hipotese de que esses fatores variam majoritariamente de forma independente. Por exemplo se x é uma imagem gostariamos de uma representação dividida em [h_objeto, h_iluminação, h_posição], aprender essas representações pode tornar o modelo mais interpretavel e robusto.

### **Invariancia e desentrelaçamento**

Uma representação invariante a algum fator y significa que qualquer x que entrar na função geradora, independente de qualquer y diferente que tiver, a representação será igual. Já uma representação desentrelaçada, o fator y estara na representação mas se quisermos podemos ignorar ele mas ele continua la para preservação de informação.

## **Problema principal** 

Queremos uma representação desentrelaçada que separe os fatores de variação mas não sabemos como fazer isso, ou seja, qual função objetivo usar para isso acontecer corretamente, ou seja, conceitualmente conseguimos descrever x como um conjunto de fatores [z1,z2,z3,zk] mas como fazer isso computacionalmente. 

### **Conclusão**

Ao final concluem que um modelo deveria conseguir descobrir esses fatores utilizando vieses indutivos diferentes (suposições).


# **Definição de Disentangled Representation**

Fonte (2018): https://arxiv.org/abs/1812.02230

Esse paper tenta criar uma definição mais formal e matemática do conceito de Disentangled Representation. As definições anteriores eram simplesmente:

- representação disentangled = representação que separa os fatores geradores dos dados

Ai entre algumas questões como o que exatamente é um fator gerador ? cada fator ocupa quantas dimensões ? quais fatores deveriam ser separados dado que podem existir infinitos ? Devido a essa imprecisão o autor tenta troca-la por *"Transformação do mundo"*.

## **Oq sao essas transformacoes ?**

Podemos imaginar que existe um estado real do mundo $w$. Por exemplo:
$$
w=(x,y,c)
$$
onde:
- $x$ = posição horizontal;
- $y$ = posição vertical;
- $c$ = cor.

Podemos aplicar transformações nesse estado, como:

- $\text{mover horizontalmente}$
- $\text{mover verticalmente}$
- $\text{alterar a cor}$

Essas transformações podem ser agrupadas em conjuntos:

$$
G_x,\ G_y,\ G_c
$$
e, no exemplo simples do artigo, o conjunto total de transformações pode ser decomposto como:
$$
G=G_x\times G_y\times G_c
$$

A ideia é que essas transformações representam diferentes maneiras independentes pelas quais o estado do mundo pode variar.

### Estado do mundo, observação e representação

O artigo separa três espaços:
$$
W \rightarrow O \rightarrow Z
$$
- W: estado abstrato do mundo;
- O: observação que o modelo recebe;
- Z: representação interna do modelo.

Por exemplo:
$$
(x,y,\text{cor})
\rightarrow
\text{imagem em pixels}
\rightarrow
\text{embedding}
$$

Existe um processo gerador:
$$
b:W\rightarrow O
$$
e um processo de inferência:
$$
h:O\rightarrow Z
$$
Então podemos juntar os dois:
$$
f=h\circ b
$$
e obter:
$$
f:W\rightarrow Z
$$

Ou seja, f leva um estado do mundo até sua representação latente.


### **Equivariância**

A representação deveria preservar a estrutura das transformações do mundo, ou seja, se aplicamos uma transformação $g$ no mundo e depois calculamos a representação, queremos obter o mesmo resultado que calcular a representação primeiro e depois aplicar a transformação correspondente dentro do espaço latente:

$$
f(g\cdot w)=g\cdot f(w)
$$

Isso é chamado de equivariância.

A ideia é:
$$
\text{transformar mundo}
\rightarrow
\text{representar}
$$
ser equivalente a:
$$
\text{representar}
\rightarrow
\text{transformar representação}
$$
Então a representação não deveria apenas armazenar informação sobre o dado, mas também preservar como o dado muda.

## **Definição de Disentangled Representation**

Se o grupo de transformações pode ser separado:
$$
G=G_1\times G_2\times\dots\times G_n
$$
então queremos que a representação também possa ser decomposta:
$$
Z=Z_1\times Z_2\times\dots\times Z_n
$$
de forma que cada transformação $G_i$ afete apenas seu respectivo subespaço $Z_i$ (causal é diferente). Exemplo:
$$
Z=
[Z_x,Z_y,Z_c]
$$
Se alterarmos somente a cor:
$$
(z_x,z_y,z_c)
\rightarrow
(z_x,z_y,z_c')
$$
Ou seja:
$$
G_c\rightarrow Z_c
$$
mas $G_c$ não deveria alterar $Z_x$ ou $Z_y$. Essa é essencialmente a definição formal proposta no artigo: 

- Mma representação é disentangled em relação a uma determinada decomposição quando pode ser dividida em subespaços independentes, cada um afetado por apenas um dos subgrupos de transformações.

### **Não precisa ser uma dimensão por fator**

Um ponto importante é que:
$$
\text{fator} \neq \text{necessariamente uma dimensão}
$$
Podemos ter:
$$
Z_{\text{cor}}\in\mathbb{R}^{3}
$$
ou até um subespaço maior.
Então uma representação pode ser:
$$
Z=
[Z_{\text{conteúdo}},Z_{\text{estilo}}]
$$
onde cada um desses elementos é um vetor.
O artigo diferencia essa ideia da hipótese mais restrita de que cada fator obrigatoriamente deveria ocupar uma única dimensão. Segundo a definição deles, um subespaço disentangled pode ser multidimensional.

### **Disentanglement depende da decomposição escolhida**

Não existe necessariamente uma única decomposição correta. No exemplo anterior, podemos usar:
$$
G=G_x\times G_y\times G_c
$$
e ter:
$$
Z=[Z_x,Z_y,Z_c]
$$
Mas também podemos agrupar toda a posição:
$$
G=G_p\times G_c
$$
e obter:
$$
Z=[Z_{\text{posição}},Z_{\text{cor}}]
$$
As duas decomposições podem ser válidas. Portanto, uma mesma representação pode ser considerada disentangled em relação a uma decomposição e entangled em relação a outra. Isso também significa que a decomposição mais útil pode depender da tarefa. O próprio artigo diz que encontrar a decomposição “natural” ou útil é um problema diferente de simplesmente definir o que é uma representação disentangled. 

### **Nem todo fator pode ser separado arbitrariamente**
O artigo dá o exemplo de rotações em 3D. Poderíamos imaginar:
$$ 
Z=
[Z_x,Z_y,Z_z]
$$ 
onde cada parte representa uma rotação em um eixo.
Mas rotações 3D não são independentes dessa forma porque a ordem das rotações importa:
$$ 
R_xR_y\neq R_yR_x
$$ 
Logo, o grupo de rotações 3D não pode ser simplesmente decomposto em três grupos independentes correspondendo aos eixos $x,y,z$. Isso mostra que não basta escolher qualquer conjunto de características e exigir que elas sejam totalmente independentes. A própria estrutura do fenômeno precisa permitir essa decomposição.

## **Conclusao**

O paper define o que é uma representação disentangledmas não resolve como aprender essa representação. Em particular, ele assume que uma decomposição útil das transformações já foi escolhida. Descobrir automaticamente quais são as decomposições “naturais” do mundo continua sendo um problema separado. Os autores deixam essa questão para trabalhos futuros. 

A principal contribuição do paper é trocar a definição intuitiva:

- separar fatores geradores (que assumem uma unica dimensao)

por uma visão mais formal:

- representação é disentangled quando diferentes transformações do mundo atuam independentemente em diferentes subespaços da representação (varias dimensoes)

# **Disentangled Representation Learning** 

Fonte (2024): https://arxiv.org/abs/2211.11695

Agora que temos uma definicao mais clara e formal podemos ver como geralmente isso é implementado e avaliado e esse survey vai ajudar nisso. Ele comeca falando das duas definicoes anteriores e citando um terceiro ponto importante:

- essas duas visões normalmente assumem fatores independentes, e isso nem sempre é realista. É aí que entram as *abordagens causais, que permitem relações entre fatores*.

## **Taxonomia definida**

A taxonomia do artigo divide os metodos em 4 perguntas:

1. Qual tipo de modelo usado ?
2. Como representar os fatores ?
3. Existe supervisao ?
4. Fatores independentes ou causais ?

### **Qual tipo de modelo é usado ?**

O paper divide em 3 tipos principais

#### **VAE-Based**

- *VAE* aprende:
    $$
    x\rightarrow z\rightarrow \hat{x}.
    $$
    O encoder estima uma distribuição:
    $$
    q_\phi(z|x)
    $$
    e o decoder tenta reconstruir:
    $$
    p_\theta(x|z).
    $$
    A função objetivo possui aproximadamente dois objetivos:
    $$
    \mathcal L
    =
    \underbrace{
    E_{q_\phi(z|x)}
    [\log p_\theta(x|z)]
    }_{\text{reconstrução}}
    -
    \underbrace{
    D_{KL}(q_\phi(z|x)\|p(z))
    }_{\text{regularização}}.
    $$
    O primeiro termo quer:
    $$
    \hat x\approx x.
    $$
    O segundo força o espaço latente a se aproximar de uma distribuição simples, normalmente:
    $$
    p(z)=\mathcal N(0,I).
    $$
    E como
    $$
    I=
    \begin{bmatrix}
    1&0&\dots\\
    0&1&\dots\\
    \vdots
    \end{bmatrix},
    $$
    isso está associado à ideia de componentes não correlacionados/independentes.

- O *β-VAE* modifica a loss:
    $$
    \mathcal L_{\beta VAE}
    =
    E[\log p_\theta(x|z)]
    -
    \beta
    D_{KL}(q_\phi(z|x)\|p(z)).
    $$
    O VAE comum corresponde a:
    $$
    \beta=1.
    $$
    Se fazemos:
    $$
    \beta>1,
    $$
    aumentamos a pressão para organizar o espaço latente segundo o prior fatorado.
    O paper relata justamente o trade-off:
    $$
    \beta\uparrow
    \Rightarrow
    \text{mais disentanglement}
    $$
    mas também:
    $$
    \beta\uparrow
    \Rightarrow
    \text{reconstrução potencialmente pior}.
    $$
    Ou seja:
    $$
    \text{disentanglement}
    \leftrightarrow
    \text{preservação de informação}
    $$
    é uma tensão importante.

- *FactorVAE* vai mais diretamente para a independência entre dimensões.
    Ele usa a chamada Total Correlation:
    $$
    TC(z)
    =
    D_{KL}
    \left(
    q(z)
    \middle\|
    \prod_j q(z_j)
    \right).
    $$
    Se
    $$
    q(z)=\prod_jq(z_j),
    $$
    então as dimensões são independentes.
    Logo:
    $$
    TC(z)=0.
    $$
    O objetivo passa a incluir uma penalização desse termo.
    Então podemos pensar:
    $$
    \text{β-VAE: reforça indiretamente a fatoração}
    $$
    enquanto
    $$
    \text{FactorVAE: penaliza explicitamente dependência entre latentes}
    $$
    O survey apresenta justamente Total Correlation como medida da independência dimension-wise. 
    Isso também ajuda a entender uma coisa importante: muitos métodos clássicos praticamente tratavam
    $$
    \text{disentanglement}
    \approx
    \text{independência}.
    $$
    Depois essa equivalência começa a ser questionada.

*Logica geral:*

- Em geral VAEs aprendem uma distribuicao multivaridade intermediaria z baseado no input x (dados) e depois tentamos reconstruir x com essa distribuicao aprendida z. A maioria das VAEs assumem uma representacao dimension-wise e usam uma regularizacao que tenta forcar a distribuicao aprendida a seguir uma normal de medias 0 e matriz identidade de covariancia, ou seja, todas as dimensoes terem covariancia 0 entre si, isso vem da suposicao dos fatores serem independentes. B-VAE basicamente da maior peso para essa regularizacao e FactorVAE usa produto de probabilidades para penalizar a distribuicao, pois se a probabilidade da intersecao de todas os fatores é igual a probabilidade isoladas multiplicadas entao sao independentes. 

#### **GAN-Based**

Nos GANs temos normalmente:
$$
z\rightarrow G(z)\rightarrow x.
$$
Diferentemente do VAE, não necessariamente há naturalmente um encoder:
$$
x\rightarrow z.
$$
Um exemplo importante do survey é InfoGAN.
Ele separa a entrada do gerador em:
$$
(z,c)
$$
onde:
$$
z=\text{ruído}
$$
e
$$
c=\text{fatores interpretáveis que queremos descobrir}.
$$
Então o modelo tenta maximizar:
$$
I(c;G(z,c)),
$$
a informação mútua entre $c$ e a imagem gerada.
A ideia é:
$$
c
\rightarrow
\text{tem efeito previsível sobre }x.
$$
Assim alguns componentes de $c$ podem acabar representando propriedades como rotação, espessura, identidade etc. O survey apresenta InfoGAN como um dos primeiros métodos GAN-based de DRL.

*Logica Geral:*

A ideia geral de GANs é ter um modelo *gerador* e um modelo *discriminador*, a tarefa do gerador é dado a uma amostra z vinda de uma distribuicao N(0, I) (igual VAE), gera novos dados aproximados x', já a tarefa do discriminador é dado um conjuntos de dados X ele deve classificar os dados entre falso/aproximado (x') e real (x) e conforme sao otimizados conjuntamente eles impulsionam um ao outro a melhorar. A InfoGAN altera a GAN da seguinte forma, ao inves de apenas z, sao amostrados (z,c) e o modelo gera o x' usando ambos (z,c)... 

#### **Diffusion-Based**

Diffusion models têm um processo:
$$
x_0
\rightarrow
x_t
$$
em que progressivamente adicionamos ruído:
$$
x_t=\alpha_t x_0+\sigma_t\epsilon.
$$
Depois o modelo aprende o processo reverso.
Trabalhos mais recentes procuram encontrar direções semânticas dentro desses modelos:
$$
h' = h+\alpha\Delta h_i.
$$
Por exemplo:
$$
\Delta h_{\text{sorriso}}
$$
poderia alterar sorriso sem alterar outras características.
Então a ideia de disentanglement continua:
$$
\text{transformação semântica}
\leftrightarrow
\text{direção/subespaço específico}.
$$
O survey usa diffusion models para mostrar que DRL é uma estratégia geral, não uma propriedade exclusiva de VAEs.

### **Como representar os fatores ?**

#### **Dimension-wise**
$$
z=[z_1,z_2,z_3,\ldots]
$$
e esperamos algo como:
$$
z_1=\text{cor}
$$
$$
z_2=\text{posição}
$$
$$
z_3=\text{forma}.
$$
Cada fator ocupa basicamente uma dimensão.

#### **Vector-wise**
$$
z=
[z_{\text{conteúdo}},
z_{\text{estilo}}],
$$
mas cada componente pode ser um vetor inteiro (Nao necessáriamento contiguo):
$$
z_{\text{conteúdo}}\in\mathbb R^{128}
$$
$$
z_{\text{estilo}}\in\mathbb R^{64}.
$$

#### **Flat e Hierarchical**

Outra ideia é que os fatores talvez não estejam todos no mesmo nível.
Podemos ter:
$$
\text{animal}
$$
e abaixo:
$$
\text{cachorro}
$$
e abaixo:
$$
\text{raça}.
$$
Ou, em texto:
$$
\text{estilo}
$$
poderia ser decomposto em:
$$
\text{formalidade},
\text{tom},
\text{registro},
\dots
$$
Então, em vez de:
$$
Z=[Z_1,Z_2,Z_3]
$$
sem relação entre eles, poderíamos ter uma estrutura hierárquica.