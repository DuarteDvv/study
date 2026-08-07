# **Computadores paralelos**

## **Por que paralelismo?**

O ganho automático de desempenho vindo de aumentar a frequência e explorar paralelismo interno (ILP — Instruction Level Parallelism) esbarrou em limites físicos: *consumo de energia e dissipação de calor*. A resposta da indústria foi adicionar múltiplos núcleos mais simples (e mais frios) em vez de um único núcleo cada vez mais complexo.

## **Evolução dos processadores**

### **Processador simples (escalar)**

Uma instrução avança por ciclo, num fluxo rígido: busca → decodifica → executa (ALU) → grava resultado. Qualquer dependência entre instruções ou acesso lento à memória trava esse fluxo único, gerando ciclos de espera. 

### **Processador superescalar**

Executa duas ou mais instruções por ciclo, desde que sejam independentes, explora ILP. O hardware (ou o compilador) identifica dependências e reordena instruções mantendo a correção do programa, preenchendo unidades de execução paralelas sem que o programador escreva código explicitamente paralelo.

### **Multi-core**

Os transistores extras passam a criar *vários núcleos mais simples no mesmo chip*, em vez de um núcleo mais complexo. Cada núcleo individual é mais lento, mas o ganho total supera a perda individual, dois núcleos ~25% mais lentos cada entregam, juntos, ~1,5× o desempenho de um núcleo sozinho. Entretanto, o software precisa expor paralelismo explicitamente (múltiplos threads) para que os núcleos extras sejam de fato usados.

## **Camadas de paralelismo**

### **Paralelismo de instrucao**

Explora a possibilidade de executar múltiplas instruções de um mesmo fluxo simultaneamente, desde que sejam independentes entre si (uma não depende do resultado da outra). Para permitir isso usa pipelining que divide a execução de uma instrução em estágios (busca, decodifica, executa, grava), permitindo que instruções diferentes estejam em estágios diferentes ao mesmo tempo, como uma linha de montagem.

### **Paralelismo de dados (vetorizacao)**

Explora a repetição da mesma operação sobre múltiplos elementos de dados, usando várias ALUs controladas por uma única instrução. Para isso usam registradores vetoriais largos (ex: SSE, AVX, AVX-512 em CPUs) que armazenam vários valores numa única instrução de load/store/operação.

### **Paralelismo de processos/threads**

Explora fluxos de execução completamente independentes rodando ao mesmo tempo, em núcleos diferentes (ou máquinas diferentes, no caso de processos distribuídos). Geralmente é implementado através de:
- **Multicore:** vários núcleos físicos no mesmo chip, cada um com sua própria lógica de busca/decodificação/execução, não precisam de coerência entre si; cada núcleo pode seguir ramificações, loops e até programas totalmente diferentes.
- **Threads:** unidades de execução leves dentro de um mesmo processo, compartilhando memória; o programador (ou runtime/biblioteca) precisa criar, distribuir e sincronizar esse trabalho manualmente.
- **Processos distribuídos (fora de um único chip):** paralelismo em nível de tarefa, entre máquinas diferentes num cluster.

#### **Processo vs Thread**

- **Processo:** Um processo é uma instância de um programa em execução, junto com todos os recursos que o sistema operacional aloca para ele: espaço de memória próprio (endereçamento virtual isolado), arquivos abertos, descritores, e pelo menos uma thread de execução. Todo processo nasce com uma thread principal, não existe processo sem nenhuma thread, mas um processo pode criar mais threads durante sua execução.

- **Thread:** Uma thread é um fluxo de instruções independente dentro de um processo, sua própria sequência de execução, com seu próprio contador de programa (program counter), sua própria pilha (stack) e seu próprio conjunto de registradores. É a unidade que o sistema operacional efetivamente escalona (coloca na fila do processador) para rodar num núcleo do processador.

#### **Paralelismo real e falso**

Nem toda "simultaneidade" que o sistema operacional mostra é paralelismo de verdade:

- **Paralelismo real:** acontece quando threads/processos diferentes rodam em núcleos físicos diferentes ao mesmo tempo. Cada um tem seu próprio hardware de execução completo (ALUs, registradores, cache), então nenhum precisa esperar o outro.

