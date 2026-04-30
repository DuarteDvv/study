## introdução e manutenção de codigo

Manutenção é uma etapa fundamental que vem após o desenvolvimento, deploy e uso do codigo (software já entregue ao cliente e funcionando...) criado em que cuidamos, corrigimos e melhoramos ele... a maior parte do custo (maior custo) em software esta ligada a sua manutenção uma vez que quando estamos desenvolvendo algo ainda sem estar pronto é muito mais facil adicionar alguma feature/funcionalidade nova pois o sistema ainda não esta maduro. Quando o sistema já tem funcionários e funcionando fica muito mais dificil e caro a manutenção pois qualquer coisa a ser adicionada/alterada existe eximio conhecimento de como aquilo afeta o codigo atual e como isso ira repercurtir em outros componentes do codigo... pense em uma casa pois é mais barato construir um novo quarto que uma casa nova inteira porém para um novo quarto precisamos quebrar a parade (um pedaço do codigo anterior) para adaptar para as mudanças mas sem cortar fios errados ou queimar algo (ou seja, essa manutenção deve respeitar logicas e restrições existentes do codigo atual).

Manutenibilidade de um software é o quao facil e barato é manter ele funcionando e fazer alterações nele... isso não é quantificado em um unico numero mas podem ter dicas (smells) no codigo para identificar problemas de manutenção:

- Fan-in: Fan-in de um modulo do codigo é quantas funções/modulos externos usam esse modulo internamente (quantos fazem import)... um fan-in alto significa que alterar aquele modulo ira cascatear efeitos para muitos outros sendo necessário mais testes e testes mais robustos.

- Fan-out: Fan-out de um modulo é quantas funções/modulos externos que ela depende (seus imports)... um fan-out muito alto siginifica que esse modulo pode ser muito complexo e dependente de outras partes do codigo violando o principio de responsabilidade unica.

- Linhas de codigo: Codigos muito compridos ou funções muito compridas é um bom sinal de complexidade exessiva pois significa pouca modularização.

- Profundidade de aninhamento: Aninhamentos muito profundos de if/for tornam o codigo muito dificil de ser entendido por seres humanos

### Quais fatores levam a necessidade de manutenção do codigo ?

Se um sistema é utilizado ele nunca estará estatico sem alterações... ele sempre irá evoluir: novas funcionalidades aparecem, bugs descobertos, refatoração, migração de bibliotecas/banco de dados e novas regras de negocio.

### Categorias de Manutenção

- Corretiva (Correção): em que corrigimos problemas quando eles aparecem... um pouco perigoso pois nessas correções podemos acabar inserindo novos problemas.
- Preventiva (Correção): tentamos lidar com problemas já antes deles acontecerem de forma preventiva... aqui focamos em melhorar o codigo (Como criar mais testes só por segurança ou limpar/refatorar o codigo)
- Perfectiva/Evolutiva (Melhoria): Aqui melhoramos o sistema não focado no problema mas sim na satisfação do usuário, ou seja, tentando sempre melhorar o sistema, alguma funcionalidade, funcionalidade nova ou design.
- Adaptativa (Melhoria): Aqui são manutenções que acontecem para manter o sistema funcionando quando o ambiente do sistema sofre alguma alteração... se trocamos o SO ou adicionarmos a compatibilidade a ele precisamos talvez de alguma alterações no codigo para continuar funcionando. (aqui melhoramos o sistema em si)

A categoria mais comum é a evolutiva seguida pela adaptativa, corretiva e preventiva. Os tipos de manutenção podem se relacionar entre si... por exemplo ao melhorar uma funcionalidade (Manutenção Evolutiva) podemos acaber introduzindo erros que poderão ser indentificados futuramente em uma manutenção Corretiva. Em geral, as manutenções de melhoria acabam gerando sem querer problemas que as manutenções de correção irão corrigir. A manutenção corretiva e evolutiva afetam diretamente o usuário pois ele ira perceber que a funcionalidade ficou mais rapida ou que o site voltou a funcionar... já as preventivas e adaptativa não chegam a afetar a vida do usuário e portanto tera efeito indireto nele.

### Exercicios e respostas

Considere as seguintes afirmações sobre Manutenção de Software.
I - Manutenção de software é o processo geral de mudança em um sistema depois de liberado para uso.
II - As pesquisas concordam que a manutenção de software ocupa uma proporção menor dos orçamentos
de TI do que o desenvolvimento e, portanto, os esforços durante o desenvolvimento do sistema para
produção de um sistema manutenível não reduzem os custos gerais durante a vida útil do sistema.
III - Existem apenas três diferentes tipos de manutenção de software: (1) correção de defeitos; (2)
adaptação ambiental (quando algum aspecto do ambiente – tal como hardware, plataforma do sistema
operacional ou outro software de apoio – sofre uma mudança); e (3) adição de funcionalidade.
Quais estão corretas?
a) Apenas I. <- 2 errada pois manutenção é a parte mais cara para se manter o sistema. 3 errada pois existe também a manutenção preventiva
b) Apenas I e II.
c) Apenas I e III.
d) Apenas II e III.
e) I, II e III.

