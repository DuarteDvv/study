## **O que é teste de software ?**

Uma maneira de evitar que problemas introduzidos durante o desenvolvimento chegue em produção, ou seja, para os usuários finais. Testes permite mensurarar a qualidade do software e evita/reduz erros.

### **Testes manuais x automatizados**

testes podem manuais, como uma pessoa ir e testar a interface, porém isso é caro e pode gerar erros (pois humanos tbm erram) e deve ser reduzido ao maximo dando espaço a testes automatizados que nos permite executar quando e quantas vezes quisermos.
!Legacy code == codigo sem testes!

### **Frameworks**

È preferivel usar frameworks para escrita e execução dos testes pois eles facilitam a escrita, execução rapida de todos os milhares de testes e geração de relatorios. Além disso, eles organizam melhor os testes padronizando em suas proprias classes/metodos e permitem facil execução pelo terminal.

### **Tipos**

- **Unidade:** Testes pequenos de granularidade baixa que geralmente testam um funcionalidade especifica de classes/metodos. (São os mais baratos/rapidos e de maior abundância)

    - **Exercicio:** 
        - Devemos escrever um método de teste para cada método do sistema? Justifique suas reposta. 

            **R:** Não necessariamente. A regra de ouro dos testes de software não é testar métodos, mas sim testar comportamentos e regras de negócio. Métodos simples como getters, setters, ou construtores padrão que apenas atribuem valores a variáveis não precisam de testes unitários isolados, a menos que possuam alguma lógica interna (como uma validação).

        - Por qual razão não existe um teste específico para o método add da classe Count?

            **R:** é um metodo void então não é possivel validar o resultado dele diretamente.


- **Integração:** Testes medios que verificam a integração do sistema com outros serviços como banco de dados, APIs e etc. Mais caros e menos abundantes que de unidade.

- **Sistema:** Simula o sistema na pele do usuário geralmente usando a interface. São mais caros, lentos e menos abundantes ainda que integração.

    - **Exercicio:**
        - Por que o teste e2e test_can_query_on_google é considerado um teste frágil? Apresente três justificativas.

            O código busca a barra de pesquisa usando find_element_by_name("q"). O atributo name="q" é um detalhe de implementação interna do front-end do Google. Se a equipe de desenvolvimento do Google decidir alterar o nome desse elemento o teste quebra.

            O teste pode falhar por motivos que não têm nada a ver com o seu código. Se o Google implementar um mecanismo de CAPTCHA para bloquear automações na rede onde o teste roda, se a internet oscilar, ou se o navegador Firefox atualizar em segundo plano e quebrar a compatibilidade com a versão do Selenium, o teste vai falhar.

            Ele não aguenta oscilações na rede. Se a requisição para o Google demorar 50ms a mais para carregar em uma execução devido à latência da internet ou lentidão no servidor, o Selenium tentará ler self.driver.title antes da nova página carregar por completo.

### **Beneficios**

- **Prevenir erros:** Ajuda a detectar erros mais rapido, ajuda a evitar inserção de erros.

- **Aumentar produtividade:** Mais confiança ao codar pois os testes ajudam e encontrar erros rapidamente e deploy mais rapido.

- **Melhorar o sistema:** Codigos que são dificeis de serem testados é um sinal de que o codigo esta ruim







