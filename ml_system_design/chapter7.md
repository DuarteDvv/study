# **Model Deployment and Prediction Service**

Nesse capitulo será discutido o deploy de modelos e servicos de predicao. Fazer deploy é uma tarefa extremamente simples se a parte dificil for ignorada uma vez que é só criar um endpoint POST chamado prediction, criar um container e colocar ele na AWS. Entretanto outros pontos importantes como latencia e escalabilidade tem que ser discutidos. 

## **Mitos sobre ML deployment**

- **Mito 1 - Só acontece deploy de 1-2 modelos ao mesmo tempo:** uma empresa nao terá apenas um modelo em producao, pois existem diversas tarefas correlatas que irao usar features diferentes e consequentemente modelos diferentes serao treinados. Além disso, nao necessáriamentente para cada tarefa vamos ter apenas um modelo pois um modelo apenas pode nao generalizar de forma suficiente para todos os clientes daquela tarefa. A ideia aqui é pensar na infraestrutura nao apenas para um modelo mas varios simultaneamente.

- **Mito 2 - Se nao fizermos nada a performace se mantem:** Modelos tendem a performar bem logo depois do treino e devido ao data shift de producao o desempenho vai caindo com tempo.

- **Mito 3 - Voce nao precisa atualizar seu modelo tanto:** Atualizacao de modelos tem que acontecer a menor frequencia permitida e que faz sentido para o problema/empresa.

- **Mito 4 - ML Engineers nao precisam pensar em escala:** Se a empresa tem pelo menos 100 funcionários é muito provavel que ela também tenha usuário suficientes para a escala do sistema se tornar um problema e por isso é necessário pensar sobre isso tbm.

## **Batch Prediction Versus Online Prediction**

Existem duas formas principais de definir como gerar e servir predicoes para usuários e decidir entre elas muda totalmente como a arquitetura tem que ser construida.

- **Batch Prediction:** Quando as predicoes sao geradas periodicamente ou apartir de algum gatilho, sao armazenadas em um banco e recuperadas quando necessárias (requisicao). Também é conhecido como predicao assincrona pois a predicao é calculada de forma assincrona com a requisicao HTTP (REST). 
    - Ex: Um bom exemplo é o calculo de recomendacoes de filmes para cada usuário da netflix que acontece de hora em hora e as ultimas predicoes sao apenas buscadas quando o usuário faz login na plataforma.

- **Online Prediction:** Quando as predicoes sao geradas e retornadas assim que a requisicao pela predicao chega. Sao conhecidas também como predicao sob demanda ou predicao sincrona pois é sincrona com a requisicao (Caso o transporte usado seja HTTPS). As pessoas acham que Online é menos eficiente porque não dá para processar várias requisições juntas (vetorização), mas o autor diz que dá sim para acumular requisições online em pequenos "mini-batches" para otimizar o hardware. Além disso, se apenas 2% dos usuários entram no app por dia (como o exemplo do Grubhub), fazer Batch Prediction para 100% dos usuários joga 98% do processamento no lixo. Online economiza porque só calcula para quem aparece. 
    - Ex: Um bom exemplo disso é pesquisar a traducao de uma frase no google em que ele imediatamente vai usar um modelo de linguagem para traduzir 

**Terminologia:** A terminologia pode confundir pois um predicao online pode ser feita em batch, ou seja, acumular predicoes de varios usuários para calcular juntas e aumentar o througput (e uso do hardware) com custo em latencia. E predicoes batch podem ser feitas a uma unica instancia também se quiser. Devido a isso costumam chamar de predicao assincrona e sincrona porém se a predicao online usar um sistema de streaming/mensagens para requisicoes agora a predicao online nao é mais sincrona e sim assincrona mas muito rapido.

Em geral os tipos de predicoes podem ser separadas em 3 de acordo com o tipo de features usadas batch features (precomputadas) e streaming features (tempo real):

- Batch prediction usa batch features precomputadas

![alt text](images/batch_pred.png)

- Online predictions que usa apenas batch features precomputadas (comum com embeddings) e baixa latencia

![alt text](images/online_pred.png)

- Online predictions que usa batch features precomputadas e streaming features. Também conhecido como **streaming prediction**.

    - **Online Features:** Qualquer feature usada na predição online (inclusive as que foram pré-calculadas em batch e guardadas na memória, como embeddings de itens).

    - **Streaming Features:** Exclusivamente as features computadas em tempo real a partir de dados em fluxo (ex: "quantos pedidos o restaurante recebeu nos últimos 10 minutos").

![alt text](images/stream_pred.png)

Entretando batch e online **nao precisam ser mutuamente exclusivos** pois podemos precomputar predicoes para queries populares e gerar online para menos populares.

- Ex: UberEats usa batch prediction para recomendações de restaurantes (são muitos restaurantes, batch é mais viável) mas usam online prediction para recomendações de itens de comida assim que você clica em um restaurante.

| | Predição em Batch (assíncrona) | Predição online (síncrona) |
|---|---|---|
| **Frequência** | Periódica, como a cada quatro horas | Assim que as solicitações chegam |
| **Útil para** | Processar dados acumulados quando você não precisa de resultados imediatos (como sistemas de recomendação) | Quando as predições são necessárias assim que uma amostra de dados é gerada (como detecção de fraude) |
| **Otimizado para** | Alta vazão (High throughput) | Baixa latência |

## **From Batch Prediction to Online Prediction**

Online prediction é a forma mais natural das coisas acontecerem, uma vez que, segue o padrao comum de sob demanda (pedir, fazer e receber) entretanto os problemas comecam a aparecer quando a **inferencia do modelo demora muito** e isso acaba tornando a latencia de resposta do cliente muito alta. A predicao em batch funciona bem nesses cenários em que calculamos o resultado de tempo em tempo pois nao precisamos dele imediatamente e o cliente apenas consulta os resultados mais recentes... o problema é que **nem sempre conseguimos precalcular os resultados** pois precisamos de um input especifico do usuário (traducao de frases) e esses resultados podem **nao ser suficientemente recentes** para evitar uma catastrofe como é comum em deteccao de fraudes que precisa de predicoes e features atualizadas (streaming).

Para lidar com a latencia de predicoes online ou streaming 2 componentes sao necessários:

- Um **pipeline em tempo real (ou quase real)** que consiga consumir dados em fluxo, extrair streaming features, buscar online features e entregar ao modelo.
- Um **modelo** que consiga fazer inferencia rapidamente

## **Unificando Batch Pipeline e Streaming Pipeline**



## **Model Compression**