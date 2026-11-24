# **Automatos**

Em geral são estruturas matematicas parecidas com uma maquina de estados mas sem outputs nos estados

## **Deterministico** 

È um conjunto finito de estados, alfabeto de ações e funções de transição que levam de um estado x1 através da ação y ate um novo estado x2. Além disso, dentre o conjuntos de estados temos o estado inicial e estados finais (aceitação). A função de transição é **total**: para cada par (estado, símbolo) existe **exatamente um** próximo estado, ou seja, δ: Q × Σ → Q.

## **Não Deterministico** 

É a mesma estrutura, mas a função de transição pode levar um estado x1, através da ação y, para mais de um estado possível ao mesmo tempo (ou pra nenhum), ou seja, δ: Q × Σ → P(Q), retorna um conjunto de estados em vez de um único. Também pode ter transições ε, que mudam de estado sem consumir nenhum símbolo da entrada. Todo AFN pode ser convertido em um AFD (construção de subconjuntos, em que cada estado do AFD é um subconjunto de estados do AFN), então os dois reconhecem as mesmas linguagens. Porém o AFD resultante pode ter até 2^|Q| estados: o poder é o mesmo, mas o tamanho pode explodir.

## **Problema**

Automatos só tem estados para lembrar de informações e o numero de estados é fixo. Logo, nenhum autômato consegue **reconhecer a linguagem** {0^n 1^n | n >= 0} (strings com n zeros seguidos de exatamente n uns), pois isso exigiria armazenar quantos 0s foram vistos para comparar depois com os 1s. Se o autômato tem k estados, depois de ler mais de k zeros ele necessariamente repete algum estado e "esquece" a contagem exata. A formalização desse argumento é o **Lema do Bombeamento**.


# **Maquinas de Turing**

Também são estruturas matemáticas parecidas com máquina de estados, mas agora com uma fita infinita que pode ser lida e escrita (em vez de só ler a entrada uma vez, da esquerda pra direita).

## **Determinística**

É um conjunto finito de estados, um alfabeto de entrada, um alfabeto de fita (que inclui o alfabeto de entrada mais um símbolo de branco), e uma função de transição que leva um estado x1, ao ler um símbolo da fita, para um novo estado x2, escrevendo um novo símbolo no lugar e movendo o cabeçote uma casa pra esquerda ou direita. Ou seja, δ: Q × Γ → Q × Γ × {E, D}.

Além disso, dentre o conjunto de estados temos o estado inicial e dois estados de parada especiais: aceitação e rejeição. No autômato, a computação sempre termina quando a entrada acaba, então "não terminar em estado final" já significa rejeitar. Na MT isso não acontece: ela anda pela fita para os dois lados e só para quando entra em um dos estados de parada, por isso a rejeição precisa ser um estado explícito.

Isso gera três resultados possíveis para uma entrada: **aceita, rejeita ou entra em loop** (nunca para). O problema da parada é justamente perguntar se é possível decidir, para qualquer máquina e entrada, se ela vai parar, e isso é indecidível.

## **Não determinística**

Mesma estrutura, mas a função de transição pode levar um estado x1, ao ler um símbolo, para mais de um par (estado, símbolo escrito, direção) possível ao mesmo tempo, δ: Q × Γ → P(Q × Γ × {E, D}). A computação vira uma árvore de ramos, e a máquina **aceita se algum ramo** chega ao estado de aceitação.

Assim como AFN → AFD, **toda MT não determinística pode ser simulada por uma MT determinística** (por exemplo, explorando a árvore de ramos em largura). Então, para computabilidade, as duas são equivalentes. A diferença aparece no tempo: a simulação pode custar exponencialmente mais, e é por isso que a definição de NP usando MT não determinística faz sentido.


### **Algoritmo**

Algortimo pode ser definido como uma sequência finita e bem definida de passos que resolve um problema ou também como uma maquina de turing que para em toda entrada tem uma saida.

### **Computabilidade**

Estuda o que pode ser computado, sem se importar com quanto tempo ou memória isso custa, só importa se existe algum algoritmo, nesse sentido formal, que resolve o problema.

### **Tratabilidade**

Aqui a pergunta muda: entre os problemas computáveis (decidíveis), quais têm um algoritmo eficiente, que roda em tempo razoável mesmo pra entradas grandes?

As classes abaixo são definidas para **problemas de decisão**, ou seja, problemas cuja resposta é sim ou não (ex: "existe um caminho de custo ≤ k?" em vez de "qual o caminho de menor custo?").

- **P:** problemas decidíveis por uma MT determinística em tempo polinomial (O(nᵏ) pra algum k), considerados "tratáveis" na prática.
- **NP:** problemas cuja solução, uma vez proposta, pode ser verificada em tempo polinomial por uma MT determinística (essa solução proposta é chamada de **certificado**, e para instâncias com resposta "sim" ele tem tamanho polinomial), equivalente a dizer que uma MT não determinística resolve o problema em tempo polinomial.

Todo problema em P também está em NP (se dá pra resolver rápido, dá pra verificar rápido também, é só resolver de novo). A pergunta em aberto mais famosa da computação é se vale a volta: P = NP? A maioria dos pesquisadores acredita que não, mas ninguém conseguiu provar.

# **Algoritmos de casamento de padrões**

Problema recorrente em diversos lugares, por exemplo control-f em que queremos encontrar ocorrencias de uma pradrao especifico, mas também é util em diversas outras aplicacoes como encontrar padroes em DNAs ou proteinas, compressao de textos e antivirus. 

## **String Matching**

Busca de padroes em texto, esses padroes e textos sao subconjuntos de um conjunto finito de caracteres chamado chamado alfabeto (sigma maiusculo).

- Texto: Representado como T[1...n]
- Padrao: Representado como P[1...m] e se queremos representar kth prefixo do padrão (k primeiros elementos P[1...k]) usamos $P_k$
- Amboa indexados em 1

O problema entao é encontrar um indice s em que 0 <= s <= n - m (se isso nao for verdade o padrao P nao cabe no final do texto T). Tal que T[s+1...s+m] = P[1...m], ou seja, s é o indice anterior ao padrao totalmente igual encontrado no texto. E como indices vao de 0 a n-m entao podemor usar -1 como flag para nao existe padrao. Outra forma de definir é encontrar s tal que P ⊐ $T_{s + m}$, ou seja, s tal que o padrão é sufixo do prefixo de T que vai s+m.

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

    for i in range(n - m + 1): # +1 para incluir a ultima janela (s = n - m)
        if T[i:i + m] == P:
            matches.append(i)

    return matches
```

Aqui passamos uma janela deslizante de tamanho m no texto T inteiro e comparamos cada uma dessas janelas com o padrão P de tamanho m. Todas as janelas deslizantes tem complexidade O(n) e para cada janela temos uma comparação de tamanho O(m). 

- *Complexidade de tempo:* Θ((n - m + 1) * m), ou seja O(n * m). Esse produto é máximo quando m ≈ n/2, dando ≈ n²/4 = O(n²). Para m > n/2 o número de janelas diminui (com m = n existe só 1 janela e o custo é O(n)). Pior caso clássico: T = aaaa...a e P = aaab, em que cada janela compara quase m caracteres antes de falhar.
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

- **Total:** O(m^3 * |Σ| + n)

- **Espaço:** O(m * |Σ|), pois a tabela tem (m+1) * |Σ| entradas.

Obs: usando a função prefixo do KMP (próxima seção) dá para construir δ em O(m * |Σ|), já que, para q > 0, δ(q, a) = δ(π[q-1], a) quando q = m ou P[q] != a.

#### **Intuição**

Primeiro passo é computar a tabela de transições que mapeia o estado atual + novo char lido para um novo estado (dado que estado é quantos caracteres do padrao ja casamos). A construcao dessa tabela tem complexidade m^3 * alfabeto e após ser construida passamos linearmente no texto T e atualizamos o estado atual com os novos caracteres lidos do texto, se chegarmos ao estado final (estado = tamanho do padrao) entao ja casamos todo o padrao e podemos salvar uma aparicao.

#### **Exemplo**

Exemplo usado em todos os algoritmos desta seção:

```text
P = abab          (m = 4)
T = abacababab    (n = 10)
    0123456789
