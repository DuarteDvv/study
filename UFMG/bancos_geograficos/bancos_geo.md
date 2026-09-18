## **Aquisição dos dados** 





## **Modelagem OMT-G**

O OMT-G (Object Modeling Technique for Geographic Applications) é um modelo de dados orientado a objetos voltado para o projeto conceitual de Sistemas de Informação Geográfica (SIG) e bancos de dados geográficos (Um extensão do UML).

### **Classes**

Existem 3 tipos de classe no OMT-G:

#### **Classes convencionais do UML**

Não possuem componente especial como por exemplo: funcionário, proprietário e etc

#### **Geo-objetos**

Geo-objetos são elementos:
- **Individuais**, ou seja, cada elemento tem seus atributos/caracteristicas proprias -> cada arvore no centro de BH tem sua propria altura, idade e endereço.
- **Discretos no espaço**, ou seja, não existem para qualquer coordenada mas sim para algumas especificas -> se existe uma arvore na coordenada (x,y) não necessáriamente tem outra na coordenada (x+0.001,y).

Os principais tipos são:

- **Pontos:** Uma coordenada no espaço como por exemplo arvores
- **Linhas:** Uma lista de coordenadas no espaço como por exemplo meio-fio
- **Poligonos:** Uma lista de coordenadas em que a primeira coordenada é igual a ultima (um poligono é por definição fechado e 2 poligos podem ter interseção mas isso não é obrigatorio). Além do poligono principal, podem ter poligodos extras que representam buracos dentro do poligono que não fazem parte do poligo principal, ou seja, a area do poligono é igual a area do poligo principal subtraida pelas areas dos poligonos extras. Um exemplo disso são lotes. 
- **Rede/Grafo:**
    - **Vértices:** São representados como pontos normais, como por exemplo conexões de esgoto.
    - **Arestas:** são representadas como linhas que começam em um vértice e terminam em outro, como por exemplo o encanamento entre duas conexões de esgoto. Cada aresta é ligada topologicamente aos nós que ela conecta e portanto carrega referencia deles.

#### **Geo-campos**

Um geo-campo representa um fenômeno de variação contínua no espaço, algo que existe em todo ponto de uma região, não só em locais discretos e portanto:
- **Não é individualizavel**, pois é um fenomeno de toda região, se fosse a contagem seria infinita.
- **Não é discreto e sim continuo,** para qualquer coordenada o geo-campo tem um valor.

Os principais são:

- **Amostras:** Literalmente um ponto no espaço continuo que possui uma coordenada e um valor. Por exemplo uma medição de temperatura no ponto (x,y). Arvore é uma entidade que você identifica e nomeia e portanto é um geo-objeto; a amostra é só uma medição pontual de um fenômeno que, em teoria, existe em todo lugar, o ponto é apenas onde você conseguiu medi-lo.
- **Sub-divisão planar:** São poligonos porém com restrições de que não podem se sobrepor, precisam cobrir toda a área de interesse e cada poligono tem um valor especifico de amostra. Um bom exemplo são zonas climaticas em que não podem ter 2 climas simultaneamente e cada uma tem um valor de clima.
- **Isolinhas:** Linhas conectando as coordenadas de *amostras* com o mesmo valor no espaço, além disso elas não se cruzam. Por exemplo curvas de nivel em que a linha tem a mesma altitude.
- **Triangulação:** Representado por amostras (pontos) e esses pontos geram poligonos (triangulos). Dado essas 3 amostras que temos usamos interpolação para aproximar todos os outros pontos dentro do triangulo com base nos seus 3 vértices. Util quando se tem pontos de amostra irregularmente distribuídos e quer superfície contínua interpolada (ex. relevo).
- **Tesselação:** Grade regular de células, cada uma com um valor. Util se o dado já vem em grade regular (imagem) ou você quer um formato uniforme para processar. Unico com representação diferente dos outros pois não usa pontos e vetor e sim algo chamado raster.

#### **Atributos**

As classes tem atributos também assim como no UML normal

### **Relacionamentos**

#### **Relacionamento Simples**

