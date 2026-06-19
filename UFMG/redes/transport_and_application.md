# **Transporte e Aplicação**

## **Motivação Transporte**

A camada de rede só sabe entregar um pacote de um computador para outro (host-to-host) mas **como entregar o pacote para o processo correto** dentro daquela maquina (process-to-process) ? Esse é o trabalho da **camada de transporte**.  

Enquanto o processo precisa dos pacotes com entrega garantida, ordem correta, sem duplicação e de forma sincronizada. A rede (IP) entrega o **best effort** dele que pode perder mensagens, duplicar mensagens ou entregar fora de ordem. Isso é um conflito de interesse... o objetivo dessa camada é **mascarar essas falhas** utilizando protocolos como UDP e TCP que fazem isso de maneira diferente.

## **UDP (User Datagram Protocol)**

È o protocolo de transporte mais simples possivel que não resolve nenhum dos problemas de rede citados. Unica função adicional em cima do IP que ele cria é a **capacidade de separar os pacotes de um fluxo e entrega-los aos processos corretos (demultiplexador)**.

### **Portas e Fila de mensagens** 

Para saber o processo correto ele usa um conceito chamado de **porta que é um ID de 16 bits**. Consequentemente o indentificador agora é o par **(IP, Porta)** (**Nunca** vão existir 2 processos na mesma porta). Portas são padronizadas (well-known) por alguns serviços como HTTP, HTTPS e SSH, então se fazemos uma requisição HTTPS por exemplos ela por padrão chegará na porta 443 e uma vez nessa porta pode ser redirecionada para outra. Em serviços não padronizados pode existir uma consulta para descobrir a porta.

O sistema operacional **implementa portas como filas**, quando um pacote de porta x chega ele é colocado no fim da fila dessa porta. Se a fila estiver cheia, o **pacote simplesmente é descartado** sem avisar o remetente.

### **Checksum**

O UDP não garante entrega mas tenta garantir que se o pacote chegou ele **não esteja corrompido**. Ele faz isso através de **checksum**. Nesse checksum ele usa o **header UDP, dados e pseudo-header** (esse pseudo-header são algumas informações que ele pega do protocolo IP como **IP do destino para garantir que pelo menos chegou no lugar correto** e não foi desviado)

## **TCP (Transmission Control Protocol)**

aaaaaaaaaaaaaaaaaaaaaaaaaaa

## **Implementacao do UDP e TCP no SO**


aaaaaaaaaaaaaaaaaaaaaaa

## **RPC (Remote Procedure Call)**

RPC é um padrao de API que busca fazer com que uma chamada de funcao pela rede se comporte extamente igual uma funcao local. Isso permite que para o dev nao importe se a funcao será executada em um computador ou outro, ele apenas chama a funcao e pega o resultado (Em java e outras linguagens POO é chamado de RMI (remote method invocation)). Porém existem alguns problemas nessa abstracao de funcao local:

- **Rede é imprevisivel:** A mensagem/pacote enviado de um computador para outro pode se perder, chegar fora de ordem e etc...
- **Arquitetura diferente:** Cliente e servidor podem rodar em diferentes SOs, processadores ou linguagens.

Para resolver isso a implementacao do RPC tem que tem 2 componentes principais:

- **O protocolo:** Algum protocolo que garante a entrega da mensagem na ordem correta, resiliente a queda, sem duplicacao e corrupcao como por exemplo o TCP ou alguma implementacao propria.
- **Os stubs e compilador de stubs:** Para permitir que diferentes maquinas se comuniquem sem erro de compatibilidade nos definimos em um arquivo uma interface das funcoes remotas. Lá colocamos nome, parametros e retorno com seus respectivos tipos. Uma vez definida a interface o compilador vai gerar os codigos de serializacao/deserializacao usando os protocolos de transporte (o codigo de pack(tipos, dados)). Quando o cliente chamar a funcao na maquina dele, o stub do cliente recebera os parametros, serializa e envia para o servidor, o stub do servidor deserializa, executa a funcao e serializa o resultado enviando para o cliente novamente.

### **Identificadores no RPC**

Para **identificar qual funcao esta sendo chamada no servidor** o RPC leva com ele um id unico de funcao ou o caminho completo do sistema de arquivo para definicao da funcao. Agora para **identificar qual cliente entregar o resultado** ele usa o **BootID concatenado com o MessageID**. **MessageID identifica a requisica** o e o **BootID (um ID unico gerado ao ligar o computador)** serve para caso o cliente caia depois de enviar uma mensagem, a proxima mensagem que ele enviar por coincidencia tenha o mesmo MessageID da anterior, fazendo com que o servidor interprete como repeticao de mensagem e descarte a mais nova.

### **RPC + UDP**

RPC frequentemente implementa sua própria camada de confiabilidade por cima de protocolos simples como o UDP e para isso ele usa: 

- **Confirmacao e timeout:** se deu timeout e nao recebeu confirmacao reenvia
- **Canais:** Cliente pode fazer varias chamadas remotas simnultanemente em canais diferentes (sequencial no canal)
- **Alive:** Se o processamento demorar muito o cliente envia um ping para ver se o servidor ainda ta online para ver se vale a pena continuar reenviando.
- **At-most-once ou Zero-or-more:** O servidor pode seguir duas semanticas: Garante que o servidor não vai executar a mesma ação duas vezes caso receba uma mensagem duplicada devido a atrasos na rede (salvando no servidor historico) **OU** Não se importa se a função rodar várias vezes. Isso só serve se a função for idempotente (ex: perguntar "Que horas são?")

### **Sinc vs Assinc**

Protocolos podem ser classificados em 3 tipos:

- **Assincronos:** O cliente envia o pacote e volta imediatamente a trabalhar e portanto nao sabe se mensagem chegou

- **Sincronos:** O cliente fica esperando a resposta chegar (RPC)

- **Meio-termo:** O cliente espera apenas ate a confirmacao de que chegou no servidor

### **Implementacoes do RPC**

- **SunRPC:** RPC minimalista que adota filosofia de fazer o basico e deixar o resto para camada de transporte ou aplicacao. Ele usa geralmente **UDP ou TCP**. Usa um **ID de requisicao que o servidor esquece assim que responde** podendo fazer com que uma funcao execute duas vezes por atraso de rede.

- **gRPC:** O mais recente e usado que foi desenvolvido pela google tem algumas caracteristicas interessantes.

    - **LoadBalancer e containers:** Ao invés de enviar uma requisicao para uma maquina manda para um loadbalancer que redireciona para algum container livre.
    - **Terceirizacao:** Ele nao reinventa roda e usa HTTP2 para garantir entrega segura. 
    - **Streaming:** gRPC suporta 4 formatos de comunicacao:
        - Simples: 1 Requisição -> 1 Resposta (o padrão clássico).
        - Server Streaming: O cliente pede 1 vez, o servidor devolve um fluxo contínuo de dados (ex: feed de cotações da bolsa).
        - Client Streaming: O cliente manda um fluxo de dados e o servidor devolve 1 resposta (ex: upload de um vídeo pesado).
        - Bidirecional: Cliente e servidor ficam trocando dados ao mesmo tempo, de forma independente (ex: chat em tempo real).
    - **Protocol Buffers:** Ao invés JSON que é texto e pesado, usa um formato binário muito mais rapido.

