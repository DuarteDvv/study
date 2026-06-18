## **Test smells**

Assim como code smells sao caracteristicas que indicam que uma parte de um codigo precisa ser refatorada, test smalls segue a mesma logica sendo caracteristicas nos testes que indicam necessidade de refatoracao.

### **Alguns tipos:**

- **Conditional Test Logic:** Testes nao devem ter estruturas de controle como if/else pois isso implica que o resultado nao é previsivel além de aumentar a complexidade do teste.

- **Exception Handling:** O teste nao deve depender de excecao padrao da linguagem e sim utilizar as ferramentas disponibilizadas pelo framework de testes. (Pode-se também dividir entre testes com e sem excecao)

- **General Fixture:** Durante a preparacao do fixture (SetUp) instanciamos objetos que nao serao usados por todos os testes. Isso gera uso desnecessário de memória durante os testes.

- **Mystery Guest:** Teste utiliza recursos externos do sistema como arquivos e banco de dados o que pode tornar o teste lento e instavel (mudanca sempre que o arquivo mudar). (esse smell só faz sentido em testes de unidade)

- **Redundant Print:** Testes nao devem ter prints pois o resultado do teste é sempre passou/nao passou

- **Unknown Test:** Teste sem asserts que sempre vao passar caso nao tenham uma excecao.

