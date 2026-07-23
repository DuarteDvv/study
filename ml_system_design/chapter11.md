# **The Human Side of Machine Learning**

## **Experiência do Usuário (UX) em sistemas de ML**

O usuário espera algo consistente sempre que usa um software, ou seja, deterministico mas isso nem sempre é possivel pois modelos de ML sao probabilisticos, nem sempre a previsoaa mais correta nem sempre é que gera a melhor experiencia para o usuário. Existem alguns problemas como:

- **Previsoes quase certas:** Modelos generativos como GPT geram respostas uteis que nem sempre sao corretas e cabe ao usuário saber distinguir isso, se ele nao sabe distinguir entao o modelo falhou. Uma solucao tipica disso é usar human-in-the-loop em que geramos mais de uma resposta e deixamos o usuário escolher a melhor.
- **Falha suave:** Existem queries que por definicao sao mais caras e demoradas fazendo o usuário esperar muito tempo, nesses casos ao inves de deixar o usuário esperando podemos usar modelos mais simples e rapidos para responder nesses casos ou colocar um cache. É um tradeoff entre velocidade e acuracia. O modelo falha mas falha suavemente.

## **Estrutura de Times**

- **Colaboracao com especialistas:** muitas vezes é importante colaborar com especialistas de areas diferentes como medicos, eles ficam responsaveis por rotulacao e deveriam também validar coisas como avaliacao e interface.

- **Organizacao de times:** podemos ter um time separado para Operacoes e um para ciencia de dados. O problema é a comunicacao pouco eficiente entre os dois times. Podemos também ter uma equipe em que o cientista de dados é responsavel por todo o processo inclusive infra. O problema é que isso tira o foco principal do cientista de dados. Idealmente queremos abstrair essa complexidade com ferramentas sem precisar ser especialista em ciencia de dados.

## **IA Responsável (Responsible AI)**

Framework para IA responsavel:

1. **Descobrir fontes de vié:** 
    - **Dados de treino:** são representativos da realidade?
    - **Rotulagem:** anotadores seguem padrões ou usam julgamento subjetivo (viés humano)?
    - **Feature engineering:** existe impacto desigual (disparate impact), quando variáveis correlacionadas com grupos protegidos (ex: CEP correlacionado com raça) causam discriminação indireta, mesmo sem usar a variável sensível diretamente?
    - **Objetivo do modelo:** está otimizando só para a maioria, prejudicando minorias?
    - **Avaliação:** está sendo feita de forma granular por subgrupo (slice-based evaluation)?

2. **Especialistas sao necessários:** Em dominios de risco depender apenas de dados é arriscado
3. **Entender trade-offs:** muitas vezes acuracia precisa ser sacrificada por privacidade, além disso, compactacao do modelo pode reduzir pouco a acuracia em geral mas e em grupos especificos?
4. **Agir cedo:** quanto mais cedo pensar nos riscos etipos mais facil é implementar
5. **Ferramentas e novidades:** Usar ferramentas consolidads como AI Fairness 360 da IBM e ficar atento a novidades