## Codigo limpo (Clean code)

Como escrever codigo com qualidade sendo direto e simples

### Legibilidade de codigo

- Nomes que relevam intencao: usar nomes que mostram claramente a intencao de um metodo ou atributo... evitar nomes simples demais como i,j,k. -> intencao
- Distincao entre nomes: nao colocar a1 e a2 e sim fonte e distino como nome de parametros  -> distincao

Em geral ter cuidado com nomes de classes, metodos, atributos e variaveis. -> buscaveis (i ?) e pronunciaveis (akkfjrf ?)
Comentários geralmente sao uma forma de compensar a pouca legibilidade do codigo em texto... talvez um comentário pode ser um sinal que alguns nomes nao sao muito intuitivos.

### Codigo removido através de comentários

Consiste em comentar o codigo antigo sem remover completamente do codigo.

Escreva três desvantagens de utilizar commented-out code?

- Codigo comentado pode distrair o programador, nao responde nada e pode fazer o programador perder muito tempo tentando entender ele
- Codigo comentado é desnecessário pois temos ferramentas de versionamento como git
- Codigo comentado pode ficar antigo, defasado e criar confusao

### Formatacao de codigo

- Formatacao vertical: formatar um arquivo igual um jornal ... nome do arquivo explicativo (ajuda a buscar) -> comeco do arquivo com funcoes mais simples e alto nivel (nada de logica complexa) -> final do arquivo funcoes mais complexas com maior logica de negocio.

- Separacao de conceitos: coloque espacos entre metodos e conceitos diferentes -> KKKKKKKKKKKKKKKKKKKK

- Funcoes dependentes: Funcoes que tem alguma relacao (uma chama a outra) devem ficar proximas

### Efeitos colaterais

Mesma coisa da responsabilidade unica... um metodo que retorna booleano nao deve por algum motivo inicializar um servidor ...

### Dont Repeat Yourself

Nao gere repeticao de codigo!!