Ocorrências: posições 4 e 6 (se sobrepõem)
```

Tabela δ para P = abab com Σ = {a, b, c}:

| q \ char | a | b | c |
|---|---|---|---|
| 0 | 1 | 0 | 0 |
| 1 (`a`) | 1 | **2** | 0 |
| 2 (`ab`) | **3** | 0 | 0 |
| 3 (`aba`) | 1 | **4** | 0 |
| 4 (`abab`) aceita | **3** | 0 | 0 |

- **δ(3, a) = 1:** eu tinha `aba` e li `a`, então tenho `abaa`. O maior prefixo de P que é sufixo disso é só `a`, então não precisamos voltar para 0.
- **δ(4, a) = 3:** depois de casar `abab`, ler `a` dá `ababa`, que termina em `aba`. É assim que o autômato encontra ocorrências sobrepostas.

Busca: os estados percorridos são 1, 2, 3, 0, 1, 2, 3, **4**, 3, **4**, com matches terminando em i = 7 (início 4) e i = 9 (início 6).

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

Note que `compute_prefix_function` tem a mesma estrutura de `kmp_search`: calcular π é rodar o próprio KMP do padrão contra ele mesmo (começando de q = 1 para que o prefixo seja próprio).

#### **Exemplo**

```text
P  = a b a b
π  = 0 0 1 2
```
- π[2] = 1: em `aba`, o prefixo `a` também é sufixo
- π[3] = 2: em `abab`, o prefixo `ab` também é sufixo

Busca em T = abacababab:

```text
i  c  matched
0  a  0 → 1
1  b  1 → 2
2  a  2 → 3        (casei "aba")
3  c  3 → 0        mismatch: π[2]=1 → P[1]='b'≠'c' → π[0]=0 → P[0]='a'≠'c' → fica 0
4  a  0 → 1
5  b  1 → 2
6  a  2 → 3
7  b  3 → 4  MATCH em 4, matched = π[3] = 2   ← reaproveita "ab"
8  a  2 → 3
9  b  3 → 4  MATCH em 6
```

Comparando com o autômato: o KMP passa pelos mesmos estados, mas em vez de consultar δ(q, c) direto, ele chega no estado certo seguindo a cadeia π até achar um prefixo compatível.

#### **Complexidade**

* **Pré-processamento:** \(O(m)\)
* **Busca:** \(O(n)\)
* **Espaço adicional:** \(O(m)\)
* **Total:** \(O(n+m)\)

**Por que O(n) se tem um `while` dentro do `for`?** É uma análise amortizada: `matched` sobe no máximo 1 por iteração do `for` (no total no máximo n subidas), e cada passo do `while` faz `matched` diminuir pelo menos 1. Como ele nunca fica negativo, não pode descer mais do que subiu, então o total de passos do `while` em toda a execução é ≤ n. O mesmo argumento (com `k`) dá O(m) para o cálculo de π.



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

O último caractere fica de fora porque, se entrasse, `shift[P[m-1]]` seria 0 e o algoritmo nunca sairia da janela atual. Usando só `P[0:m-1]` todo shift é ≥ 1.

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

isto é, o caractere do texto alinhado com o final do padrão. Se esse caractere aparece em `P[0:m-1]`, deslocamos o padrão de forma a alinhar esse caractere do texto com sua ocorrência mais à direita no padrão. Se ele não aparece, podemos deslocar o padrão inteiro (`m` posições), pois nenhum alinhamento intermediário poderia produzir um match. A principal ideia do Horspool é usar informações sobre o caractere no final da janela para eliminar vários alinhamentos impossíveis de uma só vez.

#### **Exemplo**

```text
P = a b a b
shift['a'] = 1   (ocorrência mais à direita de 'a' em "aba" está a 1 do fim)
shift['b'] = 2
qualquer outro = m = 4
```

Busca em T = abacababab:

```text
janela 0: "abac"  compara P[3]='b' com 'c' → mismatch.  Último char = 'c' → não está em P → pula 4
janela 4: "abab"  MATCH.                                Último char = 'b' → pula 2
janela 6: "abab"  MATCH.                                Último char = 'b' → pula 2 → i = 8 > n - m, fim
```

Foram 3 janelas em vez das 7 do ingênuo. O salto de 4 é seguro porque qualquer janela que contenha aquele `c` não pode casar, já que `c` não existe em P.

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

De forma similar ao KMP, ele também utiliza um estado para armazenar os prefixos do padrão que são sufixos do que foi lido ate agora. A diferença é que KMP e o autômato guardam apenas o **maior** desses prefixos, enquanto o Shift-And guarda **todos ao mesmo tempo**, como um vetor de m bits:

> o bit j do estado vale 1 ⟺ os últimos j+1 caracteres lidos são iguais a P[0..j]

Isso é simular o autômato **não determinístico** do padrão, em que cada bit é um estado que pode estar ativo ou não. O primeiro passo é criar uma mascara binária para cada caracter do padrão.

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

A atualização `state = ((state << 1) | 1) & masks[c]` tem três partes:

- **`state << 1`:** todo casamento parcial de tamanho j+1 (bit j) tenta virar um de tamanho j+2 (bit j+1).
- **`| 1`:** um casamento novo sempre pode começar no caractere atual (bit 0).
- **`& masks[c]`:** só sobrevivem os casamentos em que o próximo caractere do padrão é realmente `c`, ou seja, os bits j com P[j] == c.

Se o bit m-1 estiver ligado, os últimos m caracteres lidos formam o padrão inteiro, então há um match terminando em i.

#### **Exemplo**

Máscaras para P = abab (bit 0 na ponta direita):

```text
mask[a] = 0101
mask[b] = 1010
mask[c] = 0000
```

Busca em T = abacababab:

```text
i c   state
0 a   0001   casei "a"
1 b   0010   casei "ab"
2 a   0101   casei "aba" E "a" (dois casamentos parciais vivos ao mesmo tempo)
3 c   0000   tudo morre
4 a   0001
5 b   0010
6 a   0101
7 b   1010   bit 3 ligado → MATCH em 4 ("ab" continua vivo no bit 1)
8 a   0101
9 b   1010   MATCH em 6
```

Cada passo são poucas instruções de CPU (shift, or, and), sem `if` nem `while` para tratar mismatch.

#### **Complexidade** 

Assumindo que o padrão cabe em uma palavra da máquina, ou seja, m ≤ w sendo w o numero de bits de uma palavra

**Pré-processamento:** \(O(m)\)
**Busca:** \(O(n)\)
**Espaço:** \(O(|\Sigma|)\) com tabela explícita, ou \(O(\min(m,|\Sigma|))\) com dicionário
**Total:** O(n+m)

### **Relação entre os algoritmos**

| Algoritmo | Pré-processamento | Ideia |
|---|---|---|
| Ingênuo | nada | testa todas as janelas |
| Autômato | tabela δ(estado, char) | estado = maior prefixo de P que é sufixo do que li, com a transição pronta para qualquer char |
| KMP | vetor π (só depende do padrão) | mesmo estado do autômato, mas a transição é calculada na hora seguindo π |
| Horspool | tabela de shift | olha o último char da janela e pula vários alinhamentos de uma vez |
| Shift-And | máscara de bits por char | guarda todos os casamentos parciais ao mesmo tempo, em bits |

```text
Autômato ──(tira a tabela δ, calcula a transição sob demanda com π)──▶ KMP
Autômato ──(usa o AFN em vez do AFD, estados viram bits)────────────▶ Shift-And
Ingênuo  ──(compara de trás pra frente e pula janelas)──────────────▶ Horspool
```

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

Aqui o problema é resolvido ao contrário da seção anterior:

| | Algoritmos (KMP, Horspool…) | Estruturas (Trie, Árvore de Sufixos) |
|---|---|---|
| O que é pré-processado | o **padrão** | o **texto** ou o **conjunto de chaves** |
| Custo por consulta | O(n), sempre lendo o texto | O(m), **sem depender do tamanho do texto** |
| Bom para | texto novo a cada busca | texto fixo com muitas consultas |

Exemplos usados nesta seção: chaves **{casa, caso, cal, do}** para as tries e **banana$** para a árvore de sufixos.

## **Trie (Arvore de prefixo)**

Uma arvore M-nária, ou seja, cada nó pode ter ate M filhos em que esses filhos podem ser qualquer digito/char de um alfabeto. Palavras de um texto/conteudo são adicionadas na arvore em que cada cada nó dessa arvore é um caractere dessas palavras. A trie é determinada apenas pelo **conjunto** de chaves: cada chave sempre segue o mesmo caminho, então a ordem de inserção não muda a árvore final. Uma consequencia disso é que todas as palavras de uma mesma subarvore compartilham o mesmo prefixo. Elas são uteis para indices invertidos, autocomplete e geração de itemsets frequentes em mineração de dados (fp_growth). Cada nó dessa arvore guarda um valor que representa quantas vezes uma palavra terminada nesse nó aparece.

```text
raiz
├─ c
│  └─ a
│     ├─ s
│     │  ├─ a ●     "casa"
│     │  └─ o ●     "caso"
│     └─ l ●        "cal"
└─ d
   └─ o ●           "do"
```

(● = nó em que alguma chave termina, ou seja, valor > 0)

Todas as chaves que começam com "ca" estão na subárvore de c → a, por isso a trie serve para autocomplete: basta descer pelo prefixo e listar a subárvore.

### **Busca**

Iniciamos a busca pela raiz (que não possui um char) e vamos descendo na arvore usando o i-esimo char do chave que estamos buscando. Existem 3 casos possiveis:

- Algum simbolo da chave (nó) não existe (chave não existe na arvore) -> False
- Os simbolos da chave (nó) existem mas o valor associado ao ultimo nó é 0 (A palavra nunca foi adicionada na arvore) -> False
- Os simbolos da chave (nó) existem e o valor é maior que 0 (palavra ja foi adicionada) -> True

Exemplo: "cas" existe como caminho mas o nó do s não está marcado, então é False. "cama" para no m, que não existe, e também é False.

*Complexidade de tempo:* O(m) sendo m o tamanho da chave, independente de quantas chaves existem na árvore

### **Inserção**

Fazemos uma busca (igual a anterior) adicionando os simbolos da esquerda para direita.

- Se durante a busca chegar um momento em que não existe nós na arvore para o simbolo atual, criar o nó, repetir a criação de nó até chegar ao simbolo final e incrementar o valor do final.
- Se todo os simbolos da chave já estiverem na arvore, incrementamos o valor do nó do ultimo simbolo (ou adicionamos alguma coisa nele como documentos e etc)

*Complexidade de tempo:* O(m) sendo m o tamanho da chave

### **Remoção**

Buscamos a chave que queremos remover ate chegar no ultimo nó dela

- Se nó final tem filhos, apenas reduz o contador e apaga os valores
- Se o nó não tiver filhos, apaga e repete recursivamente no pai, **parando** quando chegar em um nó que tenha outro filho ou que seja fim de outra chave (valor > 0)

Exemplo: remover "casa" apaga apenas o a final, porque o s ainda tem o filho o (de "caso").

*Complexidade de tempo:* O(m) sendo m o tamanho da chave

### **Tradeoff espaço x tempo**

Trie tem um otimo desempenho em tempo mas custo de armazenamento depende do numero de palavras e tamanho do alfabeto e pode se tornar muito cara para alfabetos e palavras muito longas.

*Complexidade de espaço:* O(N * alfabeto) pois cada um dos N nós da arvore temos que armazenar *alfabeto* ponteiros (pois cada nó pode ter alfabeto ponteiros). No exemplo são 9 nós (contando a raiz) × 26 = 234 ponteiros, dos quais só 8 são usados.

## **Trie Ternária**

Para tentar mitigar o problema de memória na Trie ternária cada nó guarda um caractere e tem apenas 3 filhos. Comparando o simbolo atual da chave com o **simbolo do próprio nó**:

- **left_children:** simbolo da chave é menor alfabeticamente que o simbolo do nó
- **mid_children:** simbolo da chave é igual ao simbolo do nó
- **right_children:** simbolo da chave é maior alfabeticamente que o simbolo do nó

Ou seja, cada nível da trie original vira uma pequena árvore binária de busca (pelos ponteiros left/right) e o ponteiro mid desce para o próximo nível. **Só o mid consome um caractere da chave**: left e right apenas procuram o caractere certo entre as alternativas daquele nível.

Inserindo casa, caso, cal, do nessa ordem:

```text
c
├─mid─ a
│      └─mid─ s
│             ├─left── l ●            (l < s)
│             └─mid─── a ●
│                      └─right─ o ●   (o > a)
└─right─ d
         └─mid─ o ●
