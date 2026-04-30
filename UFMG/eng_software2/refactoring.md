## Refatoracao de codigo

Mudança/transformação no codigo que visa aumentar a capacidade de manutenção do codigo (melhorando a qualidade) sem alterar a logica. Existem varias maneiras de refatorar um codigo... eis as 7 principais:

- Extração de metodo: Tiramos um trecho de codigo de um metodo A e levamos para um metodo B (novo ou não)... agora A chama o metodo B. Essa refatoração é muitas vezes chamada de canivete suiço pois ajuda em diversas situações como decompor metodos longos, modularização e remoção de duplicata de codigo.

- Inline Method: Contrário de extração... aqui pegamos metodos muito simples e que são usados poucas vezes e colocamos dentro de outro metodo que chama ele. Basicamente não existe mais metodo B e o codigo volta para o metodo A.

- Move Method: Muitas vezes um metodo esta na classe errada pois usa mais coisas de outra classe que daquela... essa refatoração consiste em trocar o metodo de classe.

- Pull-up Method: Se varias subclasses tem um metodo, ele deveria ser da classe pai (concede acesso a todos os filhos e evita duplicação de codigo)

- Pull-down Method: Puxar um metodo do pai para os filhos quando eles tem implementações diferentes

- Class Extract: Quando uma classe (muito longa) tem muitas responsabilidades e atributos podemos extrair uma dessas responsabilidades em outra classe.

- Rename: Trocar o nome da variavel/metodo... mas a parte complexa não é o novo nome mas sim encontrar e trocar todas as referencias do nome anterior. Se seu metodo f que quer trocar o nome para g é muito usado no codigo inteiro, pode ser melhor manter as referencias antigas de f, mandar a lógica para g e dentro de f chamar g para manter funcionando.

    void g() { // novo nome do método
        // A
    }

    @deprecated
    void f() { 
        g(); 
    }


- Outros: Extração de variavel que consistem em extrair calculos explicitos como a*b/c por uma variavel calculo =   a*b/c e remover if's aninhados usando clausulas guarda (basicamente simplificar as condicionais)
    