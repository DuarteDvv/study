# Churn Prediction e MTL faz sentido ? 

## Oportunidades 

- **Debalanceamento:** O evento de churn é naturalmente um evento raro (ainda bem) e portanto pode sofrer de desbalanceamento de classes. Treinar varios modelos separados pode fazer com que o volume de dados para cada modelo reduza bastante, possivelmente prejudicando performace.

    - MTL poderia ajudar com clientes com pouco ou nenhum dado pois aprenderia padrões comuns entre os tipos de churn e se especializaria em cabeças especificas dos clusteres que definirmos (distribuidora, categoria de produto e etc).

    - Poderiamos treinar outras tarefas junto como por exemplo previsão de demanda (basicamente o sistema predict deles só que usando ML).


## Riscos

- **Complexidade e Custo:** O SOTA desses metodos atualmente são arquiteturas deep learning bem flexiveis e interessantes.Porém, será que:
    - vale a pena para o nosso problema em questão custo de manter e diferença de resultado ? 
    - temos dados suficientes somando todas as ditribuidoras ? 
    - qual custo para adicionar novas tarefas ou distribuidoras (retreinamento ou finetunig) ? 


## Pesquisa futura ?

- Podemos propor uma arquitetura para o nosso problema ?
- Propor uma modelagem especifica para churn ?
- Propor uma loss que leve em consideração R$