## Dados de treino

Esse capitulo era focar principalmente no dado usado para treinar modelos... como tratar e problemas mais comuns.

### Amostragem (Sampling)

Amostragem é o primeiro passo de um projeto de ML pois é o momento em que coletamos os dados para treino e validação... para isso precisamos pegar uma amostra representativa da população de maneira menos enviesada possivel. Existem dois grandes grupos de metodos de amostragem:

#### Amostragem Não-probabilistica:

Aqui não utilizamos nenhum tipo de probabilidade para coletar as amostras

- Amostragem por conveniencia (Convenience Sampling): Amostramos apenas quem esta a nosso alcance e disponivel... normalmente locais proximos ou comunidades conhecidas.

- Amostragem Snowball: Usamos amostras coletadas para coletar novas amostras... por exemplo podemos pegar pessoas amostradas e pedir para elas escolherem pessoas que conhecem e assim por diante. Outro exemplos é minerar perfis na web seguindo amigos de amigos.

- Amostragem por especialista: Um especialista na área de pesquisa decide se escolhemos ou não amostras com base na experiencia e conhecimento técnico dele... muito util em cenários clinicos raros.

- Amostragem Cota (quote): Escolhemos um cota de amostras por categoria definida independentemente da distribuição real. Por exemplos queremos 100 pessoas por faixa de idade.

Todas as amostragens não probabilisticas são enviesadas (viés de amostragem) e pode ser um problema utilizar esses dados para treinar modelos... porém muitas vezes o ponta pé inicial mais facil é utilizando esse tipo de amostragem e em sequencia quando já existir um modelo baseline partir para métodos probabilisticos.

#### Amostragem Probabilistica:

Aqui usamos conceitos de probabilidade para amostrar nossa população:

- Amostragem Aleatória Simples (Random Sampling): Aqui amostramos da população (ou amostra) em que cada amostra tem a mesma probabilidade... isso é uma suposição e que caso seja falsa terá uma amostra viciada. Um problema dessa amostragem é o fato de que se tivermos grupos muito raros dentro dessa população (1%), se amostrarmos 10% aleatoriamente a chance desse grupo não aparecer ou aparecer menos que deveria é muito grande.

- Amostragem estratificada: Aqui resolvemos o problema anterior primeiro dividindo a população em grupos chamados estratos e depois amostrando cada estrato igualmente. Se temos grupo A e B, pegamos 1% do grupo A e 1% do grupo B... um problema desse método é que nem sempre conseguimos dividir em grupos e nem sempre uma amostra se encaixa em apenas um dos grupos. E nem sempre a amostra coletada vai manter a distribuição da populaçao... pode haver casos que o grupo é tão raro que uma coleta dele gera muito poucas amostras.

- Amostragem com pesos: Esse metodo é ideal em cenários em que sabemos de antemação a importancia dos valores amostrados usando algum conhecimento prévio ou de experts. Aqui cada amostra terá um peso que é equivalente a probabilidade dele ser selecionado, ou seja, se A tem peso 0.7 e B peso 0.3 então A tem 70% de chance de ser selecionado e B 30%. Isso é util para corrigir vieses amostrais como por exemplo ter coletado de mais homens que mulheres e isso afetar o resultado... ai podemos corrigir através de pesos maiores para as mulheres. Também é util para dar peso em algoritmos de machine learning em que alguns exemplos afetam mais a loss que outros.

- Amostragem de reservoir: Esse já é ideal para casos em que temos streaming data, ou seja, dados em produção que vão chegando um atrás do outro de forma continua... especialmente util para amostrar para testes A/B e entre outros. Se coletarmos todos os dados que queremos de uma vez apenas em um intervalo de tempo fixo como entre 6h e 12h estaremos enviesados pelo horário e isso não representaria a população de clientes inteira... além disso, não sabemos o tamanho inteiro dos dados que vão vir ainda para dar a mesma probabilidade de coleta para cada amostra e nem podemos manter tudo em memória para calcular isso... A solução de reservoir é mantermos um vetor de tamanho **k** em que **k** é o tamanho da amostra que voce quer e um contador **n**... sempre que chegar um novo caso/amostra **i** voce gera um numero aleatório **j** entre **0 e n**, se **j** for menor que k nos substituimos o j-esimo elemento pelo **i**, senão não fazemos nada. Dessa forma, todo novo elemento que chegar terá exatamente **k/n** de probabilidade de estar entre os **k** primeiros elementos e todo elemento que já entre os k vai ter a mesma probabilidade de ser substituido... ou seja, todo mundo com a mesma probabilidade de estar na amostra.

![alt text](images/reservoir.png)

- Amostragem por importância: é uma técnica estatística utilizada para estimar propriedades de uma distribuição alvo quando a amostragem direta é inviável. Em muitos cenários de Machine Learning e Simulação, sortear amostras de uma população com distribuição $P(x)$ é impossível, muito difícil ou computacionalmente caro. Nesses casos, utilizamos uma distribuição auxiliar $Q(x)$, que é fácil de ser amostrada.

    Para corrigir o fato de estarmos usando a distribuição "errada", aplicamos um **peso de correção** (razão de verossimilhança) a cada amostra:

    $$w(x) = \frac{P(x)}{Q(x)}$$

    Dinâmica dos Pesos:
    * **Subestimados:** Se $Q(x) < P(x)$, damos um **peso grande** para compensar a baixa frequência de sorteio em $Q$.
    * **Sobreestimados:** Se $Q(x) > P(x)$, damos um **peso pequeno** para corrigir o excesso de amostras vindas de $Q$.

    Prova de Equivalência: esperança de $x$ em $P(x)$ é igual à esperança do valor ponderado em $Q(x)$:

    $$E_{P}[x] = \sum_{x} x P(x)$$

    Multiplicando por $\frac{Q(x)}{Q(x)}$:

    $$E_{P}[x] = \sum_{x} x \frac{P(x)}{Q(x)} Q(x)$$

    O que nos leva à identidade fundamental:

    $$E_{P}[x] = E_{Q} \left[ x \frac{P(x)}{Q(x)} \right]$$

    Requisito Fundamental: Para que a amostragem por importância seja válida, a distribuição auxiliar $Q(x)$ deve cobrir todo o espaço onde $P(x)$ existe. Matematicamente:
    $$Q(x) > 0 \text{ sempre que } P(x) > 0$$

    Uma aplicação é em aprendizado por reforço em que temos uma distribuição inicial de probabilidades de Q(a/s) de ações dado o um estado... para gerar a primeira distribuição normalmente são movimentos aleatórios... para não ter que repetir todos esse movimentos (pois é caro) estimamos a distribuição nova P(a/s) apartir da antiga ajustada por esses pesos P(a/s)/Q(a/s)...  e avaliamos elas em relação as recompensas.


### Labeling