```

Diferente da trie, **a forma da TST depende da ordem de inserção**: se "do" fosse inserida primeiro, d seria a raiz e c ficaria à sua esquerda.

### **Busca**

Passamos pela chave da esquerda para direita e para cada simbolo:

- se o *simbolo* é menor que o nó atual -> vai para a esquerda
- se o *simbolo* é igual que o nó atual -> vai para o meio
- se o *simbolo* é maior que o nó atual -> vai para a direita 
- se nó não não existe ou valor é nulo -> False

Exemplo, buscando "cal":

```text
'c' = c  → mid    (avança na chave)
'a' = a  → mid    (avança na chave)
'l' < s  → left   (NÃO avança na chave)
'l' = l  → chave acabou e nó está marcado → True
```

*Complexidade de tempo:* O(m + log N) no **caso médio** (chaves inseridas em ordem aleatória, N = número de chaves). Se as chaves forem inseridas ordenadas, as BSTs de cada nível degeneram em listas e o pior caso vira O(m * |Σ|).

### **Inserção**

- Busca a chave na arvore
- Ao encontrar nós nulos insere
- Ao inserir o último símbolo, marcamos o nó como fim da chave

*Complexidade de tempo:* *O(m + log N)* no caso médio, O(m * |Σ|) no pior caso

### **Memória**

*O(N)* pois cada nó tem apenas 3 ponteiros (constante) em cada um dos N nós

## **Trie Compacta**

Aqui ataca o problema de forma diferente em que condensamos varios nós em um unico criando nós de prefixos inteiros. Nessa formulação apenas nós de folha armazenam valores/itens e o restante serve para acelerar a busca. Para a Trie compacta é melhor imaginar que toda palavra/chave é igual a ela mesmo concatenada com um simbolo terminal $, ou seja, "water" -> "water$". Sem o $, se "ca" e "casa" fossem chaves, "ca" terminaria no meio de um caminho (nó interno). Com o $ nenhuma chave é prefixo de outra, então **toda chave termina em uma folha**.

```text
raiz
├─ "ca"
│   ├─ "s"
│   │   ├─ "a$" ●
│   │   └─ "o$" ●
│   └─ "l$" ●
└─ "do$" ●
```

As cadeias de nós com um único filho da trie (como d → o) viraram uma única aresta rotulada com uma substring.

### **Busca**

- Vamos casando os prefixo da chave com os nós
- Se chegar em uma folha e ainda existe simbolos na chave -> False
- Se chegar em uma folha e chave já foi toda processada, achou -> True

### **Inserção**

- Se a árvore estiver vazia, criamos um nó folha contendo toda a chave. 
- Caso contrário, buscamos a chave comparando o restante dela com o prefixo dos nós.
- Durante a busca:

    - **Prefixo inteiro do nó casa com o início da chave restante** → avançamos para esse nó e continuamos a busca com o restante da chave.

    - **Nenhum filho compartilha prefixo com a chave restante** → criamos um novo nó folha contendo todo o restante da chave.

    - **Apenas parte do prefixo do nó casa com a chave restante** → dividimos o nó:

        - criamos um nó interno com o maior prefixo comum;
        - alteramos o prefixo do nó antigo para seu sufixo restante e o colocamos como filho;
        - criamos outro nó folha com o sufixo restante da nova chave.

    - **A chave termina no meio do prefixo de um nó** → dividimos o nó no ponto onde a chave termina:

        - criamos um nó interno com o prefixo comum;
        - o nó antigo passa a armazenar apenas seu sufixo restante;
        - criamos uma folha terminal para representar o fim da nova chave.

Exemplos na árvore acima:

- Inserir "cama$": casa a aresta "ca" inteira, sobra "ma$". Nenhum filho começa com m, então criamos a folha "ma$" abaixo de "ca".
- Inserir "cl$": só o "c" casa com a aresta "ca". Dividimos o nó em "c", com filhos "a" (que mantém a subárvore antiga, com "s" e "l$") e a nova folha "l$".

### **Remoção**

- Buscar nó contendo a chave
- se o nó não for nulo, remove
- se o pai desse nó ficar com apenas 1 filho (e não for a raiz), concatena prefixo do pai e filho e remove o filho 

Exemplo: remover "cal$" deixa o nó "ca" só com o filho "s", então juntamos os dois em uma aresta "cas".

### **Memória**

Reduz bastante o numero de nós: como todo nó interno tem pelo menos 2 filhos, com K chaves existem K folhas e no máximo K - 1 nós internos, ou seja, **no máximo 2K - 1 nós**. Esse número depende só da quantidade de chaves e não do tamanho delas.

O custo é armazenar mais coisas em cada nó (substrings inteiras). Um jeito de mitigar isso é dado que as palavras estão em um vetor ou outra estrutura, podemos armazenar os indices para essa estrutura na trie: cada aresta guarda (id_palavra, início, fim) em vez da substring, e cada nó passa a ocupar O(1).

## **Árvore de Sufixos**

Uma árvore de sufixos (Suffix Tree) é uma **Trie compactada contendo todos os sufixos de um texto `T`**.A ideia vem do fato de que um padrão `P` ocorre em `T` se, e somente se, `P` é prefixo de algum sufixo de `T`.

Por exemplo, para:

```text
T = banana$
```

inserimos conceitualmente os sufixos:

```text
banana$
anana$
nana$
ana$
na$
a$
$
```

Depois compactamos caminhos com apenas um filho, armazenando substrings inteiras nas arestas em vez de um caractere por aresta. Para buscar um padrão `P`, percorremos a árvore consumindo seus caracteres. Se conseguirmos consumir todo `P`, então ele ocorre em `T`.

O `$` é necessário pelo mesmo motivo da trie compacta: sem ele, o sufixo "a" de "banana" é prefixo de "ana" e "anana", e terminaria no meio de um caminho. Com o `$`, cada sufixo termina em uma folha própria.

Árvore resultante (o número em cada folha é a posição onde aquele sufixo começa em T):

```text
raiz
├─ "$"            (6)
├─ "a"
│   ├─ "$"        (5)
│   └─ "na"
│       ├─ "$"    (3)
│       └─ "na$"  (1)
├─ "banana$"      (0)
└─ "na"
    ├─ "$"        (4)
    └─ "na$"      (2)
```

Buscando P = "ana": raiz → "a" → "na", consumindo o padrão inteiro. **As folhas da subárvore onde a busca terminou são exatamente as ocorrências de P**: posições 1 e 3 (b**ana**na e ban**ana**).

Outras consultas que ficam simples:

- **Quantas vezes P aparece:** número de folhas da subárvore (pode ser pré-calculado em cada nó).
- **Maior substring repetida:** o nó interno mais profundo (em número de caracteres). Aqui é "ana".

### **Complexidade**

Se \(n = |T|\) e \(m = |P|\):

* **Construção ingênua:** \(O(n^2)\) -> inserir cada um dos n sufixos custa O(n)
* **Construção com algoritmos como Ukkonen:** \(O(n)\), considerando alfabeto de tamanho constante
* **Espaço da Suffix Tree:** \(O(n)\) -> são n folhas (uma por sufixo) e no máximo n - 1 nós internos (cada um tem ≥ 2 filhos). Isso só vale porque as arestas guardam índices (início, fim) em T em vez das substrings; guardando substrings, ou sem compactar (suffix trie), seria O(n^2)
* **Busca do padrão (existe ou não):** \(O(m)\)
* **Listar todas as ocorrências:** \(O(m + occ)\), sendo occ o número de ocorrências (percorrer as folhas da subárvore)

O pré-processamento pode valer a pena quando o texto permanece fixo e queremos realizar **muitas consultas de padrões diferentes**, pois construímos a estrutura uma vez e cada busca posterior depende essencialmente apenas do tamanho do padrão.

# **Geometria Computacional**

Ramo da computação que estuda soluções algorítmicas para problemas geométricos

## **Operações com segmentos de reta**

Dado segmentos de retas (vetores) definidos por seus pontos das extremidades, ou seja, p0p1 é uma reta que vai do ponto p0 ao p1. Todas as operações que aqui tem complexidade de O(1).

### **Orientação/Direção de um segmento em relação a outro**

Se temos 2 segmentos de reta de mesma origem, por exemplo, a = p0p1 e b = p0p2 como sabemos se *a* esta a direita ou esquerda de *b*, *a* esta no sentido horario de *b* ou antihorario ?

A ideia aqui é o usar o produto vetorial a x b que é equivalente a area do paralelograma entre esses vetores para descobrir a direção de b em relação a a, esse produto vetorial pode ser calculado como o determinante da matriz desses 2 vetores. Lembrando que (a x b) = - (b x a).

- Se o determinante é *positivo*, então b esta a *esquerda* de a, ou seja, sentido antihorario 
- Se o determinante é *negativo*, então b esta a *direita* de a, ou seja, sentido horario
- Se o determinante é *0*, então b e a são *colineares* (mesma reta)

Escrevendo com os pontos (os vetores precisam ser relativos à origem comum p0):

```text
orient(p0, p1, p2) = (p1 − p0) × (p2 − p0)
                   = (x1 − x0)(y2 − y0) − (x2 − x0)(y1 − y0)
