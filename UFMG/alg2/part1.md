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

# **Algoritmos de casamento de padrões**

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

    matches = []

    for i in range(n - m):
        if T[i:i + m] = P:
            matches.append(i)

    return matches
```

Aqui passamos uma janela deslizante de tamanho m no texto T inteiro e comparamos cada uma dessas janelas com o padrão P de tamanho m. Todas as janelas deslizantes tem complexidade O(n) e para cada janela temos uma comparação de tamanho O(m). 

- *Complexidade de tempo:* O(n * m) para padrões de tamanho m > n/2 podemos dizer que a complexidade é O(n^2)
- *Complexidade de espaço:* O(1)

### **Algoritmo de automato finito para String Matching**

Em vez de comparar caracteres repetidamente como no algoritmo ingênuo, você constrói um autômato finito determinístico (AFD) a partir do padrão P. Depois, você *percorre o texto T uma única vez, alimentando cada caractere no autômato*. Cada vez que o autômato atinge o estado de aceitação (estado final, igual ao comprimento do padrão), significa que uma ocorrência do padrão termina naquela posição do texto. Isso dá complexidade O(n) para a busca no texto (depois do pré-processamento), sem nunca precisar retroceder no texto. 

- **Função sufixo:** σ(w) = max {k | Pk ⊐ w} dado uma string qualquer w (que pode ser P[1..q] + um novo char *a*, uma string que nem faz parte do padrão original), qual é o maior prefixo do padrão P que é sufixo de w. 

- **Componentes:**

    - **Estados:** 0, 1, 2, ..., m onde m = |P|. O estado representa "quantos caracteres do padrão já casei até agora" (o maior prefixo de P que é sufixo do que já li).
    - **Estado inicial:** 0
    - **Estado de aceitação:** m (padrão completo casado)
    - **Função de transição δ(estado, caractere novo):** diz para qual estado ir dado o estado atual e o próximo caractere do alfabeto lido do texto.

Para conseguir manter a complexidade linear precisamos ja ter calculado todas as funções de transição, ou seja, um dicionário que mapeia estado atual e novo caractere para um proximo estado. Calcular essa tabela é chamado de pre processamento e ele precisa ser feito para cada padrão novo mas não necessáriamente para cada texto novo dado que os textos podem seguir o mesmo alfabeto. Para calcular essa tabela usamos o algortimo:

```py
def compute_transition_function(P,Alfabeto):
    m = len(P)
    TF = {} # {estado, novo char}

    # para cada par (estado atual q, novo caractere), aplica a função sufixo σ na string (P[:q] + novo_char), para achar o novo estado (maior prefixo de P que bate como sufixo dessa string)

    for q in range(m + 1): # para cada estado possivel no padrão
        for novo_char in Alfabeto: # para qualquer novo 
            # agora vamos testar varios prefixos do padrão de tamanho k e comparar com estado atual + novo char (tamanho q+1) para encontrar o maior k que é sufixo do estado atual + novo char

            k = min(m,q+1) # tamanho do padrão não pode ser maior que o proprio padrão nem maior dq ja foi casado ate agora 
            while k > 0 and P[:k] != (P[:q] + novo_char)[-k:]: # busca o tamanho do maior prefixo de P que é sufixo do (já casado + novo char) já lido
                k -= 1
            
            TF[(q, novo_char)] = k 

    return TF 
```

Uma vez que a tabela é computada só precisamos passar pelo texto usando essa tabela:

```py
def find(P, T, alfabeto):
    delta = compute_transition_function(P, alfabeto)
    matches = []
    n, m = len(T), len(P)
    q = 0
    for i in range(n): # le os caracteres gradualmente mudando o estado (tamanho ja casado do padrão no texto) ate que ele seja igual ao tamanho do padrão
        q = delta[(q, T[i])]
        if q == m:
            matches.append(i - m + 1)  
    return matches
