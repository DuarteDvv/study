## **Pq importa ?**

Ao criar um endpoint de inferencia e deixar em um servidor com IP publico sem nenhuma protecao, qualquer pessoa no mundo pode:

- **Chamar o modelo infinitamente**, gerando gastos de inferencia
- Fazer **Scrapping dos parametros** do modelo (Treinar com entradas e saidas da API)
- **Sobrecarregar o servidor** através de DoS 
- Usar vulnerabilidades para **conseguir dados sensiveis**

Como geralmente os endpoints de ML sao muito computacionalmente caros, isso se torna ainda mais essencial.

## **API Keys**

Chaves de API é a forma mais simples de protecao em que cada cliente permitido **gera sua propria string e é obrigado e enviar ela a cada requisicao** para poder provar sua permissao de acesso.

Algo como:

```python

from fastapi import FastAPI, Header, HTTPException

app = FastAPI()

VALID_API_KEYS = {"sk-abc123", "sk-xyz789"}  # normalmente vem de um DB/secrets manager

@app.post("/predict")
async def predict(data: dict, x_api_key: str = Header(...)):
    if x_api_key not in VALID_API_KEYS:
        raise HTTPException(status_code=401, detail="API key inválida")
    
    # sua lógica de inferência aqui
    return {"prediction": "..."}

```
Boas praticas:

- Uma chave por cliente para poder saber quem gera trafego
- Nunca deixar Hardcoded 

Para APIs internas (service to service na propria infra) ou MVPs uma chave de API sozinha resolve a maioria dos problemas mas para usuários reais de toda internet existem outros riscos.

## **Autenticação vs Autorização**

- **Autenticacao:** cuida de descobrir quem é voce através de Login/Senha e chaves de API
- **Autorizacao:** vem depois que sabemos quem é voce e lida com que permissoes voce tem ou oq voce pode fazer no sistema (Especialmente util quando existem planos diferentes com acessos diferentes)

## **JWT (JSON Web Token)**

Quando é um usuário final fazendo login apenas uma chave de API nao é suficiente pois ela nao expira (entao se vazar acabou) e nao carrega informacoes sobre o usuário (como quem ele é oq pode fazer). Nesses casos o ideal é uma logica de autenticacao através de token. 

O JWT é um token que **carrega informacoes codificadas (nao criptografado, nao pode ter informacoes sensiveis)** (nome, tier, permissões, tempo de expiração) dentro dele mesmo. Ele é uma string dividida em 3 partes em que a primeira é o header, segunda sao os dados e a terceira é uma assinatura criptografada pelo servidor para saber que nao foi alterado.

Se decodificar os dados algo assim aparece: 

```json
{
  "user_id": 123,
  "tier": "pro",
  "exp": 1735689600
}
```

isso é util para nao ser necessário buscar no banco essas informacoes do token a cada requisicao enquanto o token é util para nao ser necessário fazer login sempre.

**Fluxo:**

1. Cliente envia credenciais (email/senha, ou OAuth de terceiros tipo Google) e servidor valida contra o banco de dados
2. Servidor cria o JWT, assinando com uma chave secreta (SECRET_KEY) que só ele conhece, coloca no payload as infos relevantes e servidor devolve o token pro cliente
3. cliente guarda o token (geralmente em memória, cookie httpOnly, ou localStorage — cada um com trade-offs de segurança) e envia em toda requisição futura no header
4. Validação (no servidor, a cada requisição): 
    - Verifica se a assinatura bate (ninguém alterou o conteúdo), é aqui que o SECRET_KEY entra, só quem tem a chave consegue gerar uma assinatura válida (uma chave de criptografia)
    - Decodifica o payload pra saber quem é o usuário
    - Checa se exp (expiração) já passou

## **Rate Limiting**

Rate Limiting serve para limitar **quantas requisicoes um cliente pode fazer em um intervalo de tempo**. Util para evitar ataques que podem sair caro em custo computacional. 

**Estratégias comuns:**

- **Janelas Fixas:** Maximo de 100 requisicoes em 1 min e reseta assim que o minuto acabar. Problema é se o usuário fizer 100 requisicoes no ultimo segundo e depois 100 requisicoes no primeiro segundo, teremos 200 requisicoes em 2 segundos.

- **Janela Deslizante:** Considera o limite nos ultimos N segundos movendo a janela. Mais caro de se fazer.

- **Token Bucket (contador):** Usuário tem N requisicoes disponiveis e a cada uma esse numero reduz em 1. O numero cresce em intervalos espacados e com valores definidos pelo servidor.

Esses limites nao precism ser apenas por IP mas podems ser por **usuário, tier de servico ou ate mesmo por custo** que é oq acontece no claude pois uma unica requisicao com 50000 tokens é mais cara que 50 requisicoes com 50 tokens. 

O limite nao precisa ser implementado especificamente no servidor mas pode ser feito antes de chegar no servidor através de proxys como API Gateway.

## **CORS**

Por padrão, navegadores têm uma regra de segurança chamada **Same-Origin Policy (Política de Mesma Origem)**: uma página só pode fazer requisições JavaScript pra APIs que estão na **mesma origem** (mesmo domínio, protocolo e porta). Imagine que você está logado no seu banco (banco.com) numa aba, e em outra aba visita um site malicioso (hacker.com). Sem Same-Origin Policy, o JavaScript do **site malicioso poderia fazer uma requisição pro seu banco usando seus cookies de sessão** (que o navegador enviaria automaticamente) e roubar seus dados ou fazer transações. A Same-Origin Policy impede isso por padrão.

Isso vira um problema quando um frontend por exemplo hospedado em uma origem tenta buscar conteudo no servidor que estar em outra origem (microservices). Por padrao, o navegador nao vai permitir que o frontend faca requisicao no backend.

### **Como o CORS resolve?**

O CORS é um mecanismo que permite o servidor **avisar explicitamente o navegador quais origens tem permissao de fazer requisicoes de metodos HTTPS especificos**. Ele sempre faz a requisicao porém só libera a resposta se as origens e permissoes baterem.

## **HTTPS/TLS**

**HTTPS = HTTP + TLS**

- HTTP é um protocolo de transferencia de dados (usado em APIs REST) que transfere dados em texto puro JSON.

- TLS é a camada de criptografia que permite que apenas o servidor final consiga interpretar os dados mesmo que sejam interceptados na rede.

**Fluxo do TLS:**

1. Cliente inicia a comunicacao dizendo quais algoritmos de criptografia ele suporta
2. Servidor escolhe o algoritmo e retorna o seu certificado de seguranca (identidade SSL)
3. Cliente verifica se o certificado é válido (emitido por uma autoridade confiável)
4. Cliente e servidor negociam uma chave de sessão compartilhada
5. A partir daqui, toda comunicação é criptografada com essa chave e o algortimo escolhido

**SSL:** Um certificado TLS/SSL é um arquivo que prova que o servidor é realmente quem diz ser (ex: prova que api-modelo.com é realmente controlado por quem afirma ser). Ele é emitido por uma Autoridade Certificadora (CA) confiável. (Provedores de nuvem fazem isso para voce)

## **Secrets Management**

Se chaves importantes como de APIs, senhas de banco e entre outros vao para o Git/Github especialmente um repositório público, a **chave está permanentemente comprometida**, mesmo que você delete depois, ela fica no histórico do Git para sempre (a não ser que reescreva o histórico, o que é chato e nem sempre possível se já foi clonado). **Existem bots que ficam varrendo GitHub 24/7 procurando exatamente esse tipo de vazamento**.

**Solucao basica:** usar um .env que fica apenas em localmente sem sofre push
**Solucao ideal:** usar um gerenciador de secrets dedicado pois permite ver quem esta usando e alterar para todos facilmente.