```

Exemplo: p0 = (0,0), p1 = (4,0), p2 = (2,3) → 4·3 − 2·0 = 12 > 0, então p2 está à esquerda de p0→p1.

O valor absoluto é a área do paralelogramo (o dobro da área do triângulo p0p1p2) e o sinal dá a orientação. Essa primitiva é a base de toda a seção: usa só multiplicação e subtração, sem divisão, ângulos ou raízes, então nunca divide por zero e com coordenadas inteiras o resultado é exato.

### **Detectar mudança de direção da rota**

Dado uma rota composta por 3 pontos p0 -> p1 -> p2, no ponto p1 queremos saber se vamos virar a esquerda, direita ou continuar reto. Isso converge para exatamente o problema anterior, só precisamos calcular (p0p1) × (p0p2) = orient(p0, p1, p2), nessa ordem (pois a × b = − b × a): positivo → vira à esquerda em p1, negativo → vira à direita, 0 → segue reto. Isso funciona porque (p1 − p0) × (p2 − p0) tem o mesmo valor que (p1 − p0) × (p2 − p1).

### **Determinar se dois segmentos se interceptam**

Considerando 2 segmentos p0p1 e p2p3, eles se interceptam se **cada um atravessa a reta que contém o outro**:

```text
d1 = orient(p2, p3, p0)   d2 = orient(p2, p3, p1)   → sinais opostos? (p0 e p1 em lados opostos da reta p2p3)
d3 = orient(p0, p1, p2)   d4 = orient(p0, p1, p3)   → sinais opostos? (p2 e p3 em lados opostos da reta p0p1)
```

As **duas** condições são necessárias. Contraexemplo com só uma: p0p1 = (0,0)→(0,4) e p2p3 = (5,2)→(6,2). p0 e p1 estão em lados opostos da reta y = 2, mas p2 e p3 estão do mesmo lado da reta x = 0, e os segmentos não se tocam.

Exemplo em que se cruzam: p0p1 = (0,0)→(4,4) e p2p3 = (0,4)→(4,0) dá d1 = −16 e d2 = 16 (opostos), e simetricamente d3 e d4 também são opostos.

Entretanto se alguma das 4 orientações der 0 então significa que um ponto esta na mesma reta mas não necessáriamente no mesmo segmento de reta (segmento é um subconjunto da reta), se isso acontecer é necessário verificar o ponto e as extremidades do segmento para ver se existe ou não interseção: basta testar se o ponto está dentro do retângulo delimitador (bounding box) do segmento, ou seja, min(x) ≤ x ≤ max(x) e min(y) ≤ y ≤ max(y).

## **Alguns problemas relacionados**

### **Determinar se há interseção de segmentos em um conjunto de segmentos**

Assumindo que:
- Três segmentos não se interceptam em um único ponto
- Não há segmentos puramente verticais

O algoritmo é:

1. Ordene todos os pontos das extremidades dos segmentos por x 
2. Passe por todas as coordenadas x dos pontos ordenados da esquerda para a direita 
3. Mantenha os segmentos ativos ordenados pelo y em que a reta de varredura vertical os corta naquele x (O segmento é ativo se a coordenada x atual é >= a menor coordenada x do segmento e <= que a maior)
4. Ao inserir um segmento (chegamos em um x que inclui os limites do segmento), teste interseção com seus vizinhos (vizinhos da ordenação por y, ou seja, o ponto maior que o atual e o menor)
5. Ao remover um segmento, teste os dois vizinhos que ficaram juntos
6. Se algum teste detectar interseção retorne True

**Por que basta testar vizinhos?** Considere a interseção mais à esquerda. Imediatamente antes dela, os dois segmentos que se cruzam estão adjacentes na ordem por y (não há nada entre eles). Eles se tornaram adjacentes em algum evento anterior (inserção de um deles ou remoção de quem estava no meio), e nesse evento o teste entre eles foi feito.

A complexidade da abordagem ingenua é O(n^2) pois precisamos testar interseção de todos os segmentos com todos os outros. Essa abordagem, usando uma arvore binária balanceada por exemplo tem complexidade O(nlogn): ordenar as 2n extremidades custa O(nlogn) e cada uma das 2n inserções/remoções/consultas de vizinho custa O(logn).

Obs: esse algoritmo (Shamos-Hoey) só responde se **existe** alguma interseção. Para listar todas as k interseções existe o Bentley-Ottmann, com custo O((n + k) log n).

### **Determinar se um ponto está dentro de um polígono**

A ideia aqui é bem simples:

- Dado um ponto p, criamos uma reta apartir dele em qualquer direção
- Contamos quantas vezes essa reta cruza uma aresta do poligono:
    - par -> p esta fora
    - impar -> p esta dentro

Funciona porque cada vez que o raio cruza a borda ele alterna entre dentro e fora, e no infinito ele está fora.

A complexidade é O(n) pois temos que verificar intersecao com todas as arestas

Cuidado com os casos degenerados: o raio pode passar exatamente por um vértice (que seria contado duas vezes, uma para cada aresta) ou correr ao longo de uma aresta. A regra usual é contar uma aresta apenas se **um** de seus extremos estiver estritamente acima do raio.

## **Problema da envoltoria convexa**

Consiste em encontrar o menor polígono (estritamente) convexo (todos os angulos internos menores que π) que contenha um conjunto de pontos P. Dessa forma, se H é a envoltória convexa de P, todo ponto de P está dentro ou na borda de H. A saida é um subconjunto de P que são os vértices do resultado.

### **Varredura de Graham**

Algoritmo assume no minimo 3 pontos nos dados pois é minimo para se construir um poligono convexo e assumo que caso existe 3 ou mais pontos colineares apenas o mais distante é relevante. A ideia é:

1. Escolhemos um ponto ancora (inicial) que com certeza é **vértice** da envoltória, usamos o ponto de menor y (e menor x em caso de empate). Um ponto extremo como esse nunca pode estar no interior.
2. Agora tracando uma reta do ancora para todos os outros pontos, ordenados os pontos em relacao ao angulo gerado por essa reta (isso permite passar pelos pontos em sentido antihorario) e se dois pontos têm exatamente o mesmo ângulo (colineares) mantemos o mais distante. Não é preciso calcular ângulos com atan: o ponto a vem antes de b se orient(âncora, a, b) > 0.
3. Fazemos um scan nos pontos ordenados para descobrir quem pertence ou nao a envoltoria, para isso analisamos os 2 ultimos pontos adicionados na envoltoria (uma pilha) junto com o ponto atual ((p0,p1),p_curr):
    - Se p_curr esta a esquerda em relacao a p0p1, entao precisamos de p_curr e p1 na envoltoria e adicionamos p_curr na pilha
    - Se p_curr esta a direta (ou é colinear) em relacao a p0p1, entao n precisamos de p1 na envoltoria e damos pop() nele e repetimos o processo com os novos ultimos da pilha ate virar esquerda e adicionarmos p_curr
- Usamos uma pilha porque um ponto novo pode invalidar vários dos últimos pontos aceitos, sempre a partir do topo (o mais recente)

#### **Exemplo**

Pontos A(0,0), B(4,0), C(2,1), F(2,2), D(4,4), E(0,4):

1. Âncora = A (menor y)
2. Ordenando por ângulo: B (0°), C (≈27°), F e D (45°, colineares com A → mantém D, o mais distante), E (90°). Sequência: B, C, D, E
3. Varredura:

```text
pilha [A,B]
C: orient(A,B,C) = +4  esquerda → push        [A,B,C]
D: orient(B,C,D) = −8  direita  → pop C
   orient(A,B,D) = +16 esquerda → push        [A,B,D]