```
#### **Complexidades**

- **Pré-processamento (construir δ):** O(m³ · |Σ|) pois para cada estado m, exploramos |Σ| novos caracteres, comparando no maximo m prefixos (k) com o estado atual + novo caracter. Temos então m^2 * |Σ|, porém a comparação é O(m) então vira m^3 * |Σ|.

- **Busca no texto:** O(n), sempre, sem exceção, cada caractere do texto é lido exatamente uma vez.

- **Total:** O(m^3 * |Σ|)

#### **Intuição**

Primeiro passo é computar a tabela de transições que mapeia o estado atual + novo char lido para um novo estado (dado que estado é quantos caracteres do padrao ja casamos). A construcao dessa tabela tem complexidade m^3 * alfabeto e após ser construida passamos linearmente no texto T e atualizamos o estado atual com os novos caracteres lidos do texto, se chegarmos ao estado final (estado = tamanho do padrao) entao ja casamos todo o padrao e podemos salvar uma aparicao.

### **KMP**

A ideia do KMP é obter um comportamento semelhante ao autômato de casamento, mas sem precisar armazenar uma transição para cada caractere do alfabeto \(\Sigma\). Para isso, ele pré-computa apenas informações sobre a estrutura do próprio padrão usando a **função prefixo**.

A função prefixo é definida como:

$$
\pi[q] = \max\{k < q+1 \mid P[:k] \text{ é sufixo de } P[:q+1]\}
$$

Ou seja, dado um índice `q` do padrão, `pi[q]` retorna o **tamanho `k` do maior prefixo próprio de `P[:q+1]` que também é seu sufixo**.

Por exemplo:

```text id="wmpd02"
P  = a b c a
     0 1 2 3

pi = 0 0 0 1
```

Calculamos essa tabela em um pré-processamento:

```py
def compute_prefix_function(P):
    m = len(P)
    pi = [0] * m
    k = 0

    for q in range(1, m): # q = quantos do padrão ja foram casados
        while k > 0 and P[k] != P[q]:
            last_match_idx = k -1
            k = pi[last_match_idx]

        if P[k] == P[q]:
            k += 1

        pi[q] = k

    return pi
```

Aqui, `k` representa o tamanho do prefixo que atualmente também é sufixo. Se `P[k] != P[q]`, não precisamos voltar imediatamente para zero: usamos `pi[k - 1]` para encontrar o próximo maior prefixo que ainda pode ser reaproveitado.

Depois percorremos o texto:

```py
def kmp_search(P, T):
    n, m = len(T), len(P)
    pi = compute_prefix_function(P)

    matches = []
    matched = 0

    for i in range(n):
        while matched > 0 and P[matched] != T[i]:
            matched = pi[matched - 1]

        if P[matched] == T[i]:
            matched += 1

        if matched == m:
            matches.append(i - m + 1)
            matched = pi[matched - 1]

    return matches
```

`matched` representa a **quantidade de caracteres do padrão que já casaram**. Consequentemente, `P[matched]` é o próximo caractere do padrão que queremos comparar. Quando ocorre um mismatch, em vez de voltar no texto ou começar o padrão do zero, fazemos:

```py
matched = pi[matched - 1]
```

Isso funciona porque `pi[matched - 1]` nos diz quantos caracteres no final da parte que já casou também formam um prefixo do padrão. Portanto, essa parte pode ser reaproveitada. Se ainda houver mismatch, repetimos o processo até encontrar um prefixo compatível ou chegar a zero. O ponto fundamental é que **o índice `i` do texto nunca retrocede**; apenas `matched` recua dentro do padrão.

Quando:

```py"
matched == m
```

o padrão inteiro casou. Como `i` aponta para o último caractere da ocorrência, seu início é:

$$
i-m+1
$$

Depois do match fazemos novamente:

```py
matched = pi[matched - 1]
```

em vez de zerar `matched`, pois o sufixo da ocorrência encontrada pode já ser o prefixo de uma próxima ocorrência. Isso também permite encontrar matches sobrepostos.

#### **Complexidade**

* **Pré-processamento:** \(O(m)\)
* **Busca:** \(O(n)\)
* **Espaço adicional:** \(O(m)\)
* **Total:** \(O(n+m)\)



### **Boyer-Moore-Horspool**

Diferentemente de KMP, o Boyer-Moore-Horspool não necessariamente avança a busca uma posição por vez. O padrão continua sendo deslocado da esquerda para a direita sobre o texto, mas, em cada alinhamento, seus caracteres são comparados da direita para a esquerda. Quando ocorre um match completo ou um mismatch, o algoritmo utiliza o caractere do texto atualmente alinhado com o último caractere do padrão para determinar quantas posições podem ser puladas com segurança.

Para isso, primeiro construímos uma **shift table**:

```py
def compute_shift_table(P):
    m = len(P)
    shift = {}

    for i in range(m - 1):
        shift[P[i]] = m - i - 1

    return shift
