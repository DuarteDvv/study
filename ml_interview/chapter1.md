## Framework para ML system design

Esclarecer requisitos ---> Modelar o problema em uma tarefa de ML ---> Preparação dos dados ---> Desenvolvimento do modelo ---> 
Avaliação ---> Desenvolvimento e Deployment ---> Monitoramento e Infraestrutura 


#### Esclarecer requisitos

Aqui definimos os requisitos do sistemas em:

- objetivo de negócio: Como a empresa espera que o sistema afete o negocio da empresa ? Afetar as vendas ? O consumo diário ? Lucro ? 
- Features que o sistema precisa suportar: Que tipo de coisas o sistema que estamos construindo precisa suportar ? Likes ? Comentários ?
- Fontes de dados: Quais as fontes de dados que vamos usar ? quantidade ? estruturada ? rotulada ? 
- Restrições: Alguma restrição de latência ? de hardware ? Cloud ou local ? Predições tem que ser rapidas ? precisão ou tempo ?
- Escalabilidade: Quantos usuários vamos ter ? items ? 

#### Modelar o problema em uma tarefa de ML

Aqui definimos o objetivo de ML (objetivo que o sistema pode maximizar) através do objetivo de negócio (business), ou seja, a métrica que queremos maximizar na empresa através de machine learning (como taxa de cliques por exemplo)... depois definimos a entrada e saida do sistema (vai entrar uma imagem e sair uma probabilidade ?)... depois com base na entrada e saida escolhemos a categoria de problema que estamos lidando (Classificação Multiclasse ? Regressão ?).

#### Preparação dos dados

Agora precisamos preparar e garantir inputs de qualidade para nosso sistema:

- Engenharia de dados: 

- Engenharia de Features: