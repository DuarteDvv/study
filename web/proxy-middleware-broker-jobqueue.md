# **Proxies, Middlewares, Brokers e Job queues**

Variações de uma mesma ideia: colocar um intermediário entre quem pede e quem faz

## **Proxies**

Um proxy é um intermediário que recebe requisições e as repassa para outro destino, podendo inspecionar, modificar, cachear ou redirecionar no caminho.

### **Foward Proxy (Proxy de saida)** 

Fica na frente do cliente. O servidor de destino não sabe quem é o cliente real, só vê o proxy. (VPN faz algo parecido)

```
Cliente → Forward Proxy → Internet → Servidor
```

Ajuda na:
- *Anonimização*, esconder o IP do cliente
- *Uso corporativo*, empresas usam para filtrar/bloquear sites, monitorar tráfego de funcionários

### **Reverse Proxy (Proxy de entrada):** 

Fica na frente do servidor. O cliente não sabe (nem precisa saber por seguranca) quantos servidores existem por trás, só vê um ponto de entrada. Um exemplo é o Nginx.

```
Cliente → Internet → Reverse Proxy → [Servidor 1, Servidor 2, Servidor 3...]
```

- **Load Balancer:** Distribui requisições entre múltiplos servidores backend. Tecnicamente é um tipo de reverse proxy mas pode operar em camada mais baixa. Util para reduzir a sobrecarga de servidores usando algorirmos de fila: 
    - *Round robin*, distribui em sequência
    - *Weighted Round robin*, servidores mais fortes recebem mais tráfego

- **API Gateway:** reverse proxy especializado para APIs, com responsabilidades a mais que um reverse proxy genérico como:
    - *Autenticação/autorização centralizada*, valida JWT, API keys, OAuth antes de rotear
    - *Rate limiting* por cliente, por rota, por plano
    - *Roteamento para microsserviços*, um gateway na frente de N serviços internos
- **CDN (Content Delivery Network):** Caches distribuidos geograficamente para entrega de conteudo estatico como HTML, CSS, imagens e videos globalmente sem precisar buscar no servidor sempre. É util para melhorar a experiencia do usuário para que nao tenha que esperar muito.

## **Middlewares**

Middleware é qualquer código que fica "no meio" de um fluxo, interceptando e processando algo antes (ou depois) de chegar ao destino final.

### **Middleware no contexto Web (request/response pipeline)**

O padrao na web é Chain of Responsibility em que a requisição passa por uma cadeia de funções, cada uma podendo:
- Modificar a requisição
- Modificar a resposta
- Interromper o fluxo (ex: retornar 401 sem nem chegar na rota)
- Passar adiante pra próxima 

```
Request → [Middleware 1] → [Middleware 2] → [Middleware 3] → Handler final → Response
             (logging)         (auth)          (CORS)
```

Middlewares comuns:
- Logging, registra cada requisição (método, path, tempo de resposta)
- Autenticação/Autorização, valida token, injeta req.user
- CORS, adiciona headers de Cross-Origin
- Rate limiting, bloqueia excesso de requisições por IP/usuário

### **Middleware em outros contextos**

Middlewares podem estar em outros contextos como Sistema operacional ou processamento de Jobs/messages

## **Brokers**

Um broker é um intermediário que recebe mensagens de quem produz (producer) e entrega para quem consome (consumer), desacoplando os dois lados no tempo e no espaço. O producer não precisa saber quem (ou quantos) vai consumir, nem quando isso vai acontecer.

```
Producer → Broker → Consumer(s)
```

### **Pq brokers ?**

- **Desacoplamento:** producer e consumer não se conhecem, só conhecem o broker
- **Resiliência:** se o consumer cai, mensagens ficam esperando (não se perdem, dependendo da config)
- **Escalabilidade horizontal:** pode adicionar mais consumers pra processar em paralelo sem tocar no producer
- **Processamento assíncrono:** producer não precisa esperar o processamento terminar pra continuar

### **Modelos de entrega (garantias)**

- **At-most-once:** mensagem é entregue 0 ou 1 vez. Pode perder mensagem, nunca duplica. (rápido, mas arriscado)
- **At-least-once:** mensagem é entregue 1 ou mais vezes. Nunca perde, mas pode duplicar. (o mais comum na prática). Processar a mesma mensagem duas vezes não pode causar efeito colateral duplicado (ex: cobrar o cliente duas vezes) e isso é resolvido com um id de mensagem armazenado assim que processado.
- **Exactly-once:** mensagem processada exatamente 1 vez. Extremamente difícil de garantir de verdade em sistemas distribuído., geralmente o que existe na prática é "at-least-once + idempotência no consumer", que simula exactly-once do ponto de vista do efeito final. 