```

Para cada caractere presente em `P[0:m-1]`, armazenamos a distância entre sua ocorrência mais à direita nessa região e o último índice do padrão. Como percorremos o padrão da esquerda para a direita e sobrescrevemos valores anteriores, caracteres repetidos ficam associados à sua ocorrência mais à direita.O último caractere `P[m-1]` não participa da construção da tabela. Caracteres que não aparecem na tabela recebem um shift de `m`.

Depois fazemos a busca:

```py
def horspool_search(P, T):
    n, m = len(T), len(P)
    shift = compute_shift_table(P)
    matches = []

    i = 0
    while i <= n - m:
        j = m - 1
        while j >= 0 and T[i + j] == P[j]:
            j -= 1

        if j < 0:
            matches.append(i)
        i += shift.get(T[i + m - 1], m)

    return matches
```

Aqui, `i` representa o início da janela atual no texto e `j` representa uma posição dentro do padrão.

Em cada alinhamento, começamos com:

```python
j = m - 1
```

e comparamos:

```python
P[j] == T[i + j]
```

da direita para a esquerda.

Se `j < 0`, todos os caracteres do padrão foram comparados com sucesso, portanto encontramos uma ocorrência começando em `i`. Independentemente de termos encontrado um match ou mismatch, observamos:

```python
T[i + m - 1]
```

isto é, o caractere do texto alinhado com o final do padrão. Se esse caractere aparece em `P[0:m-1]`, deslocamos o padrão de forma a alinhar esse caractere do texto com sua ocorrência mais à direita no padrão. Se ele não aparece, podemos deslocar o padrão inteiro (`m` posições), pois nenhum alinhamento intermediário poderia produzir um match.A principal ideia do Horspool é usar informações sobre o caractere no final da janela para eliminar vários alinhamentos impossíveis de uma só vez.

#### **Complexidade**

* **Pré-processamento:** \(O(m)\)
* **Busca no pior caso:** \(O(nm)\)
* **Espaço adicional:** \(O(|\Sigma|)\) no caso de uma tabela explícita para todo o alfabeto, ou \(O(\min(m,|\Sigma|))\) usando um dicionário apenas para os caracteres presentes no padrão.

Um exemplo de pior caso é:

```text
T = aaaaaaaaaaaaaaaaa...
P = baaaa
```

Em cada alinhamento, vários `a`s são comparados com sucesso antes do mismatch em `b`. Além disso, como o caractere no final da janela é `a` e `shift['a'] = 1`, o padrão avança apenas uma posição. Assim, podemos realizar \(\Theta(m)\) comparações em \(\Theta(n)\) alinhamentos, resultando em:

$$
\Theta(nm)
$$

Na prática, especialmente quando o alfabeto é relativamente grande em relação ao padrão, os shifts frequentemente são maiores que 1. Por isso, Horspool costuma examinar muito menos posições do texto que algoritmos que avançam uma posição por vez.


### **Shift-And**

De forma similar ao KMP, ele também utiliza um estados para armazenar os prefixos do padrão que são sufixos do que foi casado ate agora. O primeiro passo é criar uma mascara binária para cada caracter do padrão.

```py
def compute_masks(P):
    masks = {}

    for j, c in enumerate(P):
        masks[c] = masks.get(c, 0) | (1 << j)

    return masks
```

A mascara do caractere *c* é um vetor de bits do mesmo tamanho *m* do padrão e se o char *c* aparece na posição i do vetor de bits o bit é 1, senão é 0. Para o padrão:

```text
P = a b a
    0 1 2
```

temos: 

```text
a → 101
b → 010
```

Depois fazemos a busca:

```py
def shift_and_search(P, T):
    m = len(P)

    if m == 0:
        return []

    masks = compute_masks(P)

    state = 0
    matches = []

    for i, c in enumerate(T):
        state = ((state << 1) | 1) & masks.get(c, 0)

        if state & (1 << (m - 1)):
            matches.append(i - m + 1)

    return matches