Para cada solicitação abaixo, apresente sua categoria de manutenção:
a) Migrar o framework de teste JUnit para a versão 5.0 -> Adaptativa , pois mudamos uma biblioteca e temos que adaptar o codigo
b) Atualizar as regras de cálculo do IPTU em razão de alteração na lei municipal -> Adaptativa, o ambiente mudei e nos vamos mudar para continuar funcionando
c) Melhorar o código da classe HttpHandlerAutoConfiguration -> Preventiva, estamos melhorando o codigo
d) Corrigir exceção ao digitar caractere chines no input 12 -> Corretiva, o problema já aconteceu e estamos corrigindo
e) Função para relatório de compras por ano -> Evolutiva, estamos melhorando o sistema 

### Leis de Lehman

existem 8 leis de lehman mas as 4 principais são:

- Codigo deve ser alterado: O codigo tem que evoluir/adaptar junto com seus usuários para responder a demanda de todos
- Complexidade do codigo vai crescer: Se nada for feito, a complexidade do codigo tende a continuar crescendo indefinidamente
- Codigo deve crescer: Novas funcionalidades devem ser feitas para agradar os usuários
- Codigo perde a qualidade: O codigo perde muita qualidade conforme tempo passa e portanto é necessário atualizar e refatorar de vez enquando para manter atualizado com os usuários.

### Atividades de Manutenção

Algumas ações/atividades tipicas de manutenção de codigo:

- Analise de impacto: Analisar areas afetadas por uma alteração
- Refatoração de codigo: Melhorar codigo antigo
- Engenharia reversa: Reconstruir a ideia através do codigo
- Reengenharia: Identificação e analises de problemas seguido por uma reimplementação do sistema com todas as funcionalidades antigas e correções (especialmente util em sistemas legado em que o codigo é muito valioso mas esta ultrapassado... reduzimos a complexidade do codigo, riscos e custos futuros)

### Exercicios e respostas

1: Interpretação da imagem -> As duas curvas mostram que um software novo tera grande parte do seu custo na implementação inicial e preparação para o publico mas um software já velho e integrado tem maior custo na manutenção.

1. Para cada alteração abaixo, apresente sua categoria de manutenção:
1) Ajustar uma função que calcula impostos incorretamente devido a uma fórmula errada. -> Corretivo, tinha um erro
2) Resolver uma falha de segurança que permite acesso não autorizado a dados confidenciais. -> Corretivo, resolver falha
3) Corrigir um problema que impede o processamento de pagamentos ao finalizar a compra. -> Corretivo, resolver problema
4) Adicionar um chatbot de atendimento ao cliente para responder perguntas frequentes dentro do aplicativo. -> Evolutivo, melhoria para o cliente
5) Incluir uma funcionalidade de recomendação de produtos com base no histórico de compras do usuário. -> Evolutivo, melhoria para o cliente
6) Adicionar um calendário compartilhado em um sistema de gestão de projetos. -> Evolutivo, melhoria do sistema
7) Resolver uma falha que impede o carregamento de arquivos devido a problemas de compatibilidade com o navegador. -> Adaptativa, adaptar ao navegador
8) Corrigir uma funcionalidade de login social que falha ao tentar autenticar com uma conta externa. -> Corretiva
9) Adicionar um novo método de pagamento em um sistema de e-commerce. -> Evolutiva
10) Melhorar app de entrega com integração de API de geolocalização para permitir localização em tempo real. -> Evolutiva, melhoria do sistema

2. Para cada alteração abaixo, apresente sua categoria de manutenção:
1) Refatorar classe com código redundante para reduzir duplicação. -> preventiva
2) Remover código duplicado criando métodos mais reutilizáveis. -> preventiva
3) Alterar o software para ser compatível com uma nova versão do sistema operacional. -> adaptativa
4) Adaptar o sistema para lidar com novos requisitos fiscais impostos por uma mudança na legislação de impostos. -> Adaptativa
5) Adaptar o código para lidar com um novo formato de dados em uma API que passou de XML para JSON. -> Adaptativa
6) Ajustar as configurações de armazenamento de arquivos após migração para um novo serviço de nuvem. -> Adaptativa
7) Adaptar o sistema para suportar o uso de autenticação biométrica, em resposta a novos requisitos de segurança. -> Adaptativa
8) Refatorar o código de validação de formulários para torná-lo mais testável. -> Preventiva
9) Dividir funções longas e complexas em funções menores e mais focadas para melhorar a legibilidade e o reúso. -> preventiva
10) Renomear variáveis e métodos para nomes mais significativos. -> preventiva