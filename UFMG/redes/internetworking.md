### **Interconexão de redes (internet'working)**

O problema central abordado aqui é que nenhuma tecnologia de rede local (LAN) consegue cobrir uma quantidade de nodes em escala global. Um cabo Ethernet sozinho mal suporta 1024 conexões (nodes) pois a chance de colisão é muita alta, wifi é limitado por distancia e link ponto a ponto envolve apenas 2 nodes.

A ideia então é ter varias subredes locais (LANs) diferentes uma da outra e conectar elas ... isso é chamado de internet. 
Primeiro passo é conectar de maneira eficiente maquinas proximas (mesma LAN) que compartilham o mesmo tipo de link (tipos são iguais se o protocolo de enlace é igual)... isso é alcançado por meio de **switches** que recebem pacotes em uma porta e enviam para outra. Em seguida, precisamos conectar LANs separadas de diversas partes do mundo que usam diferentes tipos de links e para isso existem os **roteadores** que fazem essa tradução (usam o padrão global IP). Depois temos que encontrar **algoritmos** que consigam fazer o roteamento de forma eficiente e tolerante a problemas de uma LAN para outra... isso inclui **hardware especializado** para isso. Redes compostas por redes locais são chamadas de redes chaveadas.


### **Switches (chaves)**

Switches podem comunicar não apenas com sua propria LAN mas também com outros switches (e roteadores tbm) através de links ponta-a-ponta... eles fazem o transporte de um pacote de entrada ate uma (ou mais) saida correta, mas como o pacote vai encontrar ela? o pacote tem que levar essa informação em seu cabeçário/header mas como usar essa informação... existem 3 principais estratégias de encaminhamento: 

- **Datagrama (Sem conexão):** O pacote carrega no header o endereço MAC do destino e cada switcher tem uma tabela que mapeia todos os endereços MAC da LAN para alguma saida. Isso torna o datagrama robusto pois se o caminho mudar no switche, o pacote será simplesmente redirecionado para a nova saida. Usa espaço tanto no switche quanto no proprio pacote mas não tem overhead inicial de envio.

- **Circuito Virtual (Orientado a Conexão):** Aqui existe primeiro uma fase de conexão (escolha de um caminho fixo valido) entre fonte e destino. Os pacotes carregam um identificador de fluxo/destino e todos os switches armazenam dentro de si um dicionário/tabela que mapeia fluxo -> saida correta (tudo que chegar com id x vai para saida k). Tem custo alto para conexão inicial.

- **Source Routing:** Aqui ao enviar o pacote colocamos no header todo o caminho (indices das portas)... exigindo que a fonte conheça toda a rede, usa espaço/tempo com header e tem um overhead alto de escolha de rota (dentre várias)



#### **Vantagens**