- **Paralelismo falso:** quando há mais threads prontas do que núcleos disponíveis, o sistema operacional revela por fatias de tempo curtas, cada thread roda um pouco, é pausada (context switch), outra assume o núcleo. Do ponto de vista do usuário parece simultâneo, mas na verdade é uma alternância muito rápida, uma thread por vez em cada núcleo.

Ter 100 threads criadas num sistema com 4 núcleos não significa 100 execuções simultâneas, significa 4 execuções realmente paralelas a qualquer instante, e as demais 96 na fila, sendo intercaladas via escalonamento. O paralelismo real está limitado pela quantidade de núcleos (físicos, ou lógicos com hyperthreading); tudo além disso é concorrência gerenciada pelo escalonador do SO, não paralelismo de fato.

#### **SMT (Simultaneous Multithreading)** 

Faz um único núcleo físico se apresentar ao sistema operacional como dois (ou mais) núcleos lógicos. Apenas é duplicado o estado de cada thread, registradores, contador de programa. Já as unidades de execução reais (ALUs, unidades de ponto flutuante), o cache do núcleo, a largura de banda de memória. As duas threads lógicas competem pelos mesmos recursos físicos de cálculo.

Um núcleo superescalar frequentemente *não encontra instruções independentes suficientes numa única thread* para preencher todas as suas unidades de execução a cada ciclo, sobram "bolhas" no pipeline (esperando cache miss, dependência de dados, previsão de desvio errada). O hyperthreading *preenche esses ciclos ociosos com trabalho de uma segunda thread*, que está pronta para executar enquanto a primeira espera.

## **GPUs e CUDA**
 
### **Origem e motivação**
 
GPUs surgiram como aceleradoras gráficas, aplicando a **mesma computação sobre muitos elementos** (vértices, pixels), um padrão naturalmente de paralelismo de dados... depois chegou em algebra e calculos matriciais pesados. 
 
CPU é otimizada para **latência**; GPU é otimizada para **vazão (throughput)**.
 
- **CPU:** foca em terminar **uma thread** o mais rápido possível, caches grandes e execução fora de ordem
- **GPU:** foca em terminar **o máximo de trabalho por segundo**, não uma thread específica rápido, mantém milhares de threads disponíveis; quando uma trava esperando memória, troca instantaneamente para outra pronta, mantendo as ALUs sempre ocupadas.


### **Modelo CUDA: host x device**
 
- **Host** (CPU): aloca memória, copia dados entre host/device, lança kernels.
- **Device** (GPU): executa **kernels** (`__global__`), em modo SPMD, por um grande número de **threads CUDA**.
- O número de blocos e de threads por bloco é definido **explicitamente pelo programador**, não inferido do tamanho dos dados.

### **Hierarquia de threads**
 
Dois níveis: **threads** dentro de **blocos**.

- Threads do mesmo bloco **cooperam** entre si.
- Blocos são **independentes**, podem rodar em qualquer ordem, isso permite escalar o mesmo código para GPUs com números diferentes de núcleos, sem mudar o programa.

### **Hierarquia de memória**
 
| Tipo | Visível para | Latência/Banda |
|---|---|---|
| Global | Todos os threads | Alta latência, alta banda |
| Compartilhada | Threads de um mesmo bloco | Baixa latência (no chip) |
| Privada | Só a própria thread | Registradores/local |
 
Host e device têm **espaços de endereço separados**, ponteiros do device não são acessíveis pelo host diretamente; comunicação é explícita via `cudaMemcpy` (parecido com passagem de mensagens).
 
### **Sincronização**
 
- `__syncthreads()`: barreira **dentro** de um bloco.
- Operações atômicas: exclusão mútua em dados compartilhados.
- Sincronização implícita host-device ao fim do kernel.
- **Não existe barreira global entre blocos**, dependências entre blocos exigem múltiplos lançamentos de kernel ou atomics.

### **Warps e SIMT**
 
A unidade real de execução é o **warp** (32 threads), rodando em **SIMT**: uma instrução aplicada a todos os threads ativos do warp ao mesmo tempo. Divergência (ex: `if` que varia entre threads) serializa a execução, perdendo eficiência, parecido com vetorização explícita, mas a decisão de agrupar em SIMD é feita **dinamicamente pelo hardware**, não pelo compilador.
 


