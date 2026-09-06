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
- **Tesselação:** Grade regular de células, cada uma com um valor. Util se o dado já vem em grade regular (imagem) ou você quer um formato uniforme para processar. Unico com representação diferente dos outros pois não usa pontos e vetor.

#### **Atributos**

As classes tem atributos também assim como no UML normal


### **Relacionamentos**




