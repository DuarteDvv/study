# **Interconexão de redes (internet'working)**

O problema central abordado aqui é que nenhuma tecnologia de rede local (LAN) consegue cobrir uma quantidade de nodes em escala global. Um cabo Ethernet sozinho mal suporta 1024 conexões (nodes) pois a chance de colisão é muita alta, wifi é limitado por distancia e link ponto a ponto envolve apenas 2 nodes.

A ideia então é ter varias subredes locais (LANs) diferentes uma da outra e conectar elas ... isso é chamado de internet. 
Primeiro passo é conectar de maneira eficiente maquinas proximas (mesma LAN) que compartilham o mesmo tipo de link (tipos são iguais se o protocolo de enlace é igual)... isso é alcançado por meio de **switches** que recebem pacotes em uma porta e enviam para outra. Em seguida, precisamos conectar LANs separadas de diversas partes do mundo que usam diferentes tipos de links e para isso existem os **roteadores** que fazem essa tradução (usam o padrão global IP). Depois temos que encontrar **algoritmos** que consigam fazer o roteamento de forma eficiente e tolerante a problemas de uma LAN para outra... isso inclui **hardware especializado** para isso. Redes compostas por redes locais são chamadas de redes chaveadas.

## **Switches (chaves)**

### **Bridge**

O antecessor do Switch para quando os computadores eram conectados por barramento (um fio unico)... dividiamos o fio em 2 pedaços e cada pedaço conectava grupos separados de computadores. Esses grupos são conectados por uma bridge que filtra pacotes... só passam para o outro grupo se o destinatario estiver lá (evitar colisões no outro grupo)... além disso ele reenvia o sinal recuperando a energia tbm.

### **HUBs (repetidor)**

Tecnologia antiga que atua na camada 1 do modelo OSI e funciona como um switch porém ao invés de redirecionar e armazenar sinais ele simplesmente pega o sinal eletrico recebido e faz broadcast para todos os outros nodes conectados. Ainda possuem problemas graves de colisão e segurança pois todos pacote é recebido por toda a LAN.

### **Encaminhamento**

Switches podem comunicar não apenas com sua propria LAN mas também com outros switches (e roteadores tbm) através de links ponta-a-ponta... eles fazem o transporte de um pacote de entrada ate uma (ou mais) saida correta, mas como o pacote vai encontrar ela? o pacote tem que levar essa informação em seu cabeçário/header mas como usar essa informação... existem 3 principais estratégias de encaminhamento: 

- **Datagrama (Sem conexão e o mais usado):** 

    1. Enviamos cada pacote de forma independente com o endereço completo do destino (MAC, IP...)... fica no header ocupando grande espaço
    2. Cada switch tem uma tabela que mapeia endereço -> porta para chegar nele... usamos bastante espaço no switch então se feito de maneira burra
    3. Seguimos as saidas respectivas do endereço ate chegar ao final

    - Como cada pacote é independente, eles não necessáriamente seguem a mesma rota (por exemplo se um switch cair, ele entrega por outra rota) e consequentemente podem chegar fora de ordem... 
    - Sem overhead de setup

- **Circuito Virtual (Orientado a Conexão):** 

    1. Setup que definimos uma rota fixa para o destino e prenchemos o vetor dos switches com ID_rota_x -> (Porta de saida, New_id_rota_x)... overhead de envio mais alto. Trocar o id se chama Label Swapping e evita que 2 usuários usem o mesmo id.
    2. Seguimos os IDs da nossa rota fixa e suas respectivas portas de saida ate chegar ao destino... precisamos carregar no header o ID atual da rota (int, então é pequeno) 

    - Todos os pacotes para o mesmo destino seguem a mesma rota e se um link cai perdemos os pacotes
    - Mais rapido que datagrama pois usamos indice inteiro ao invés de hash 

- **Source Routing:** 

    1. Antes de enviar o pacote definimos uma rota fixa e colocamos todos os ids das portas de saida dos switches no header do pacote... usando bastante espaço header
    2. Seguimos a rota descrita no header

    - Mesmos problemas do circuito virtual
    - Necessário conhecer toda rede

### **Switches para Ethernet**