E: orient(B,D,E) = +16 esquerda → push        [A,B,D,E]
```

Envoltória: A, B, D, E. C saiu porque, ao chegar em D, a curva em C virou para a direita (C é uma "reentrância").

A complexidade do algoritmo é *O(n)* na varredura (amortizado: cada ponto entra e sai da pilha no máximo uma vez, então o total de push + pop é ≤ 2n) mas precisamos ordenar todos os nós pelo angulo entao é dominado por *O(nlogn)*

### **Algoritmo embrulho para presente (gift wrapping)**

Aqui a ideia é nao ordenar e pagar um preco definido pelo tamanho da envoltoria (saida), a ideia é:

1. Escolhe um ponto ancora igual o algoritmo anterior 
2. A partir do último vértice p adicionado na envoltória, buscamos o próximo vértice q tal que **todos os outros pontos fiquem à esquerda de p→q**. Para isso começamos com um candidato q qualquer e, para cada ponto r do conjunto, se r está à direita de p→q (orient(p, q, r) < 0), r passa a ser o candidato. No final da passada, q é o ponto "mais à direita" visto de p, e o adicionamos na envoltória.
3. Repetimos o passo 2 ate voltar ao ponto âncora

A complexidade do algoritmo depende de quantos pontos *h* existem na envoltoria e para cada um deles temos que fazer uma busca linear em todos os *n* pontos do conjunto, ou seja, complexidade total O(h*n). É um algoritmo **output-sensitive** (o custo depende do tamanho da saída): se h < log n ele é melhor que o Graham, mas se todos os pontos estiverem na envoltória (h = n) vira O(n^2).

### **Algoritmo Incremental**

Algoritmos bem simples que comeca com 3 pontos nao colineares do conjunto completo e vai adicionando:

1. Criamos o poligono convexo inicial com 3 pontos nao colineares
2. Para cada um dos outros n - 3 pontos, fazemos a pergunta: "o ponto esta dentro do poligono convexo atual?"
    - Se sim, nao precisamos atualizar a envoltoria
    - Se nao, precisamos atualizar a envoltoria
        - Para atualizar a envoltoria buscamos as 2 retas tangentes ao poligo atual que passam no novo ponto P a ser adicionado, depois é só conectar P aos 2 pontos da envoltoria que tocam nas tangentes. Mas o problema é como conseguir essas retas tangentes: testar se o ponto está dentro e achar as tangentes custa O(n) por ponto, então o algoritmo inteiro custa O(n^2)

### **Algoritmo incremental (v2) / Andrew (monotone chain)**

A ideia aqui é ao inves de buscar tangentes, ordenamos os pontos por X e agora sempre que chegar um ponto P novo sabemos que ele nao pode estar dentro da envoltoria atual pois o x dele é maior (ordenado). Mais que isso, ele é o ponto mais à direita até agora, então **sempre é vértice** da envoltória. Sabendo disso n precisamos mais fazer a pergunta de pertencimento.

Construímos separadamente a cadeia de cima e a cadeia de baixo da envoltória, cada uma com uma pilha igual à do Graham: chega o ponto novo, damos pop enquanto a curva estiver para o lado errado e depois push. Não é rodar o Graham de novo a cada ponto: é uma única passada pelos pontos ordenados.

A complexidade é O(n) na passada (cada ponto entra e sai de cada pilha no máximo uma vez) e **O(nlogn)** no total por causa da ordenação.

### **Algoritmo dividir e conquistar**

A ideia aqui é usar a estrategia dividir e conquistar para encontrar a envoltoria (hull):

1. Ordenados os pontos por x (isso permite dividir os pontos no espaco)
2. Dividimos recursivamente o espaco até chegar em conjuntos com no máximo 3 pontos pois achar o convex hull del 1, 2 e 3 pontos é trivial (sao eles mesmos)
3. Para juntas 2 convex hulls preciamos encontrar 2 retas tangentes a eles (elas representam as arestas que precisamos criar para pegar todos os pontos juntos)
    - Considerando apenas os pontos do hull, ou seja, sem considerar os internos, começamos com a reta que liga o ponto **mais à direita** do hull esquerdo ao ponto **mais à esquerda** do hull direito
    - Para a tangente superior, avançamos a extremidade direita para o seu **vizinho no hull direito** (no sentido que sobe) enquanto a reta subir, ou seja, enquanto o vizinho estiver acima da reta. Depois fazemos o mesmo com a extremidade esquerda no hull esquerdo. Alternamos entre os dois lados
    - Quando nenhuma das duas extremidades consegue mais se mover, todos os pontos estão abaixo da reta e chegamos a tangente
    - repetimos o mesmo processo para a tangente de baixo mas com objetivos opostos
    - Por fim, descartamos os vértices dos dois hulls que ficaram entre as tangentes (do lado de dentro)

O merge tem complexidade de O(n), pois cada extremidade só anda pelos vértices do seu hull. A recorrência é T(n) = 2T(n/2) + O(n): temos logn niveis na recursao e cada nível soma O(n) de merge, consequentemente a complexidade é O(nlogn)

## **Problema da galeria de arte**

Uma câmera consegue enxergar todos os pontos que possuem uma linha de visão direta até ela. As paredes bloqueiam essa visão. Se o polígono for convexo, uma única câmera basta, porque de qualquer ponto interno conseguimos enxergar todo o polígono. Já em um polígono não convexo, aparecem “quinas para dentro”, que podem esconder regiões.

O objetivo seria encontrar o menor número possível de câmeras. Só que calcular esse mínimo exatamente é um problema computacionalmente difícil. Por isso, a estratégia apresentada procura uma garantia do tipo: Independentemente do formato do polígono, com no máximo X câmeras certamente conseguimos vigiar tudo.

**Teorema da galeria de arte (Chvátal):** para um polígono simples com n vértices, ⌊n/3⌋ câmeras **sempre bastam** e **às vezes são necessárias** (ex: um polígono em forma de "pente", em que cada dente precisa de uma câmera própria). A prova abaixo (de Fisk) usa triangulação + coloração.

### **Triangulação de poligonos**

A primeira ideia é observar que um triângulo é muito fácil de vigiar. Uma câmera colocada adequadamente consegue enxergar o triângulo inteiro. Então podemos pegar um polígono complicado e dividi-lo em triângulos e essa divisão é chamada de triangulação.

- **Quantos triangulos ?** Todo polígono simples com n vértices pode ser triangulado em exatamente n − 2 triângulos mas o problema aqui é que saimos de n cameras no pior caso (uma por vértice), para n - 2 cameras, ou seja, um por triangulo... isso é pouco relevante.

- **Uma camera para N triangulos ?** A ideia então é ao invés de uma camera para cada triangulo, podemos  usar uma camera para varios triangulos afinal se o poligono formado por esses triangulos é convexo então uma camera sozinha da conta. Em vez de colocar uma câmera dentro de cada triângulo, colocar câmeras em alguns vértices do polígono. Queremos escolher vértices de maneira que todo triângulo da triangulação tenha pelo menos um vértice contendo câmera mas como fazer isso ? 

#### **Coloração de grafos**

A ideia é pintar os vértices com 3 cores de forma que **todo triângulo tenha exatamente um vértice de cada cor**. Se isso for possível, qualquer cor sozinha toca todos os triângulos, então colocar câmeras em todos os vértices de uma única cor já vigia o polígono inteiro. A ideia da coloração é:

1. Construimos o grafo G que são os vértices e arestas do poligono **mais as diagonais da triangulação**, e o grafo dual T(G) em que cada nó é um triangulo e ele tem aresta para outro triangulo se eles compartilharem uma **aresta (diagonal)**. Compartilhar só um vértice não conta.
2. O dual de uma triangulação de polígono simples é uma **árvore** (cada diagonal divide o polígono em dois). Usamos o grafo T para definir a ordem que colorimos o grafo G, usando um DFS no T: o primeiro triângulo recebe as 3 cores, e cada triângulo seguinte compartilha uma diagonal (2 vértices já pintados) com o anterior, então o terceiro vértice recebe a cor que sobrou. A escolha é forçada e nunca gera conflito.
3. Depois de todos os nós de G estarem coloridos, pegamos a cor que tem menos nós coloridos e colocamos nos nós dela as cameras

- No final vamos ter no máximo ⌊n/3⌋ cameras: como os n vértices estão divididos em 3 cores, pelo princípio da casa dos pombos a menor cor tem no máximo n/3 vértices. Isso é uma vantagem muito maior que n-2

#### **Como triangular os poligonos ?**

Para esse problema existe um algoritmo chamado corte de pontas de orelha. Um vértice Pi é uma **orelha** se o triângulo P(i-1), Pi, P(i+1) está inteiro dentro do polígono, ou seja, a diagonal P(i-1)P(i+1) pode ser traçada e "corta fora" a ponta Pi. O **teorema das duas orelhas** garante que todo polígono simples com n ≥ 4 vértices tem pelo menos 2 orelhas, então o algoritmo sempre encontra uma. Ele funciona assim:

1. Buscamos todas as orelhas do grafo passando de 3 em 3 nós. São n candidatos e cada teste precisa verificar se algum dos outros vértices está dentro do triângulo (O(n)), então isso tem custo O(n^2)
2. Enquanto o poligono tiver mais que 3 pontos (ultimo triangulo): O(n)

    1. Encontramos uma orelha do poligono atual e salvamos quem são os 3 pontos que fazem parte dela (um triangulo)
    2. Removemos o ponto que é a ponta da orelha e atualizamos os vizinhos dessa orelha pois podem ter se tornado uma orelha

Algortimo no final tem complexidade O(n^2): são n - 3 cortes e cada um revalida os 2 vizinhos em O(n). Existem soluções O(nlogn) mais sofisticadas (particionamento em polígonos monótonos).

- **Como indentificar orelhas ?** Assumindo os vértices do polígono em ordem anti-horária, Pi é orelha se as duas condições valem:
    - Pi é um vértice **convexo**: P(i+1) está à esquerda de P(i-1)→Pi, ou seja, orient(P(i-1), Pi, P(i+1)) > 0
    - **nenhum outro vértice** do polígono está dentro do triângulo P(i-1), Pi, P(i+1)

## **Range Search** 

O problema consiste em dado um conjunto de pontos multidimensionais (features de dados), quais pontos estão dentro desta região de consulta? Por exemplo, quais dos meus pontos tem idade entre [20,40] e salario [1000,3000].

### **1 Dimensão**

- A solução trivial é passe por todos os pontos e verifique as condições da query, isso tem complexidade linear O(n). 
- Uma solução mais robusta seria criar uma arvore binária balanceada com os pontos. Depois ao buscar por um intervalo [min,max] especifico temos que olhar todas as folhas maiores que min e menores que max, para isso podemos fazer duas consultas na arvore buscando exatamente os valores da borda e pegar pontos que estão em subarvores de interesse. Nessa arvore geralmente é interessante usar a mediana para fazer divisões balanceadas.

    -  A complexidade é O(logn + k) sendo logn pela busca na arvore balanceada e k o numero de pontos que existe no intervalo buscado
    -  Construção O(nlogn) e espaço O(n)

- Em 1D, um **vetor ordenado com busca binária** já dá o mesmo O(logn + k): busca binária para achar o primeiro ponto ≥ min e varre até passar de max. A árvore é interessante porque é a ideia que generaliza para mais dimensões (e aceita inserções/remoções).

### **K-dimensôes (KD-Tree)**

Em 1D, cada nó separava: menores e maiores 

Agora temos duas coordenadas. Então a kd-tree faz algo simples que é alternar a dimensão usada para dividir os pontos de acordo com o nivel da arvore, ou seja, a cada nivel dividimos o espaço em uma dimensão. 

Em 2D:

nível 0 → divide por x
nível 1 → divide por y
nível 2 → divide por x
nível 3 → divide por y

A ideia da kd-tree é que cada subarvore representa uma região do espaço, quando fazemos uma query estamos também buscando uma região e portanto existem 3 casos durante a busca:

- **Região de consulta é disjunta da região do nó:** não precisamos olhar essa subarvore
- **Região do nó esta dentro da região da consulta:** não precisamos explorar a arvore pois sabemos que tudo la dentro é importante e portanto só retornamos os pontos
- **Região do nó tem interseção com a região da consulta:** precisamos explorar a arvore pois nem tudo la é importante

A construição da arvore tem complexidade **O(nlogn)** pois para cada um dos logn niveis da arvore temos que processar todos os pontos n pontos para as novas divisões. A busca em 2D tem complexidade **O(√n + k)** e o espaço é O(n). Podemos generalizar para qualquer quantidade d de dimensões essa ideia pois ela sempre ira separar o espaço d-dimensional, mas a busca passa a custar **O(n^(1-1/d) + k)**, que se aproxima de O(n) conforme d cresce (maldição da dimensionalidade). Por isso KD-trees funcionam bem em poucas dimensões, mas para dados com muitas features perdem a vantagem sobre a busca linear.
 

# **Questões** 

## **Casamento de padões**

1. (Adaptado de Goodrich e Tamassia R-23.1) Seja P = aaabbaaa. 

    - Qual o tamanho do conjunto {k > 0 | Pk ⊐ P }? 
    
    k=1 -> "a"
    k=2 -> "aa"
    k=3 -> "aaa"
    k=4 -> ""
    k=5 -> ""
    ...
    k=8 -> "aaabbaaa"
    Tamanho = 4 que são os possiveis prefixos que são sufixos do padrão

    - Qual o de σ(ccaabbaa)? 

    O maior prefixo do padrão que é sufixo da string tem tamanho 2 e é "aa"

    - Compute a função de transição para do AFD para o casamento desse padrão

    (state, new_char) -> new_state

    Tabela da função sufixo será:

    (0,a) -> 1, (0,b) -> 0, (0, qualquer outro) -> 0
    (1,a) -> 2, (1,b) -> 0, (1, qualquer outro) -> 0
    (2,a) -> 3, (2,b) -> 0, (2, qualquer outro) -> 0
    (3,a) -> 3, (3,b) -> 4, (3, qualquer outro) -> 0
    (4,a) -> 1, (4,b) -> 5, (4, qualquer outro) -> 0
    (5,a) -> 6, (5,b) -> 0, (5, qualquer outro) -> 0
    (6,a) -> 7, (6,b) -> 0, (6, qualquer outro) -> 0
    (7,a) -> 8, (7,b) -> 0, (7, qualquer outro) -> 0
    (8,a) -> 3, (8,b) -> 4, (8, qualquer outro) -> 0

2. Construa o autômato que represente o padrão 0110200100 e mostre o funcionamento do algoritmo para casamento de padrões baseado em AFD com o texto 00202320101302001000020232010110200100.

    Automato: 
    
    (0,0) -> 1, (0,1) -> 0, (0,2) -> 0, (0,3) -> 0
    (1,0) -> 1, (1,1) -> 2, (1,2) -> 0, (1,3) -> 0
    (2,0) -> 1, (2,1) -> 3, (2,2) -> 0, (2,3) -> 0
    (3,0) -> 4, (3,1) -> 0, (3,2) -> 0, (3,3) -> 0
    (4,0) -> 1, (4,1) -> 2, (4,2) -> 5, (4,3) -> 0
    (5,0) -> 6, (5,1) -> 0, (5,2) -> 0, (5,3) -> 0
    (6,0) -> 7, (6,1) -> 2, (6,2) -> 0, (6,3) -> 0
    (7,0) -> 1, (7,1) -> 8, (7,2) -> 0, (7,3) -> 0
    (8,0) -> 9, (8,1) -> 3, (8,2) -> 0, (8,3) -> 0
    (9,0) -> 10, (9,1) -> 2, (9,2) -> 0, (9,3) -> 0
    (10,0) -> 1, (10,1) -> 2, (10,2) -> 0, (10,3) -> 0

    Executando no texto: 00202320101302001000020232010110200100

    Começamos no estado 0.

    índice 0:  lê 0 -> (0,0) -> 1
    índice 1:  lê 0 -> (1,0) -> 1
    índice 2:  lê 2 -> (1,2) -> 0
    índice 3:  lê 0 -> (0,0) -> 1
    índice 4:  lê 2 -> (1,2) -> 0
    índice 5:  lê 3 -> (0,3) -> 0
    índice 6:  lê 2 -> (0,2) -> 0
    índice 7:  lê 0 -> (0,0) -> 1
    índice 8:  lê 1 -> (1,1) -> 2
    índice 9:  lê 0 -> (2,0) -> 1
    índice 10: lê 1 -> (1,1) -> 2
    índice 11: lê 3 -> (2,3) -> 0
    índice 12: lê 0 -> (0,0) -> 1
    índice 13: lê 2 -> (1,2) -> 0
    índice 14: lê 0 -> (0,0) -> 1
    índice 15: lê 0 -> (1,0) -> 1
    índice 16: lê 1 -> (1,1) -> 2
    índice 17: lê 0 -> (2,0) -> 1
    índice 18: lê 0 -> (1,0) -> 1
    índice 19: lê 0 -> (1,0) -> 1
    índice 20: lê 0 -> (1,0) -> 1
    índice 21: lê 2 -> (1,2) -> 0
    índice 22: lê 0 -> (0,0) -> 1
    índice 23: lê 2 -> (1,2) -> 0
    índice 24: lê 3 -> (0,3) -> 0
    índice 25: lê 2 -> (0,2) -> 0
    índice 26: lê 0 -> (0,0) -> 1
    índice 27: lê 1 -> (1,1) -> 2
    índice 28: lê 0 -> (2,0) -> 1
    índice 29: lê 1 -> (1,1) -> 2
    índice 30: lê 1 -> (2,1) -> 3
    índice 31: lê 0 -> (3,0) -> 4
    índice 32: lê 2 -> (4,2) -> 5
    índice 33: lê 0 -> (5,0) -> 6
    índice 34: lê 0 -> (6,0) -> 7
    índice 35: lê 1 -> (7,1) -> 8
    índice 36: lê 0 -> (8,0) -> 9
    índice 37: lê 0 -> (9,0) -> 10

    Ao chegar no estado 10, o padrão foi encontrado.

3. (CLRS 32.3-5) Suponha que seja permitida a utilização de curingas (símbolos que casam com qualquer outro)
na construção do padrão. Mostre como adaptar o algoritmo baseado em autômatos para continuar a encontrar
o padrão em tempo O(n).

    Podemos adaptar a função de transição do AFD considerando que o curinga casa com qualquer símbolo do alfabeto. Assim, se estamos no estado q e o próximo símbolo do padrão P[q+1] é um curinga, então, para qualquer caractere c do texto:

    δ(q,c) = q + 1

    Ou seja, independentemente do caractere recebido, avançamos para o
    próximo estado. Para os demais estados, a função de transição é construída normalmente,
    procurando o maior prefixo do padrão que casa com um sufixo da sequência
    processada, levando em consideração que um curinga é compatível com
    qualquer caractere. Portanto, para um texto de tamanho n, o casamento continua sendo feito
    em tempo O(n).

4. Compute a função prefixo (π(q)) para o padrão cgtacgttcgtac.

    (tamanho dq ja casei) -> (tamanho do maior prefixo menor dq ja casei que é sufixo dq ja casei)

    (0) -> (0)
    (1) -> (0)
    (2) -> (0)
    (3) -> (0)
    (4) -> (0)
    (5) -> (1)
    (6) -> (2)
    (7) -> (3)
    (8) -> (0)
    (9) -> (1)
    (10) -> (2)
    (11) -> (3)
    (12) -> (4)
    (13) -> (5)

5. (Goodrich e Tamassia R-23.5) Compute a função shift do algoritmo de Horspool para o padrão “the quick brown fox jumped over a lazy cat”

    (char) -> (distancia da ultima aparicao char no padrao ate o ultimo indice)

    (a) -> 1
    (c) -> 2
    ( ) -> 3
    (y) -> 4
    (z) -> 5
    (l) -> 7
    (r) -> 11
    (e) -> 12
    (v) -> 13
    (o) -> 14
    (d) -> 16
    (p) -> 18
    (m) -> 19
    (u) -> 20
    (j) -> 21
    (x) -> 23
    (f) -> 25
    (n) -> 27
    (w) -> 28
    (b) -> 31
    (k) -> 33
    (i) -> 35
    (q) -> 37
    (h) -> 40
    (t) -> 41 (Nao consideramos o char do ultimo indice pois se nao vira loop infinito)

    (qualquer outro caractere) -> 42 (padrao todo)

6. (Levitin 7.3-3) Quantas comparações serão realizadas pelo algoritmo de Horspool para encontrar os padrões a
seguir no texto 0^(1000).

(a) 00001

    aproximadamente 1000 comparacoes, ou seja, linear no texto pois ficara dando saltos de 1 em 1 devido a errar ja sempre na primeira 

(b) 10000

    aproximadamente 1000*5 pois é o pior caso n*m em que casamos quase todo o padrao, erramos no final e deslocamos um indice

(c) 01010

    aproximadamente 1000 pois esta sempre comparando 2 mas pulando 2 também

7. Aponte qual a melhor escolha de algoritmo para casamento de padrões em texto considerando cada situação (entre kmp, horspool, shift-and): 

    - (a) Deseja-se verificar se algum código malicioso contido em uma lista pré-definida ocorre em um arquivo recebido por email. 

    Shift-and aqui nao é uma boa pois esses codigos maliciosos costumam ser longos e isso quebra as vantagens dele. Como esses padroes sao longos e o alfabeto grande o horspool pode ser uma otima ideia pois os pulos sao maiores e o grande alfabeto aumenta chance de erros.
    
    - (b) Deseja-se filtrar posts no Twitter que possuam uma palavra-chave definida pelo usuário.

    Palavras chaves sao pequenas, ou seja, o padrao é muito pequeno e isso é um problema para o horspool pois ele se da melhor com padroes grandes (pulos grandes) e alfabetos grandes. Como a palavra é pequena o Shift-and tem vantagem pois é menor que uma palavra do processador e portanto vai ser muito eficiente, além disso ele pode facilmente virar uma busca aproximada sem mudar completamente o algortimo. Essas sao as unicas vantagens dele sobre o KMP nesse cenário.

8. (Goodrich e Tamassia R-23.8) Mostre o resultado da inserção das seguintes palavras numa trie padrão: abab, baba, ccccc, bbaaaa, caa, bbaacc, cbcc, cbca.

    1. root -> abab
    2. root -> a -> b -> a -> b
        |-> b -> a - > b -> a
    3. root -> a -> b -> a -> b
        |-> baba
        |-> ccccc
    4. root -> a -> b -> a -> b
        |-> b -> aba
        |   |-> baaaa
        |-> ccccc
    5. root -> a -> b -> a -> b
        |-> b -> aba
        |   |-> baaaa
        |-> c -> cccc
        |   |-> aa
    6. root -> a -> b -> a -> b
        |-> b -> aba
        |   |-> baa -> aa
        |       |-> cc
        |  
        |-> c -> cccc
        |   |-> aa
    7. root -> a -> b -> a -> b
        |-> b -> aba
        |   |-> baa -> aa
        |       |-> cc
        |  
        |-> c -> cccc
        |   |-> aa
        |   |-> bcc
    8. root -> a -> b -> a -> b*
        |-> b -> a -> b -> a*
        |   |-> b -> a -> a -> a -> a*
        |       |-> c -> c*
        |  
        |-> c -> c -> c -> c -> c*
        |   |-> a -> a*
        |   |-> b -> c -> c*
        |   |   |-> a*

9. (Goodrich e Tamassia R-23.9) Mostre o resultado das inserções das chaves do exercício R-23.8 numa trie compacta (radix tree).

    1. root -> abab
    2. root -> abab
        |-> baba
    3. root -> abab
        |-> baba
        |-> ccccc
    4. root -> abab
        |-> b -> aba
        |   |-> baaaa
        |-> ccccc*
    5. root -> abab*
        |-> b -> aba*
        |   |-> baaaa*
        |-> c -> cccc*
        |   |-> aa*
    6. root -> abab*
        |-> b -> aba*
        |   |-> baa -> aa*
        |       |-> cc*
        |  
        |-> c -> cccc*
        |   |-> aa*
    7. root -> abab*
        |-> b -> aba*
        |   |-> baa -> aa*
        |       |-> cc*
        |  
        |-> c -> cccc*
        |   |-> aa*
        |   |-> bcc*
    8. root -> abab*
        |-> b -> aba*
        |   |-> baa -> aa*
        |       |-> cc*
        |  
        |-> c -> cccc*
        |   |-> aa*
        |   |-> bc -> c*
        |   |   |-> a*
    
10. O maior argumento para a utilização da representação compacta em árvores de sufixo (radix tree) é porque o pior caso para tries tradicionais requer espaço O(m2). Dê um exemplo desse pior caso e justifique porque ele requer espaço quadrático.

    Considere, por exemplo:

    S = abcdef

    Seus sufixos são:

    abcdef
    bcdef
    cdef
    def
    ef
    f

    Em uma trie tradicional, cada sufixo precisa armazenar praticamente
    um caminho próprio, pois eles não possuem prefixos comuns significativos.

    Assim, o número total de caracteres armazenados é:

    m + (m-1) + (m-2) + ... + 1

    Essa soma é:

    m(m+1)/2

    Logo, o espaço utilizado é O(m²).
    Na radix tree, sequências de nós com apenas um filho são compactadas em uma única aresta, reduzindo o espaço para O(m)

11. como garantir que somente as folhas armazenem sufixos do padrão em uma árvore de sufixos? Justifique.

    um símbolo terminal especial ao final da string, por exemplo `$`, sendo esse símbolo diferente de todos os símbolos do alfabeto.

    Exemplo:

    S = banana
    S' = banana$

    Como `$` ocorre somente no final da string, nenhum sufixo de `S'` pode ser prefixo de outro sufixo.
    Dessa forma, cada sufixo termina obrigatoriamente em uma folha distinta da árvore de sufixos e impede que um sufico termine em um nó interno

