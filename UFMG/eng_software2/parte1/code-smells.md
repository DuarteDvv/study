## Code Smells (Bad Smells)

Code smells sao uma firma de identificar codigos de baixa qualidade que talvez precisem ser refatorados... mas nao imediatamente é apenas uma flag para os desenvolvedores se atentarem aquela parte do codigo.
Alguns deles sao:

- Codigo Duplicado: quando temos codigo repetido em mais de um metodo... isso faz com que qualquer manutencao naquele codigo tenha que ser repetida N vezes e aumenta complexidade do codigo. As principais formas de eliminar duplicacao é extrair aquele trecho em um metodo sozinho, se forem classes muito parecidas extrair uma classe unica delas e se for um metodo de classes filhos subir o metodo para a classe pai. Existem 4 tipos principais de codigo duplicado: 

    - Tipo 1: O codigo é totalmente igual e só varia espacos e comentários...
    - Tipo 2: A logica do codigo é a mesma mas só muda o nome das variaveis
    - Tipo 3: A logica é a mesma mas admite algumas pequenas mudancas que nao mudam a logica como um log, print e etc...\
    - Tipo 4: Sintaxe (codigo) diferente mas a semantica (logica) é a mesma 

- Metodos Longos: Metodos muito longos pode ser um sinal para dividir em mais metodos... um metodo muito longo aumenta complexidade do codigo e dificulta o entendimento. Sempre um comentário é necessário talvez uma extracao de metodo também seja.

- Classes grandes: Mesmo problema dos metodos longos mas com classes... aplicar extracao de classe se necessário. Uma instancia desse problema é chamada de GOD CLASS em que uma classe cresceu tanto que grande parte da logica do sistema faz parte dela... geralmente tem nomes genéricos como System/Subsystem.

- Feature Envy (inveja): metodos invejosos que usam mais metodos/atributos de outra classe que da sua propria... pode ser sinal para mover o metodo para outra classe. 

    public class DrawingEditorProxy extends AbstractBean implements DrawingEditor {
        ...
        void fireAreaInvalidated2 (AbstractTool abt, Double r ){
        Point p1 = abt.getView().drawingToView(...);
        Point p2 = abt.getView().drawingToView(...);
        Rectangle r= new Rectangle(p1.x,p1.y,p2.x-p1.x p2.y-p1.y);
        abt.fireAreaInvalidated(r);
        }
    ...
    }  -> Aqui por exempo talvez seja melhor mover o metodo para a classe AbstractTool

- Muitos parametros: Metodos devem ser pequenos e também ter poucos parametros... o problema de muitos parametros pode ser resolvido de 2 formas:

    - Se um dos parametros A pode ser adquirido usando o outro B entao só precisamos do parametro B como parametro e o A pegamos dentro do metodo
    - Se os parametros fazem parte de alguma composicao logica talvez faca sentido transformar os parametros em um objeto simples em que seus atributos sao os parametros 

    No exemplo do slide faz sentido transformar 3 parametros soltos em um objeto do tipo:

    @dataclass
    class DadosTransferencia:
        conta_origem: str  
        conta_destino: str
        valor: float   

    pois esses 3 parametros estao sempre juntos no conjunto de parametros dos metodos.
