# **Representation Learning** 

Fonte(2013): https://arxiv.org/abs/1206.5538

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

Fonte(2018): https://arxiv.org/abs/1812.02230

Esse paper tenta criar uma definição mais formal e matemática do conceito de Disentangled Representation. As definições anteriores eram simplesmente:

- representação disentangled = representação que separa os fatores geradores dos dados

Ai entre algumas questões como o que exatamente é um fator gerador ? cada fator ocupa quantas dimensões ? quais fatores deveriam ser separados dado que podem existir infinitos ? Devido a essa imprecisão o autor tenta troca-la por *"Transformação do mundo"*.

##

