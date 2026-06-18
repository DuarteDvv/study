## **Gerencia de configuracao**

### **Motivacao**

O software muda muito durante manutencao/desenvolvimento e por isso é importante gerenciar bem o ambiente de trabalho e sistema trabalhado.

### **Gerencia de config (SCM)**

Uma mudanca gera uma versao nova do sistema, esse conjunto de versoes do sistema precisam ser mantidos e gerenciados. Varias versões do sistema podem estar em desenvolvimento ao mesmo tempo sendo facil perder o controle das alterações entre as versões e acabar trabalhando na versão errada do sistema. Isso é util em projetos pessoais para não esquecer mudanças feitas, projetos com varias pessoas ao mesmo tempo e no Agile é imprescindivel.

### **Quatro atividades**

- **Gerência de versões:** Processo de gerenciar diferentes versões do sistema desenvolvidaas por diferentes pessoas.
    - **Codeline:** Basicamente uma branch no git que esta em constante alteração (dinamico)
    - **Baseline:** Basicamente uma realese, uma foto (estatico) de uma codeline que foi aprovada
    - **Sistema de versionamento:** sistemas que oferecem um repositório para armazenar e recuperar versões recentes/antigas do codigo de um sistema. 
        - **Centralizado (SVN):** Existe um unico servidor (repositório) em que um cliente faz uma copia dos arquivos do servidor (através do update) e envia a versão atualizada de volta ao servidor através do commit.

        - **Distribuido (Git):** Existe um sevidor com o repositorio central em que o cliente clona esse "servidor" para uma maquina local. Essa cópia tem seu proprio controle de versão e repositório em que fazemos operações como commits na versão local e quando quisermos, enviamos as alterações do repositório local para o servidor principal (nuvem). As principais vantagens disso é poder **versionar offline** (localmente), **commits são mais rapidos e constantes** pois são locais, se o sistema principal quebrar existem **N copias locais** não necessáriamente atualizadas e permite a **execução e teste local** sem necessidade de deploy.

- **Construção de sistema:** Processo de criar um sistema completo e executavel através de algum script ou programa. Basicamente subir todos os seus serviços e dependencias de forma automatica.

    - **CI (Continuos Integration):** Pratica em que a cada alteração feita por um desenvolvedor e enviada (push) é automaticamente construida (build) e validada com testes para verificar se tudo esta funcionando (geralmente vai para o servidor CI após um pull request). Isso permite **desenvolvimento mais rapido**, **indentificação rapida de problemas** pois sabemos exatamente em qual commit o problema surgiu e **reduz a complexidade** de deploy. Problemas podem surgir se o sistema é muito grande e portanto build é lento e nem todo teste pode ser executado localmente.

    - **Exercicios:** 

        1. Defina com suas palavras o conceito de “Gerência de Configuração”

            É a prática de organizar, controlar e registrar todas as mudanças no código e na infraestrutura de um software, garantindo que o sistema seja estável, rastreável e repetível.

        2. Quando devemos utilizar ferramentas como CircleCI e GitHub Actions?

            Quando você quer automatizar o processo de build, teste e deploy do software toda vez que um desenvolvedor atualiza o código (Integração Contínua/Entrega Contínua).

        3. Por qual razão pode não ser possível executar todos os testes
        localmente?

            Porque sistemas grandes exigem muito poder de processamento (hardware pesado) ou dependem de ferramentas, dados e serviços na nuvem que não cabem ou não podem ser copiados para um computador comum.

- **Gerencia de mudanças:** Processo de manter controle de componentes alterados, analisar, aprovar e rejeitar mudanças. 

    - Um **gerenciador de issue** por exemplo permite que usuários sugiram alterações e falem de problemas do sistema possibilitanto gerenciar a alteração a partir da issue.

    - **Changelog** é outra opção que mantem o registro de alterações de cada desenvolvedor **(comentarios do commit)**

- **Gerencia de releases:** Processo de manter versões dos sistemas entregues ao cliente (já em produção). Podem ser do tipo com novas funcionalidade ou do tipo correção.

    - Geralmente o versionamento é padronizado em MAJOR.MINOR.PATCH:

        - **MAJOR:** Alterações sem retrocompatibilidade (tem que atualizar)
        - **MINOR:** Novas features com retrocompatibilidade
        - **PATCH:** Correção de bugs

    - Exercicios: 
        1. Apresente 5 possíveis estados de uma issue
            - Backlog (ou Aberta/To Do): A tarefa foi registrada, mas o trabalho ainda não começou. Ela está na fila.

            - Em Progresso (In Progress): Um desenvolvedor assumiu a tarefa e está ativamente escrevendo o código ou trabalhando nela.

            - Em Revisão (In Review / Code Review): O código foi escrito e está sendo avaliado por outros desenvolvedores da equipe para garantir a qualidade.

            - Em Teste (In Testing / QA): A tarefa está sendo validada em um ambiente de testes para garantir que funciona e não gerou novos bugs.

            - Fechada (Done / Closed): A tarefa foi concluída com sucesso, validada e integrada ao sistema principal.

        2. Qual o objetivo do Versionamento Semântico?

            O objetivo principal é comunicar claramente o impacto e o risco de uma atualização para quem utiliza o software (outros desenvolvedores ou sistemas).

    




