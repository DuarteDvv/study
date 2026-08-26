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

# **Algoritmos de manipulacao de sequencias**

Problema recorrente em diversos lugares, por exemplo control-f em que queremos encontrar ocorrencias de uma pradrao especifico, mas também é util em diversas outras aplicacoes como encontrar padroes em DNAs ou proteinas, compressao de textos e antivirus. 

## **String Matching**

Busca de padroes em texto, esses padroes e textos sao subconjuntos de um conjunto finito de caracteres chamado chamado alfabeto (sigma maiusculo).

- Texto: Representado como T[1...n]
- Padrao: Representado como P[1...m] e se queremos representar kth prefixo do padrão (k primeiros elementos P[1...k]) usamos $P_k$
- Amboa indexados em 1

O problema entao é encontrar um indice s em que 0 <= s <= n - m (se isso nao for verdade o padrao P nao cabe no final do texto T). Tal que T[s+1...s+m] = P[1...n], ou seja, s é o indice anterior ao padrao totalmente igual encontrado no texto. E como indices vao de 0 a n-m entao podemor usar -1 como flag para nao existe padrao. Outra forma de definir é encontrar s tal que P ⊐ $T_{s + m}$, ou seja, s tal que o padrão é sufixo do prefixo de T que vai s+m.

- Tamanho de uma string w é |w|
- string vazia é denotada como lambda λ
- Concatenacao w de duas strings x e y é xy

x é chamado de *prefixo* de w e pode ter tamanho menor ou igual a w (ou seja, se x = w então w é prefixo dele mesmo). O simbolo de prefixo é x ⊏ w. y é chamado de *sufixo* de w e segue a mesma logica que o prefixo mas seu simbolo é y ⊐ w. O vazio é prefixo e sufixo de qualquer string. 

- **Transitividade:** Se *a* é prefixo de *b* (a ⊏ b) e *b* é prefixo de *c* (b ⊏ c) então *a* é prefixo de *c* (a ⊏ c). Da mesma forma se *a* é sufixo de *b* (a ⊐ b) e *b* é sufixo de *c* (b ⊐ c), então *a* é sufixo de *c* (a ⊐ c).


**Lema 1 (Sobreposição):** Se duas strings x1 e x2 são simultaneamente prefixos ou sufixos de um terceira string y, existem 3 casos possiveis:
- x1 e x2 tem mesmo tamanho e são iguais
- x1 é maior que x2, portanto x2 é sufixo ou prefixo de x1
- x2 é maior que x1, portanto x1 é sufico ou prefixo de x2

### **Algortimo ingênuo**

```py
def naive(P, T):

    n = len(T)
    m = len(P)

    matchs = []

    for i in range(n - m):
        if T[i:i + m] = P:
            matchs.append(i)
```

Aqui passamos uma janela deslizante de tamanho m no texto T inteiro e comparamos cada uma dessas janelas com o padrão P de tamanho m. Todas as janelas deslizantes tem complexidade O(n) e para cada janela temos uma comparação de tamanho O(m). 

- *Complexidade de tempo:* O(n * m) para padrões de tamanho m > n/2 podemos dizer que a complexidade é O(n^2)
- *Complexidade de espaço:* O(1)

### **Algoritmo de automato finito para String Matching**


### **KMP**

### **Boyer-Moore-Horspool** 

### **Shift-And**