### **Push vs Pull**

- **Push:** o broker empurra a mensagem pro consumer assim que ela chega (ex: RabbitMQ pode fazer isso) -> tem menor latência mas pode sobrecarregar um consumer lento se não houver controle de fluxo 
- **Pull:** o consumer ativamente pergunta "tem mensagem nova?" (ex: Kafka funciona assim, consumer faz poll) -> dá mais controle pro consumer sobre o próprio ritmo (evita ser inundado)

### **Padrões de mensageria**

#### **Fila ponto-a-ponto (Work Queue)**

Uma mensagem é entregue e processada por um único consumer, mesmo que existam vários consumers competindo pela fila (competing consumers pattern). Bom para distribuir trabalho. (Redis e RabbitMQ sao exemplos)

```
Producer → [Fila] → Consumer A (pega mensagem 1)
                  → Consumer B (pega mensagem 2)
```
No RabbitMQ, o producer não manda direto pra fila, manda pra um exchange, que decide (via regras de roteamento) pra quais filas replicar:

- **Direct exchange:** roteia por uma chave exata (routing key = "erro" vai só pra fila de erros)
- Topic exchange:** roteia por padrão com wildcard (pedido.*.criado)
- **Fanout exchange:** replica pra todas as filas ligadas (equivalente a pub/sub)
- **Headers exchange:** roteia baseado em headers da mensagem


#### **Pub/Sub (Publish/Subscribe)**

Uma mensagem publicada num tópico é entregue para todos os subscribers daquele tópico. Cada subscriber recebe sua própria cópia.

```
Producer → [Tópico] → Subscriber A (recebe cópia)
                    → Subscriber B (recebe cópia)
                    → Subscriber C (recebe cópia)
```
O Kafka é um exemplo:

- Mensagens ficam num tópico, dividido em partições
- Cada mensagem tem um offset (posição sequencial dentro da partição)
- Consumers não "removem" mensagens, eles leem e avançam seu próprio offset, então múltiplos consumers podem ler o mesmo dado de formas independentes, e mensagens podem ser re-lidas (replay)
- Consumers de um mesmo grupo dividem as partições entre si (paralelismo); grupos diferentes leem tudo independentemente

## **Job Queues**

Uma job queue (fila de tarefas) é uma camada construída em cima de um broker, especializada em executar unidades de trabalho assíncronas, os "jobs". 

```
App → enfileira job → [Broker por baixo] → Worker pega job → executa → sucesso/falha
```

### **Pq usar ?** 

Se seu endpoint HTTP faz algo pesado (enviar email, gerar PDF, processar imagem, chamar API externa lenta) dentro do próprio request, você tem problemas:

- Cliente fica *esperando* (UX ruim, timeout)
- *Servidor web fica ocupado*, reduzindo capacidade de atender outras requisições
- Não dá pra tentar de novo automaticamente

Job queue permite que o endpoint só enfileire o job e responda rápido. Um worker separado, em outro processo (ou até outra máquina), pega o job da fila e executa.

### **Producer vs Worker**

- *Producer:* quem enfileira o job (geralmente sua aplicação web)
- *Worker:* processo separado que fica escutando a fila, pega jobs e executa

Workers geralmente rodam como processos independentes, separados do processo web (você escala workers e servidores web de forma independente)

### **Capacidades**

- **Retry:** Se um job falha (exceção, timeout, API externa fora do ar), a fila pode tentar de novo automaticamente.
- **Idempotência:** Como jobs podem rodar mais de uma vez é importante garantir que o mesmo efeito nao seja aplicado 2x
- **Prioridade:** múltiplas filas por prioridade (high, default, low), e workers configurados pra checar a fila de alta prioridade primeiro.
- **Agendamento:** Agendar execucoes de jobs

### **Job Queue vs Workflow Orchestration**

- **Job queue (Celery, Sidekiq):** bom pra tasks independentes e relativamente curtas. Se você precisa encadear múltiplos passos com lógica condicional, retry granular por passo, e estado persistente de longo prazo, começa a ficar difícil.
- **Workflow orchestration (Airflow, AWS Step Functions):** pensado pra workflows multi-etapa, potencialmente de longa duração (horas, dias).