Relacionamento sem nenhuma restricao espacial embutida, ou seja, um relacionamente como qualquer outro do UML normal. Um exemplo disso é departamento tem funcionário. Basicamente uma linha preta com uma seta no meio descrevendo a relacao.

![alt text](imgs/relacionamento_simples.png)

#### **Relacionamento Espacial (topologico)**

Ocorre quando a geometria de uma classe se relaciona com a geometria de outra. Por exemplo lote está dentro de Quadra. Linha tracejada com o nome da relacao em cima.

- A disjunto de B, A toca B, A igual B, A sobrepoe B
- A dentro de B / B contem A 
- A coberto por B / B cobre A

![alt text](imgs/relacionamento_espacial.png)

#### **Relacionamento de rede** 

Ambos aqui sao representados por 2 linhas tracejadas e o nome da relacao no meio delas (ex: malha viaria)

- *aresta-nó:* liga a classe aresta à classe nó (cada arco referencia o nó inicial e o nó final)

![alt text](imgs/relacionamento_arco_no.png)

- *aresta-aresta:* auto-relacionamento dentro da própria classe arco, usado quando arcos se conectam diretamente entre si sem um nó explícito modelado (ex.: trechos de rio que se emendam).

![alt text](imgs/relacionamento_arco_arco.png)

#### **Agregacao**

- *Agregação comum:* relação todo-parte convencional, sem exigência geométrica (ex.: Departamento é composto por Funcionários, mas funcionário não "preenche" o espaço do departamento).
- *Agregação espacial:* além do todo-parte, exige que geometricamente as partes preencham o todo, cada parte contida no todo, o todo inteiramente coberto pela união das partes, e as partes só podem se tocar ou ser disjuntas entre si (nunca se sobrepor). Exemplo: Quadra (todo) formada pelos Lotes (partes).

![alt text](imgs/relacionamento_agregacao.png)

#### **Generalizacao/especializacao**

Divisao em subclasses:

- *Triângulo preto/preenchido* -> Total, toda instância da superclasse obrigatoriamente cai em alguma subclasse (não pode "sobrar" instância sem categoria)
- *Triângulo vazado/contorno* -> Parcial, pode existir instância da superclasse que não pertence a nenhuma subclasse
- *Com bolinha* -> Sobreposto, uma mesma instância pode pertencer a mais de uma subclasse simultaneamente
- *Sem bolinha* -> Disjunto, as subclasses são mutuamente exclusivas; uma instância só pode estar em uma delas

![alt text](imgs/relacionamento_generalizacao.png)

### **Cardinalidade**

Indicada em cada ponta do relacionamento, no formato *(mínimo, máximo)*:

(0,1) → participação opcional, no máximo um
(1,1) → participação obrigatória, exatamente um
(0,*) → participação opcional, pode ter vários
(1,*) → participação obrigatória, pelo menos um, pode ter vários

Se uma classe A tem proximo dela um (1,*) significa que a outra classe B que tem relacionamento com ela tem no minimo uma instancia de A e no maximo varias.

### **Restricoes de integridade espacial**

#### **Para geocampos**

- **R1:** Vale para todo geocampo, diz que para qualquer ponto no espaço deve existir um valor
- **R2:** Isolinhas, diz que as linhas não se cruzam e o valor deve ser constante na linha (iso = igual)
- **R3:** Tesselação, diz que é necessários celulas regulares que combrem toda a região
- **R4:** Subdivisão planar, diz que os poligonos não tem interseção e combrem toda a area
- **R5:** Malha triangular, diz que os valores do centro do triangulo serão interpolados pelos valores conhecidos nos 3 vértices

#### **Para relacionamentos**

- **R6:** Aresta e nó, todo nó tem pelo menos uma aresta; toda aresta liga exatamente dois nós
- **R7:** Aresta e Aresta, toda aresta deve estar ligada a pelo menos um outra aresta

#### **Agregação especial**

- **R8:** Agregação espacial, cada parte contida no todo; o todo é coberto pela união das partes; as partes só se tocam ou são disjuntas entre si (nunca se sobrepõem).

#### **Para geo-objetos**