12. sejam S1 e S2 duas strings arbitrárias. Um problema bastante comum envolvendo duas strings é determinar qual a maior substring compartilhada por ambas. Por exemplo, supondo que S1 = aauladealgoritmosefacil e S2 = osambaeoritmodocarnaval a maior substring compartilhada por ambas é oritmo. Na década de 1970, conjecturou-se que esse problema não possuía solução linear. Posteriormente, com o conhecimento das árvores de sufixo, desenvolveu-se um algoritmo linear (no tamanho total das strings) para encontrar a maior substring comum às duas. Descreva uma solução que resolva tal problema com a complexidade proposta.

    Existem algoritmos de construção linear de árvores de sufixos. Assumindo um deles, sabemos que a maior substring comum entre S1 e S2 pode ser vista como o maior prefixo comum entre algum sufixo de S1 e algum sufixo de S2. Isso ocorre porque toda substring de uma string é prefixo de algum de seus sufixos. Assim, podemos construir uma árvore de sufixos generalizada contendo os sufixos de S1 e S2, utilizando terminadores distintos, como `$` e `#`, para identificar corretamente a origem de cada sufixo.
    Em seguida, fazemos uma DFS na árvore. Para cada nó, verificamos se sua subárvore contém pelo menos uma folha originada de S1 e pelo menos uma folha originada de S2. O caminho da raiz até um nó com essa propriedade representa uma substring comum às duas strings. Mantemos o nó de maior profundidade que satisfaça essa condição. O caminho da raiz até esse nó corresponde à maior substring comum. A construção da árvore custa O(|S1| + |S2|), e a DFS também custa O(|S1| + |S2|). Portanto, o custo total é O(|S1| + |S2|).


