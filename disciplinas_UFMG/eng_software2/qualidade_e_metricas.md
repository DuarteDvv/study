## Qualidade e métricas

Qualidade de software são requisitos a serem atingidos para que o sistema suporte os desejos de todos os tipos de stakeholders... usuários e CEOs. Para medir essa qualidade podemos usar métricas estaticas ou dinamicas. As dinamicas são métricas extraidas durante o funcionamento do sistema (latencia, usuários ativos e etc) já as estaticas são calculadas usando artefatos como codigo e documentação.

### Métricas Básicas

- fan-in(f): representa quantos modulos usam o modulo f... um alto numero significa alta dependencia caso alguma alteração seja feita em f
- fan-out(f): quantos modulos externos f precisa... se é muito grande então essa função é muito complexa.

-------------------------------------------------------------------------------------------------------------------
a) Apresente as métricas fan-in e fan-out para cada função abaixo.

1: fan-in(1) = 0, fan-out(1) = 3
2: fan-in(2) = 1, fan-out(2) = 1
3: fan-in(3) = 3, fan-out(3) = 0
4: fan-in(4) = 2, fan-out(4) = 0
5: fan-in(5) = 1, fan-out(5) = 1
6: fan-in(6) = 1, fan-out(6) = 2
7: fan-in(7) = 0, fan-out(7) = 1

b) Encontre a função mais complexa e com maior impacto de mudança.

A mais complexa é a 1 pois depende de  3 modulos e a com maior impacto de mudança é a 3 que é usada por 3 modulos
---------------------------------------------------------------------------------------------------------------------

- Tamanho do codigo: LOC, numero de linhas de codigo... geralmente relacionado com a complexidade do codigo e pontos possivelmente alvos de refatoração.

- Complexidade ciclomática: mede a complexidade através de caminhos de execução do codigo que foram gerados por for, while, if e etc... esta relacionado a facilidade de compreender o codigo.

- Profundidade de Aninhamento: Numero de for, if aninhamados e mede também a dificuldade de compreensão do codigo

- Tamanho do nome das variaveis: Nomes mais longos tendem a ser mais intuitivos

-------------------------------------------------------------------------------------------------------------------------
Descreva as diferenças entre métricas estáticas e dinâmicas:

Métricas estáticas são calculadas com objetos como arquivos de codigo e documentação enquanto as dinamicas são calculadas durante a execução dos sistema 

Apresente exemplos de métricas dinâmicas e estáticas:

Estatica: fan-in, fan-out, LOC, profundidade de aninhamento...
Dinamica: Latencia e testes 
-------------------------------------------------------------------------------------------------------------------------

# Métricas de orientação a objetos (OO)

Métricas especificas para sistemas orientados a objetos:

- Profundidade de henrança (DIT): Mede a profundidade que sua classe herda metodos e atributos relação a classe raiz... quanto maior a profundidade maior a complexidade 

- Numero de filhos (NOC): Mede o numero de classes filhas que sua classe tem... quanto maior o numero maior a reutilização de codigo

- Acoplamento entre objetos (CBO): Numero de classes chamada por uma classe, quanto maior mais acoplado (dependente junto) esta o codigo (classes de bibliotecas padrão são ignoradas)

------------------------------------------------------------------------------------------------------------------------

Em UML setas:
- Simples Aberta: Conhece/usa a classe
- Seta com triangulo: Herança (é um)
- Seta com losangulo: Tem um (composição)

              
Classe        DIT  NOC  CBO
Account        1    0    1
ATM            1    0    2
BankDatabase   1    0    3
Logger         1    0    0
Transaction    1    3    0
Withdraw       2    0    1
BalanceInquiry 2    0    2
Deposit        2    0    1

----------------------------------------------------------------------------------------------------------------------------

- Falta de coesão em métodos (LCOM): Numero de atributos que são acessados por unicamente um método da classe... quanto maior menos coeso (faz sentido) o metodo pois se ele usa apenas um atributo então ele esta fazendo talvez algo que não é o proposito.

- Metodos ponderados por classe: Mede o numero de metodos ponderado pela complexidade dos metodos... alto = alta complexidade

- Numero de possiveis metodos (RFC): Numero de possiveis metodos que podem ser invocados na classe... quanto maior mais complexo

Public class A{
    B 

    Metodo A1 {
        B.metodoA2()
    }
} RFC = 2