- **R9:** Linhas, não podem se autointerceptar (devem ser simples).
- **R10:** Polígonos simples, contorno fechado, sem autointerseção.
- **R11:** Regiões poligonais, validade de polígonos com buracos/múltiplas partes.

#### **Topologia**

- **RT**, garante que a instância realmente obedece ao tipo de relação espacial declarado no diagrama (ex.: se você modelou "Lote dentro de Quadra", o SGBD tem que impedir, via trigger, um lote que não esteja de fato dentro da quadra).

#### **Como isso é garantido?**

Na prática (implementação via AST-PostGIS), cada restrição vira um trigger no banco,  por isso a tabela de mapeamento lógico->físico associa cada tipo de classe/relacionamento espacial diretamente a um tipo de trigger específico (ast_line, ast_polygon, ast_node, etc., cada um já "carregando" as restrições que precisa checar).

## **GEOsql**

Num banco tradicional, você guarda números, textos e datas. Num banco geográfico, cada registro pode ter também uma geometria. As extensões espaciais acrescentam esses tipos e funções ao SQL.

### **PostGIS**

O PostGIS é uma extensão gratuita de código aberto para o sistema de gerenciamento de banco de dados PostgreSQL que adiciona suporte a dados e análises espaciais.

#### **Geometry x Geography**

O PostGIS oferece dois tipos espaciais principais.

- **geometry:** trabalha num sistema cartesiano/plano. As unidades utilizadas nas operações dependem do sistema de coordenadas usado.
- **geography:** considera a Terra de forma esférica/esferoidal, usando WGS84, e as medidas são feitas em metros.

```sql
ST_Distance(a.geom::geography, b.geom::geography) 
```
O casting ::geography é usado porque queremos uma distância real sobre a Terra, em metros.

#### **SRID**

Uma geometria não é apenas uma sequência de números. É necessário saber o que aquelas coordenadas representam. O SRID identifica o sistema de referência espacial. Por exemplo, coordenadas podem estar em latitude/longitude ou em uma projeção métrica e portanto elas nao sao comparaveis.

#### **Funcoes de relações espaciais**

Essas funcoes permitem computar relacoes, atributos e novas geometrias/geografias

#### **Funções de relações espaciais**

Essas funções permitem verificar relações espaciais, calcular medidas e gerar novas geometrias/geografias.

- `ST_Contains(A, B)`
  - **O que faz:** Verifica se a geometria A contém espacialmente a geometria B. Nenhum ponto de B pode estar fora de A e pelo menos um ponto de B deve estar no interior de A.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).

- `ST_Within(A, B)`
  - **O que faz:** Verifica se a geometria A está completamente dentro da geometria B. É o inverso de `ST_Contains(B, A)`.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).

- `ST_DWithin(A, B, distance)`
  - **O que faz:** Verifica se a distância entre A e B é menor ou igual à distância especificada.
  - **Entrada:** Duas geometrias/geografias A e B e uma distância.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).
  - **Observação:** Com `geography`, a distância é dada em metros. Ex.: `50000` = 50 km.

- `ST_Covers(A, B)`
  - **O que faz:** Verifica se nenhum ponto de B está fora de A. B pode estar no interior de A ou sobre sua fronteira.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).

- `ST_CoveredBy(A, B)`
  - **O que faz:** Verifica se A é coberta por B, ou seja, nenhum ponto de A está fora de B. É o inverso de `ST_Covers(B, A)`.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).

- `ST_Touches(A, B)`
  - **O que faz:** Verifica se A e B se tocam pelas suas fronteiras, mas seus interiores não possuem pontos em comum.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).

- `ST_Equals(A, B)`
  - **O que faz:** Verifica se A e B representam espacialmente a mesma geometria.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).

- `ST_Overlaps(A, B)`
  - **O que faz:** Verifica se A e B possuem uma região em comum, possuem a mesma dimensão e nenhuma contém completamente a outra.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).

- `ST_Intersects(A, B)`
  - **O que faz:** Verifica se A e B possuem pelo menos um ponto em comum.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).
  - **Observação:** É o contrário de `ST_Disjoint(A, B)`.