13. (Adaptado de Goodrich e Tamassia R-23.11) Construa a árvore de sufixos para cgtacgttcgtacg. Qual a maior substring que se repete no texto?

    1. cgtacgttcgtacg
    2. root -> cgtacgttcgtacg
        L-> gtacgttcgtacg
    3. root -> cgtacgttcgtacg
        L-> gtacgttcgtacg
        L-> tacgttcgtacg
    4. root -> cgtacgttcgtacg
        L-> gtacgttcgtacg
        L-> tacgttcgtacg
        L-> acgttcgtacg
    5. root -> cgt -> acgttcgtacg
                L->tcgtacg
        L-> gtacgttcgtacg
        L-> tacgttcgtacg
        L-> acgttcgtacg
    6. root -> cgt -> acgttcgtacg
                L->tcgtacg
        L-> gt -> acgttcgtacg
            L-> tcgtacg
        L-> tacgttcgtacg
        L-> acgttcgtacg
    7. root -> cgt -> acgttcgtacg
                L->tcgtacg
        L-> gt -> acgttcgtacg
            L-> tcgtacg
        L-> t -> acgttcgtacg
            L-> tcgtacg
        L-> acgttcgtacg
    8. root -> cgt -> acgttcgtacg
                L-> tcgtacg
        L-> gt -> acgttcgtacg
            L-> tcgtacg
        L-> t -> acgttcgtacg
            L-> tcgtacg
            L-> cgtacg
        L-> acgttcgtacg
    9. root -> cgt -> acgttcgtacg
                L-> tcgtacg
                L-> acg
        L-> gt -> acgttcgtacg
            L-> tcgtacg
        L-> t -> acgttcgtacg
            L-> tcgtacg
            L-> cgtacg
        L-> acgttcgtacg
    10. root -> cgt -> acgttcgtacg
                L-> tcgtacg
                L-> acg
        L-> gt -> acgttcgtacg
            L-> tcgtacg
            L-> acg
        L-> t -> acgttcgtacg
            L-> tcgtacg
            L-> cgtacg
        L-> acgttcgtacg
    11. root -> cgt -> acgttcgtacg
                L-> tcgtacg
                L-> acg
        L-> gt -> acgttcgtacg
            L-> tcgtacg
            L-> acg
        L-> t -> acg -> ttcgtacg
                  L-> $
            L-> tcgtacg
            L-> cgtacg
        L-> acgttcgtacg
    12. root -> cgt -> acgttcgtacg
                L-> tcgtacg
                L-> acg
        L-> gt -> acgttcgtacg
            L-> tcgtacg
            L-> acg
        L-> t -> acg -> ttcgtacg
                  L-> $
            L-> tcgtacg
            L-> cgtacg
        L-> acg -> ttcgtacg
            L-> $
    13. root -> cg 
                L-> $
                L-> t -> acgttcgtacg
                    L-> tcgtacg
                    L-> acg
        L-> gt -> acgttcgtacg
            L-> tcgtacg
            L-> acg
        L-> t -> acg -> ttcgtacg
                  L-> $
            L-> tcgtacg
            L-> cgtacg
        L-> acg -> ttcgtacg
            L-> $
    14. root
        ├── cg
        │   ├── $
        │   └── t
        │       ├── acgttcgtacg$
        │       ├── tcgtacg$
        │       └── acg$
        │
        ├── g
        │   ├── $
        │   └── t
        │       ├── acgttcgtacg$
        │       ├── tcgtacg$
        │       └── acg$
        │
        ├── t
        │   ├── acg
        │   │   ├── $
        │   │   └── ttcgtacg$
        │   ├── tcgtacg$
        │   └── cgtacg$
        │
        └── acg
            ├── $
            └── ttcgtacg$

