### Conceitos

Genes: são os parametros que queremos otimizar... por exemplo se queremos otimizar um vetor nossos genes são os elementos do vetor

### População Inicial

Lista/conjunto de candidatos iniciais também chamados de pais (parents).

### Elitismo 

Ao invés de substituir cegamente os pais dessa geração pelos filhos e correr o risco deles serem piores e andarmos para trás. Podemos manter os k melhores pais e trocar os k piores filhos por eles.

### Mutação

Fazemos alterações em x% dos filhos de forma aleatória ou quase aleatória para tentar fugir de minimos locais.

### Crossing-over 

Escolhemos um numero k (ou porcentagem) da população atual que ira reproduzir, ou seja, um numero de pais que vao reproduzir... pegamos par a par e cada casal gera 2 filhos que tentam manter o melhor dos dois pais. (processo de reprodução)

### Seleção 

Aqui decidimos como pegar os k pais para reproduzir... as 2 formas mais comuns são: fazemos k torneios de t participantes em que escolhemos t aleatorios na população e escolhemos o melhor deles ate escolher o k pais. O outro metodo é a roleta em que escolhemos os k melhores pais ordenados pelo fitness e usamos eles para reproduzir.