Antigamente as redes locais de ethernet eram simplesmente varios computadores conectados em um unico cabo de rede longo... isso gera alguns problemas como:
- 2 computadores enviam pacotes simultaneamente e acontece uma colisão 
- Sinal enfraquece conforme atravessa cabos muito longos
- Sinal é compartilhado com todos da LAN

Hub resolve apenas o problema do sinal fraco repetindo o sinal para todos... porém switches não apenas recebem repetem o sinal, ele recebe em uma porta especifica, armazena temporariamente, verifica a integridade e então entrega o pacote em outra porta. Isso acaba com o problema de colisão e acaba com o problema de privacidade... claro, supondo que as maquinas estejam em formato estrela.

#### **Aprendendo tabelas de redirecionamento em datagramas**

Uma premissa do datagrama é que os switches tenham uma tabela que aponte para qual porta enviar o pacote... isso é dificil de garantir pois:

- Redes grandes como internet tem milhões de nodes e para ter uma tabela desse tamanho é necessário muita memória.
- Mesmo com memória infinita se trocarmos a porta de entrada de rede do nosso computador ? Adicionarmos um novo computador na rede ? teriamos que alterar todos os outros switches.

Inicialmente a tabela do switch não possui nenhuma entrada... vai aprendendo conforme os pacotes são enviados.

A ideia é: 

- Quando um pacote chega no switch sabemos que o remetente deste pacote esta na porta em que o pacote chegou... então criamos uma entrada na tabela que mapeia endereço do remetente para porta que o pacote chegou. 
- Quando um pacote chega no switch, se o destino esta na tabela, seguimos a porta mapeada... se não sabemos qual porta fica o destino, fazemos broadcast para todas as portas (menos a que recebeu) ate encontrar o destino (isso é chamado de flooding). Todos os switches que recebem do broadcast também atualizam as tabelas com o remetente do pacote. Se o destino responder o remetente, o remetente aprende onde o destino esta de agora em diante, ou seja, switch aprende apenas onde quem enviou o pacote esta.
- Se o remetente e destino estao na mesma porta, o pacote é descartado. Se a entrada na tabela não for mais usada ela tem um timeout e é removida.

#### **STP para o problema de ciclos**

Problema: um ciclo + nó desconhecido faria o pacote circular infinitamente pela rede... solução: encontramos uma arvore geradora STP (arvore que minimiza a distancia da raiz para todos os outros nós) da rede (nós são os switches) e caso alguma coisa na rede mude como um nó cair nos executamos o algoritmo novamente.

Ideia do STP: 

- Cada switch (nó) tem um ID unico inteiro, usamos o switch com menor ID como raiz da arvore... além disso tem também o ID da raiz da arvore, distancia do nó a raiz da árvore e os estados das portas.
- Inicialmente todos os nós se consideram a raiz e fazem **multicast (só envia para switches, não para computadores (multicast é enviar para alguns/grupo))** de uma mensagem especial chamada BPDU, se o nó recebe sinal de outro nó que o ID é menor que o dele, ele para de enviar sua mensagem e passa a reenviar as mensagem do nó raiz. Se voce recebe o mesmo pacote por outra porta verifica a distancia (dentro do pacote) daquela porta para o nó raiz e deixa apenas a porta mais proxima da raiz ativada.
- Quando convergir todos da arvore vão ter a mesma raiz e vão continuar recebento esse pacote da raiz de tempo em tempo... se esse pacote sumir por muito tempo significa que algo na rede mudou e outro caminho vai ser encontrado.

#### **Limitações de switches e VLANs**

Problemas: STP é um algoritmo linear no numero de switches e se a rede for grande demais isso fica caro... além disso pode encontrar arvores ruins pois não leva em conta capacidade dos nós. O broadcast durante o aprendizado da rede gera uma grande difusão/trafego de mensagens em literalmente toda a rede... redes fora do seu contexto de trabalho não precisam receber esses pacotes.

VLAN: Criação de LANs virtuais (lógicas) dentro de uma rede e dividir responsabilidades... um VLAN por departamento por exemplo. Isso separa os trafegos independentes, reduz o numero de broadcasts desnecessários e mantem uma STP para cada VLAN. Isso é feito colocando uma tag de 4bytes no dataframe da ethernet que identifica qual VLAN o pacote tem que chegar... o switch ve e manda para as portas corretas (multicast).

## **Internet (IP)**