## **Geometria Computacional**

1. (Goodrich e Tamassia C-22.1) Descreva um algoritmo inTriangle(p, q, r, s) que teste em tempo O(1) se um ponto p está dentro do triângulo formado pelos pontos (q, r, s), onde q, r, e s são dados em ordem anti-horária.

    Como os pontos q, r e s são dados em ordem anti-horária, o interior do triângulo está à esquerda de todas as arestas orientadas q→r, r→s e s→q. Para cada uma dessas arestas, calculamos a orientação do ponto p usando o produto vetorial (determinante) e 
    se orient(a,b,p) > 0, p está à esquerda da reta orientada a→b; se for menor que 0, está à direita.

    Assim, p está dentro do triângulo se:

    orient(q,r,p) >= 0,
    orient(r,s,p) >= 0,
    orient(s,q,p) >= 0.

    Como são realizados apenas três testes de orientação, cada um em tempo constante, o algoritmo executa em O(1).

2. (Adaptado de Goodrich e Tamassia C-22.2) Usando a função desenvolvida no exercício anterior, descreva um algoritmo que responda em tempo O(log n) se um ponto p está dentro de um polígono convexo com n vértices. Dica: use uma estrutura de dados para apoiar as consultas. O tempo de construção da estrutura não é computado na consulta.

    Escolhemos um vértice fixo v0 do polígono convexo e armazenamos os demais vértices em ordem anti-horária em um vetor. Os segmentos de v0 até os demais vértices dividem o polígono em um conjunto ordenado de triângulos (v0, vi, vi+1). Para consultar um ponto p, fazemos uma busca binária no vetor de vértices. Em cada passo, usamos o teste de orientação entre v0, vi e p para decidir em qual lado angular continuar a busca. Ao final, encontramos dois vértices consecutivos vi e vi+1 tais que p está entre os raios v0→vi e v0→vi+1.

    Então aplicamos a função:

    inTriangle(p, v0, vi, vi+1)

    para verificar em O(1) se p realmente pertence ao triângulo correspondente. A busca binária custa O(log n) e o teste final custa O(1), logo a consulta total custa O(log n). O pré-processamento consiste em armazenar os vértices em ordem angular; caso seja necessário ordená-los, esse passo pode custar O(n log n), mas não é considerado no custo da consulta.

3. Sejam p1 = (x1, y1) e p2 = (x2, y2) dois pontos no plano. Diz-se que o ponto p2 domina o ponto p1 se x1 ≤ x2 e y1 ≤ y2. Dado um conjunto de pontos P = {p1, p2, . . . , pn} no plano, um ponto pi ∈ P é chamado de maximal se ele não for dominado por nenhum outro ponto de P . Prove que, se (xi1 , yi1 ), (xi2 , yi2 ), . . . , (xik , yik ) for uma lista de pontos maximais tais que xiq < xiq+1 para todo q, então yiq > yiq+1 .

    Considere dois pontos consecutivos da lista de maximais, piq e piq+1, tais que xi_q < xi_q+1. Suponha, por contradição, que yi_q <= yi_q+1.
    Como xi_q < xi_q+1, também temos xi_q <= xi_q+1. Portanto, xi_q <= xi_q+1 e yi_q <= yi_q+1.
    Pela definição de dominância, isso implica que pi_q+1 domina pi_q. Entretanto, pi_q é maximal e, portanto, não pode ser dominado por nenhum outro ponto de P. Temos uma contradição.
    Logo, necessariamente: yi_q > yi_q+1.

4. (Adaptado de Jeff Erickson CS498 UIUC) Como professor da disciplina de Alg2, você pediu aos alunos que implementassem um algoritmo de envoltória convexa. Agora que os alunos entregaram os TPs você quer escrever um corretor automático para os trabalhos. A primeira parte consiste em verificar se a saída do programa, uma lista de pares (x, y) representando as coordenadas dos pontos da envoltória, está ordenada no sentido anti-horário. Descreva um algoritmo que faça essa verificação em tempo O(n) (n é o total de pontos na saída). A figura abaixo mostra possíveis saídas de TPs e a resposta do seu programa.

    Percorremos a lista de pontos da envoltória considerando cada trio consecutivo (pi, pi+1, pi+2). Para cada trio, calculamos sua orientação usando o determinante. Como a envoltória deve estar em sentido anti-horário, todo terceiro ponto deve estar à esquerda da reta orientada formada pelos dois anteriores.

    Assim, para todo i, verificamos se: orient(pi, pi+1, pi+2) > 0.

    Os índices são considerados módulo n para também verificar os trios que envolvem o final e o início da lista. Se algum teste resultar em orientação à direita, a lista não está em ordem anti-horária. Caso todos os testes sejam válidos, a ordem está correta. Como realizamos n testes, cada um em O(1), o algoritmo executa em O(n).

5. (CLRS 33.2-4) Descreva um algoritmo O(n log n) que verifique se um polígono é simples (não necessariamente convexo). Assuma que o polígono não possua buracos

    Representamos as n arestas do polígono como segmentos e aplicamos uma varredura vertical da esquerda para a direita. Inicialmente, ordenamos os extremos dos segmentos por coordenada x, o que custa O(n log n).
    Durante a varredura, mantemos em uma árvore binária balanceada os segmentos que intersectam a reta de varredura, ordenados pela coordenada y.

    Quando um segmento é inserido, verificamos interseção apenas com seus vizinhos imediatamente acima e abaixo. Quando um segmento é removido, verificamos se os dois segmentos que passam a ser vizinhos se intersectam. Caso seja encontrada uma interseção entre duas arestas não adjacentes do polígono, o polígono não é simples. Interseções entre arestas consecutivas em seus vértices comuns são permitidas. Como existem O(n) eventos e cada operação na árvore custa O(log n), o algoritmo executa em O(n log n).

6. (CRLS 33.3-3) Prove que os pontos mais distantes de um conjunto P devem ser vértices da envoltória convexa.

    Sejam p e q os dois pontos mais distantes do conjunto P. Suponha, por contradição, que p não seja um vértice da envoltória convexa. Então p está no interior da envoltória, ou sobre uma aresta entre outros vértices. Considere a reta que passa por q e p. Prolongando essa reta a partir de p na direção oposta a q, encontramos a fronteira da envoltória em algum ponto r.

    Como p está entre q e r, temos: d(q,r) > d(q,p).
    
    Isso contradiz a hipótese de que p e q formam o par de pontos mais distantes de P. Portanto, p deve ser um vértice da envoltória convexa. O mesmo argumento vale para q. Logo, os pontos mais distantes de P devem ser vértices da envoltória convexa.

7. Crie um algoritmo O(n) que transforme um polígono não-convexo em convexo.

   Percorremos os vértices do polígono em ordem utilizando uma pilha. Para cada novo vértice p, verificamos a orientação formada pelos dois últimos vértices da pilha e por p. Se os três pontos mantêm a orientação correspondente a uma curva convexa, inserimos p na pilha. Caso formem uma curva para o interior do polígono, ou sejam colineares caso desejemos remover pontos redundantes, removemos o vértice do meio e repetimos o teste com os novos dois últimos vértices da pilha. Assim, vértices que formam reentrâncias são removidos e os vizinhos passam a ser ligados diretamente. Cada vértice é inserido na pilha uma vez e removido no máximo uma vez. Portanto, o custo total é O(n).

8. Construa a kd usandos os seguintes pontos: P={(2,3),(5,4),(9,6),(4,7),(8,1),(7,2),(1,5),(6,8)} e depois consultas para Q1​=[3,7]×[2,6] e Q2​=[1,5]×[4,8]

    arvore binária kd-tree:

    x ordenado: ((1,5),(2,3),(4,7),(5,4),(6,8),(7,2),(8,1),(9,6)) -> mediana = 5.5
    nivel 0:        (5.5)

    y's ordenados:  ((2,3),(5,4),(1,5),(4,7)) -> 4.5    ((8,1),(7,2),(9,6),(6,8)) -> 4
    nivel 1:                (4.5)                                    (4)

    x's ordenados:  ((2,3),(5,4)) -> 3.5  ((1,5),(4,7)) -> 2.5     ((7,2),(8,1)) -> 7.5  ((6,8),(9,6)) -> 7.5
    nivel 2:              (3.5)                 (2.5)                     (7.5)               (7.5)

    y...:        ((2,3))  ((5,4))       ((1,5))  ((4,7))              ((7,2))  ((8,1))      ((6,8)) ((9,6))
    nivel 3:   FIM

    Q1​=[3,7]×[2,6]:

    nivel 0: 
    - 5.5 intercepta [3,7] portanto temos que explorar ambos filhos

    nivel 1: 
    - 4.5 e 4 interceptam [2,6] portanto temos que explorar os 2 filhos nos 2 nós
    
    nivel 2: 
    - 3,5 está dentro de [3,7] -> explora os dois filhos.
    - 2.5 < 3 -> a consulta está toda à direita desse corte -> explora só o filho direito.
    - 7.5 > 7 -> a consulta está toda à esquerda desse corte -> explora só o filho esquerdo.
    - 7.5 > 7 -> de novo, só o filho esquerdo

    candidatos: (2,3),(5,4),(4,7),(7,2),(6,8) que vieram de regioes que tem intersecao 
    


