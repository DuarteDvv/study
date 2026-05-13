

#### **Modelo OSI**

- **Camada 1 (Fisica/Hardware):** Lida com sinais elétricos e cabos (conversão para ondas de radio (eletromagneticas) e etc)

- **Camada 2 (Enlace/link):** Lida com a tradução dos dados para um formato que possa ser transmitido (entrega e leitura) dentro de uma LAN.

- **Camada 3 (Rede/Roteamento):** Lida com roteamento e conversão entre tipos de links diferentes para transmitir dados entre diferentes LANs.

- Camada 4 (Transporte)

- Camada 5 (): 

#### **Pacote**

Pacote = Dados do usuário + header de controle

#### **Enlace ou Link**

link é o canal de comunicação que conecta dois ou mais nodes... esses links podem usar materiais/tecnologias diferentes e trabalharem com protocolos de enlace(link) diferentes.

- **Meios fisicos como:** Cabos de cobre para LANs, Fibra optica para longas distancias e ondas de radios (wifi/satelite) que são transmitidas pelo ar.

- **Topologias como:** Ponto-a-ponto que conecta apenas 2 maquinas e multi-acessso em que varias maquinas competem no mesmo link.

- **Protocolos de Enlace:** Regras que organizam os dados para serem transmitidos nesses links... como Ethernet que é padrão para LANs, Wifi que é padrão para links multi-acesso e PPP que é padrão para links ponto-a-ponto.

#### **Endereço MAC**

MAC serve como identificador unico da sua placa de rede (wifi ou ethernet)... servindo como uma espécie de CPF para seu computador. Geralmente é composto com 48 bits sendo alguns deles responsavel para identificar o fabrincante e outros metadados da peça.

#### **Topologias de cabo**

- **Barramento:** Um unico cabo principal conecta todos os computadores (computadores se ligam ao cabo)

- **Ring:** Um cabo circular entre varios computadores (usavam um token para dizer quem esta mandando pacotes)

- **Star:** Um switch/roteador central em que varios computadores fazem ponta-a-ponta

- **Arvore:** Hierarquia de switches