- `ST_Disjoint(A, B)`
  - **O que faz:** Verifica se A e B não possuem nenhum ponto em comum.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** `BOOLEAN` (`TRUE` ou `FALSE`).
  - **Observação:** É o contrário de `ST_Intersects(A, B)`.

- `ST_Distance(A, B)`
  - **O que faz:** Calcula a menor distância entre A e B.
  - **Entrada:** Duas geometrias/geografias: A e B.
  - **Saída:** Um valor numérico representando a distância.
  - **Observação:** Com `geography`, a distância é retornada em metros. Com `geometry`, a unidade depende do sistema de coordenadas.

- `ST_Area(A)`
  - **O que faz:** Calcula a área de uma geometria, normalmente um polígono ou multipolígono.
  - **Entrada:** Uma geometria/geografia A.
  - **Saída:** Um valor numérico representando a área.
  - **Observação:** Com `geography`, normalmente o resultado é dado em metros quadrados. Com `geometry`, depende da unidade do sistema de coordenadas.

- `ST_Length(A)`
  - **O que faz:** Calcula o comprimento de uma geometria linear, como uma rodovia, ferrovia ou rio.
  - **Entrada:** Uma geometria/geografia A.
  - **Saída:** Um valor numérico representando o comprimento.
  - **Observação:** Com `geography`, o comprimento é dado em metros. Com `geometry`, depende da unidade do sistema de coordenadas.

- `ST_Transform(A, novoSRID)`
  - **O que faz:** Transforma/reprojeta a geometria A de seu sistema de referência atual para outro sistema de referência espacial.
  - **Entrada:** Uma geometria A e o SRID do sistema de referência de destino.
  - **Saída:** Uma nova geometria representada no novo sistema de coordenadas.
  - **Exemplo:** `ST_Transform(A, 4326)`.

- `ST_Buffer(A, distance)`
  - **O que faz:** Cria uma região ao redor da geometria A a uma determinada distância.
  - **Entrada:** Uma geometria/geografia A e uma distância.
  - **Saída:** Uma nova geometria, normalmente um polígono.
  - **Exemplo:** `ST_Buffer(A::geography, 50000)` cria uma região de 50 km ao redor de A.

- `ST_Union(...)`
  - **O que faz:** Une duas ou várias geometrias em uma única geometria, eliminando as fronteiras internas das regiões que se sobrepõem.
  - **Entrada:** Duas geometrias ou um conjunto de geometrias.
  - **Saída:** Uma nova geometria resultante da união.
  - **Exemplos:** `ST_Union(A, B)` ou `ST_Union(geom)` para agregar várias geometrias.

- `ST_Intersection(A, B)`
  - **O que faz:** Calcula a parte espacial que A e B possuem em comum.
  - **Entrada:** Duas geometrias: A e B.
  - **Saída:** Uma nova geometria correspondente à interseção entre A e B.

- `ST_Difference(A, B)`
  - **O que faz:** Calcula a parte de A que não pertence a B. Pode ser entendida como `A - B`.
  - **Entrada:** Duas geometrias: A e B.
  - **Saída:** Uma nova geometria correspondente à parte restante de A.

#### **Indices espaciais**

Indices tradicionais, como B-Tree, funcionam muito bem para valores unidimensionais: 10, 15, 20, 25... Uma geometria possui várias dimensões. Por isso, bancos geográficos precisam de métodos de acesso multidimensionais. Sem índice, uma consulta: Quais municípios cruzam esta rodovia? O banco teria que comparar a rodovia com todos os municípios. O índice espacial reduz rapidamente o conjunto de objetos que precisam ser analisados e são praticamente essenciais para bancos geográficos de tamanho razoável.

Analisar um polígono complexo pode ser caro. Por isso o banco primeiro trabalha com uma aproximação muito simples: o Retângulo Envolvente Mínimo (REM). É o menor retângulo, paralelo aos eixos, que contém completamente uma geometria. Primeiro o índice testa os retângulos e isso produz candidatos. Depois o PostGIS realiza o cálculo geométrico exato apenas sobre esses candidatos. 