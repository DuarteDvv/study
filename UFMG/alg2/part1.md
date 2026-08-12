# **Automatos**

Em geral são estruturas matematicas parecidas com uma maquina de estados mas sem outputs nos estados

## **Deterministico** 

È um conjunto finito de estados, alfabeto de ações e funções de transição que levam de um estado x1 através da ação y ate um novo estado x2. Além disso, dentre o conjuntos de estados temos o estado inicial e estados finais (aceitação).

## **Não Deterministico** 

É a mesma estrutura, mas a função de transição pode levar um estado x1, através da ação y, para mais de um estado possível ao mesmo tempo (ou pra nenhum), ou seja, δ: Q × Σ → P(Q), retorna um conjunto de estados em vez de um único. Também pode ter transições ε, que mudam de estado sem consumir nenhum símbolo da entrada. Todo AFN pode ser convertido em um AFD.

## **Problema**

Automatos só tem estados para lembrar de informações e o numero de estados é fixo. Logo, em problemas como encontrar {0^n 1^n} para qualquer n >= 0, ou seja, procurar um estado em que o numero de 1 e 0 são iguais para qualquer n, o automato não conseguiria resolver pois esse problema exigiria armazenar quantos 0s forem vistos e comparar depois com os 1s. 


# **Maquinas de Turin**

Também são estruturas matemáticas parecidas com máquina de estados, mas agora com uma fita infinita que pode ser lida e escrita (em vez de só ler a entrada uma vez, da esquerda pra direita).

## **Determinística**

É um conjunto finito de estados, um alfabeto de entrada, um alfabeto de fita (que inclui o alfabeto de entrada mais um símbolo de branco), e uma função de transição que leva um estado x1, ao ler um símbolo da fita, para um novo estado x2, escrevendo um novo símbolo no lugar e movendo o cabeçote uma casa pra esquerda ou direita. Ou seja, δ: Q × Γ → Q × Γ × {E, D}.

Além disso, dentre o conjunto de estados temos o estado inicial e dois estados de parada especiais: aceitação e rejeição (em vez de só "estados finais" como no autômato, aqui a rejeição também é um estado explícito, porque a máquina pode não parar nunca como no problema da parada).

## **Não determinística**

Mesma estrutura, mas a função de transição pode levar um estado x1, ao ler um símbolo, para mais de um par (estado, símbolo escrito, direção) possível ao mesmo tempo, δ: Q × Γ → P(Q × Γ × {E, D}).


### **Algoritmo**

Algortimo pode ser definido como uma sequência finita e bem definida de passos que resolve um problema ou também como uma maquina de turing que para em toda entrada.

### **Computabilidade**

Estuda o que pode ser computado, sem se importar com quanto tempo ou memória isso custa, só importa se existe algum algoritmo, nesse sentido formal, que resolve o problema.

### **Tratabilidade**

Aqui a pergunta muda: entre os problemas computáveis (decidíveis), quais têm um algoritmo eficiente, que roda em tempo razoável mesmo pra entradas grandes?

- **P:** problemas decidíveis por uma MT determinística em tempo polinomial (O(nᵏ) pra algum k), considerados "tratáveis" na prática.
- **NP:** problemas cuja solução, uma vez proposta, pode ser verificada em tempo polinomial por uma MT determinística, equivalente a dizer que uma MT não determinística resolve o problema em tempo polinomial.

Todo problema em P também está em NP (se dá pra resolver rápido, dá pra verificar rápido também, é só resolver de novo). A pergunta em aberto mais famosa da computação é se vale a volta: P = NP? A maioria dos pesquisadores acredita que não, mas ninguém conseguiu provar.