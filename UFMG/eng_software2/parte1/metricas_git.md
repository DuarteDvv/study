## Métricas do Git

### Metricas de Evolução

Medem a evolução do repositório em relação ao tempo... métricas classicas como numero de linhas de codigo, testes, numero de testes, frequencia de commits e arquivos adicionados

### Acoplamento temporal

Diferente do acoplamento normal em que temos dependencias explicitas por meio de composição ou chamada de metodos no temporal a dependencia é implicita e é indentificada através de commits em um intervalo de tempo. Arquivos que são sempre modificados juntos nos commits tem um acoplamento temporal alto. (ruim pois se mexe em uma tem que mexer em outras)

### Autoria de codigo

Conseguimos ver quem fez o que em cada arquivo de codigo... numero de linhas adicionada por cada um e etc. Isso pode ser util para descobrir padrões, alocar tarefas de acordo com eficiencia e descobrir quem contribuiu mais.

### Truck Factor 

È uma métrica que mede a dependencia de um projeto a seus contribuidores. Se um projeto é muito dependente de poucos contribuidores... se um caminhão mata esses poucos, o projeto esta em risco. Baixo truck factor = muita dependencia a poucos membros.
