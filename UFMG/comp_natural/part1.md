# **Computação Natural**

Computação Natural -> Algortimos, Simulações de ambientes naturais e biologia aplicada na computação. 
- Trabalha com modelos que são abstrações aproximadas do mundo real.

**Heuristicas x Meta heuristicas x hyper heuristicas** 

- *Heuristica* é uma estratégia especifica para resolver um problema que usa conhecimento do dominio e não garante o a solução ótima.

- *Meta Heuristica (Algoritmos geneticos estão aqui)* estratégias genéricas que orientam como explorar o espaço independente do dominio (framework de epxloração) -> Meta-heurística normalmente explora o espaço de soluções;

- *Hiper Heuristica* automatiza a escolha, combinação ou criação de heurísticas mais simples para resolver problemas complexos de otimização -> Hiper-heurística normalmente explora o espaço de heurísticas.

## **Conceitos**

- **Coletividade e agentes**: Maioria dos métodos é composto por um conjunto (coletividade) de agentes que interagem entre si e com o ambiente.
- **Paralelismo e distribuição:** Agentes, populações trabalham em paralelo
- **Interação:** Os agentes interagem entre si em 2 tipos:
    - Conexão direta entre eles (como arestas)
    - Estigmergia que é uma relação indireta como alterações no ambiente compartilhado
- **Adaptação:** Capacidade do sistema se adaptar ao ambiente/espaço
    - Aprendizado: Através de experiencia e erros cometidos
    - Evolução: Através de reproduções e mutações dos individuos 
- **Feedback:** Um estimulo no sistema tem capacidade de aumentar ou reduzir o estimulo original
- **Auto-organização:** Capacidade de um sistema produzir estruturas ou padrões organizados a partir de interações locais, sem a necessidade de um controlador central.
- **Complexidade**: Um sistema complexo possui muitos componentes ou interações cujo comportamento coletivo não pode ser facilmente explicado apenas analisando cada componente isoladamente.
- **Emergencia:** Surgimento de propriedades ou comportamentos no nível global do sistema que resultam das interações entre componentes locais. (Uma formiga não tem comportamente de achar algo mas um conjunto de formigas tem)

## **Computação Evolucionária**

### **Problemas de otimização**

- *Espaço de busca S:* São todas as possiveis soluções que o método pode retornar (por exemplo em uma regressão são todos as infinitas combinações de pesos). Esse espaço costumar se gigante ou infinito sendo necessário formas inteligentes de explorar.
- *Função Objetivo f:* mede o quão boa ou ruim uma solução candidata é
- *Região de restrições/factivel R:* Uma subregião contida em S que limita as soluções validas 
- *Representação da solução:* O jeito que uma solução é representada muda completamente seu espaço de busca, se temos um vetor binário o espaço terá combinações diferentes que um vetor continuo.

### **Paisagem de aptidão/fitness** 

È uma visualização do espaço de busca em que conseguimos ver a vizinhança e suas respectivas qualidades em relação a função objetivo. Essa paisagem pode deixar evidente diversos otimos locais e globais e certas caracteristicas podem tornar ainda mais dificil explorar ele:

- Rugosidade do espaço: Muitos maximos locais que dificultam encontrar o maximo global
- Plato no espaço: Região com fitness igual para varias soluções que dificulta explorar vizinhança
- Engano: Um otimo local na direção oposta do global
- Epistasia: Varias variaveis tem correlação entre si e otimizar elas separadas não é possivel

### **Algoritmos Evolucionários**

Algortimos inspirados na evolução biológica e dentro desse conjunto existem existem familias como Algoritmos Genéticos, Programação Genética e PG com gramaticas