```

#### **Complexidade** 

Assumindo que o padrão cabe em uma palavra da máquina, ou seja, m < w sendo w o numero de bits de uma palavra

**Pré-processamento:** \(O(m)\)
**Busca:** \(O(n)\)
**Espaço:** \(O(|\Sigma|)\) com tabela explícita, ou \(O(\min(m,|\Sigma|))\) com dicionário
**Total:** O(n+m)

### **Quando usar qual ?**

Os três algoritmos surgiram em um contexto em que características do hardware tinham bastante impacto no projeto do algoritmo: memória era limitada, operações sobre palavras da CPU eram muito baratas e evitar acessos/comparações desnecessárias podia gerar ganhos significativos.

#### **KMP**

O KMP foi publicado em 1977 e sua principal vantagem é garantir o O(n+m) independentemente do texto e do padrão. Ele processa o texto *sequencialmente* e nunca precisa voltar nele. Quando ocorre um mismatch, a função prefixo informa quanto do casamento anterior pode ser reaproveitado. Isso era especialmente interessante em situações em que o texto não estava necessariamente inteiro disponível em memória, como processamento sequencial de arquivos ou streams. Além disso, não existia acesso aleatório a memória, apenas sequencial eficiente e por isso ele le o texto da esquerda para direita.

**Exemplos de uso:**

- procurar um padrão em um arquivo muito grande;
- processar dados recebidos sequencialmente por uma rede;
- situações em que precisamos de garantia \(O(n)\), inclusive para entradas adversariais;
- padrões longos, nos quais técnicas baseadas em uma única palavra da CPU não são aplicáveis.

A principal vantagem do KMP é, portanto, sua **previsibilidade e processamento sequencial**.

#### **Boyer-Moore-Horspool**

Horspool foi publicado em 1980 como uma simplificação de Boyer-Moore. Enquanto KMP processa essencialmente todos os caracteres do texto, Horspool tenta **não olhar para todos eles**. A partir do caractere no final da janela, ele pode eliminar vários alinhamentos de uma vez e isso funciona particularmente bem quando o alfabeto é grande e o padrão tem tamanho suficiente para produzir bons saltos. Por exemplo em um grande texto ASCII, muitos caracteres encontrados no final das janelas não aparecem no padrão. Nesse caso podemos frequentemente avançar m posições de uma vez.

**Exemplos de uso:**

-  busca de uma palavra dentro de um documento;
-  busca de uma sequência de bytes em um arquivo;
-  textos naturais e alfabetos relativamente grandes;
-  quando o desempenho médio/prático importa mais que a garantia de pior caso.

Sua desvantagem é o pior caso é O(nm) e portanto, Horspool troca a garantia forte do KMP pela possibilidade de **pular grandes regiões do texto na prática**.

#### **Shift-And**

Shift-And explora diretamente uma característica do hardware: uma CPU consegue realizar operações bitwise sobre uma palavra inteira em uma única operação.Em uma máquina com palavra de 64 bits, por exemplo:

```text
0001001010010010101010010100101010100101010100101010010101001010
```
pode ser manipulado por operações bitwise em pouquíssimas instruções.

Assim, em vez de atualizar individualmente vários estados do casamento, Shift-And representa esses estados como bits e os atualiza **em paralelo dentro de uma palavra da máquina**.

**Exemplos de uso:**

- procurar padrões curtos;
- busca de pequenas sequências de DNA;
- processamento em que milhões de buscas pequenas precisam ser executadas;
- algoritmos de approximate/fuzzy matching derivados dessa técnica.

Se o padrão for muito maior que a palavra da máquina, precisamos usar várias palavras:

$$
O\left(n\left\lceil\frac{m}{w}\right\rceil\right)
$$

e parte da vantagem diminui.

# **Estruturas de dados para casamento de padrões**

## **Trie (Arvore de prefixo)**

Uma arvore M-nária, ou seja, cada nó pode ter ate M filhos em que esses filhos podem ser qualquer digito/char de um alfabeto. Palavras de um texto/conteudo são adicionadas sequencialmente na arvore (ordens diferentes geram arvores diferentes) em que cada cada nó dessa arvore é um caractere dessas palavras. Uma consequencia disso é que todas as palavras de uma mesma subarvore compartilham o mesmo prefixo. Elas são uteis para indices invertidos, autocomplete e geração de itemsets frequentes em mineração de dados (fp_growth). Cada nó dessa arvore guarda um valor que representa quantas vezes uma palavra terminada nesse nó aparece.

### **Busca**

Iniciamos a busca pela raiz (que não possui um char) e vamos descendo na arvore usando o i-esimo char do chave que estamos buscando. Existem 3 casos possiveis:

- Algum simbolo da chave (nó) não existe (chave não existe na arvore) -> False
- Os simbolos da chave (nó) existem mas o valor associado ao ultimo nó é 0 (A palavra nunca foi adicionada na arvore) -> False
- Os simbolos da chave (nó) existem e o valor é maior que 0 (palavra ja foi adicionada) -> True

### **Inserção**

Fazemos uma busca (igual a anterior) adicionando os simbolos da esquerda para direita.

- Se durante a busca chegar um momento em que não existe nós na arvore para o simbolo atual, criar o nó, repetir a criação de nó até chegar ao simbolo final e incrementar o valor do final.
- Se todo os simbolos da chave já estiverem na arvore, incrementamos o valor do nó do ultimo simbolo (ou adicionamos alguma coisa nele como documentos e etc)

### **Remoção**



## **Trie Ternária**

## **Trie Compacta**

## **Arvore de sufixo**