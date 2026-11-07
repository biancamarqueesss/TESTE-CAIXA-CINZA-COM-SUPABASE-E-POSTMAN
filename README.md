# TESTE-CAIXA-CINZA-COM-SUPABASE-E-POSTMAN
BIANCA FRANÇA MARQUES RA:250784

# Introdução
Este repositório documenta a aplicação de testes de caixa cinza em uma API de autenticação. O ambiente de autenticação foi criado na plataforma Supabase e os testes foram executados com o Postman Desktop, sem interface gráfica: toda a validação foi feita diretamente sobre requisições HTTP.
Os testes caixa cinza são importantes em APIs porque combinam as vantagens dos outros dois enfoques:
- Cobertura mais inteligente que a caixa preta:conhecendo endpoint, headers, parâmetros e contratos de resposta, o testador projeta casos direcionados (ex.: header ausente, chave inválida, body vazio) em vez de apenas "tentar às cegas".
- Mais realistas e rápidos que a caixa branca:não exigem acesso ao código, apenas à documentação/contrato da API, o que reproduz a visão de um cliente integrador ou de um QA.
- Validam contrato e segurança: verificam códigos HTTP, estrutura do JSON, mensagens de erro e a qualidade do token retornado, além de detectar vazamento de informação (ex.: mensagens que revelam se um e-mail existe).
- Detectam falhas de integração: APIs são consumidas por outros sistemas; erros de contrato (status errado, campo faltando) quebram clientes mesmo que o código "funcione".

# Objetivo da Atividade
Aplicar testes de caixa cinza em uma API de autenticação, utilizando ferramentas modernas de mercado, com os seguintes objetivos específicos:

1. Configurar um ambiente de autenticação utilizando o Supabase;
2. Criar usuários para testes;
3. Compreender o funcionamento de requisições HTTP (método, URL, headers, body, status e resposta);
4. Utilizar o Postman para execução dos testes;
5. Validar as respostas de autenticação (status, JSON, mensagens de erro e token);
6. Documentar tecnicamente todo o processo neste `README.md`.

# Configuração do Supabase

## Como o projeto foi criado

1. Criação de um novo projeto
   - **Name:** `Teste-caixa-cinza-BiancaMarques`

## Tipo de autenticação utilizada

Foi utilizada a autenticação por **e-mail e senha** (provider *Email* do Supabase Auth), habilitada em **Authentication → Sign In / Providers → Email**. O login ocorre via fluxo `grant_type=password`, no qual o cliente envia e-mail e senha e recebe um **JWT** (`access_token`) e um `refresh_token`.

## Como o usuário foi criado

O usuário de teste foi criado manualmente em **Authentication → Users → Add user → Create new user**, com a opção **Auto Confirm User** marcada (para que o e-mail não precise ser confirmado antes do login):

| Campo | Valor |
|---|---|
| E-mail | `aluno.teste.cinza@example.com` |
| Senha | `Teste@Cinza2026` |

> Usuário e senha exclusivos para esta atividade, sem uso em outros sistemas.

## Credenciais da API

As credenciais necessárias para os testes foram obtidas em **Project Settings → API Keys** (a URL também aparece em **Connect**):

| Informação | Valor |
|---|---|
| API URL (`base_url`) | `[https://[PROJECT-REF].supabase.co](https://nzwwzfpejxrkvrihyvcy.supabase.co` |
| API Key (`api_key`) | `XXXXX` (omitida por segurança) |

## Objetivo da configuração

Criar um serviço de autenticação real e controlado, com um usuário válido conhecido, para que fosse possível testar tanto o caminho de sucesso (login válido) quanto os caminhos de falha (senha errada, usuário inexistente, campos vazios etc.) em uma API HTTP de verdade.

# Configuração do Postman

## Como o Workspace foi criado

No Postman Desktop, em **Workspaces → Create Workspace**, foi criado o workspace, ID:7d149b0c-b28f-4669-81c1-441da36ad80c
link: https://biancamarqueesss-5394364.postman.co/workspace/7d149b0c-b28f-4669-81c1-441da36ad80c

## Como o Environment foi configurado

Em **Environments → Create Environment**, foi criado o ambiente `Supabase Auth - Caixa Cinza` com as variáveis abaixo, e ele foi selecionado como ambiente ativo (canto superior direito do Postman).

| Variável | Descrição | Valor |
|---|---|---|
| `base_url` | URL da API do Supabase | `https://[PROJECT-REF].supabase.co` |
| `api_key` | API Key do projeto | `XXXXX` (omitida) |
| `email` | E-mail do usuário de teste | `aluno.teste.cinza@example.com` |
| `password` | Senha do usuário de teste | `Teste@Cinza2026` |

## Finalidade das variáveis

Usar variáveis evita repetir valores em cada requisição, reduz erros de digitação e permite trocar de projeto/usuário alterando um único lugar. As requisições referenciam os valores no formato `{{base_url}}`, `{{api_key}}`, `{{email}}` e `{{password}}`. Também mantém a chave fora das requisições e do código versionado.

## Como o Postman será utilizado nos testes

O Postman monta e envia as requisições HTTP ao endpoint de autenticação, exibe status, tempo, headers e JSON de resposta e permite escrever **scripts de teste** (aba *Tests*) com asserções automáticas sobre status e conteúdo.

# Configuração das Requisições

## Dados da requisição

| Item | Valor |
|---|---|
| Método HTTP | `POST` |
| Endpoint | `{{base_url}}/auth/v1/token?grant_type=password` |
| Query param | `grant_type=password` |
| Finalidade | Autenticar um usuário por e-mail e senha e obter o `access_token` (JWT) |

## Headers utilizados

| Header | Valor |
|---|---|
| `apikey` | `XXXXX` (valor de `{{api_key}}`) |
| `Authorization` | `Bearer XXXXX` (valor de `Bearer {{api_key}}`) |
| `Content-Type` | `application/json` |

## Body JSON utilizado

```json
{
  "email": "usuario@email.com",
  "password": "123456"
}
```

Na requisição real do Postman, os valores são `{{email}}` e `{{password}}`:

```json
{
  "email": "{{email}}",
  "password": "{{password}}"
}
```

## Exemplo de resposta de sucesso (resumida)

```json
{
  "access_token": "eyJhbGciOi...",
  "token_type": "bearer",
  "expires_in": 3600,
  "refresh_token": "xxxxxxxx",
  "user": { "id": "...", "email": "aluno.teste.cinza@example.com", "role": "authenticated" }
}
```

# Execução dos Testes

Foram executados os cinco cenários obrigatórios e dois cenários adicionais de caixa cinza, explorando o conhecimento sobre o header `apikey`. Em todos os casos foram validados: **status HTTP, corpo JSON, mensagem de erro, access token e comportamento da autenticação**.

## Tabela de cenários

| ID | Cenário | Entrada Utilizada | Resultado Esperado | Resultado Obtido | Status |
|---|---|---|---|---|---|
| CT01 | Login válido | `aluno.teste.cinza@example.com` / `Teste@Cinza2026` | Status 200 + `access_token` | 200 OK (1,44 s); JSON com `access_token` (JWT), `token_type: bearer`, `expires_in`, `refresh_token`; 8/8 testes automáticos aprovados | OK |
| CT02 | Senha incorreta | `aluno.teste.cinza@example.com` / `SenhaErrada@999` | Status 400 + erro de autenticação (`invalid_credentials`) | 400 Bad Request (1,29 s); `error_code: invalid_credentials`, `msg: Invalid login credentials`; sem token | OK |
| CT03 | Usuário inexistente | `nao.existe.cinza@example.com` / `Teste@Cinza2026` | Status 400 + acesso negado, sem token | 400 Bad Request (930 ms); `invalid_credentials` / `Invalid login credentials`; sem token | OK |
| CT04 | Campos vazios | `""` / `""` | Status 4xx + erro de validação | 400 Bad Request (777 ms); `error_code: validation_failed`, `msg: missing email or phone` | OK |
| CT05 | Credenciais inválidas | `email-invalido` / `123` | Status 4xx + mensagem de erro | 400 Bad Request (422 ms); `invalid_credentials` / `Invalid login credentials` | OK |
| CT06 | API key inválida (extra) | apikey `chave-invalida-123` + credenciais corretas | Status 401 + mensagem de chave inválida | 401 Unauthorized (47 ms); `message: Invalid API key`, com `hint` pedindo para conferir a chave anon ou service_role | OK |
| CT07 | Sem API key (extra) | Sem headers `apikey` e `Authorization` | Status 401 + mensagem de chave ausente | 401 Unauthorized (121 ms); `message: No API key found in request`, com `hint` informando que não há header apikey | OK |

## Descrição da execução

Cada cenário foi executado no Postman duplicando a requisição base (ou usando a coleção importada), alterando apenas a entrada que caracteriza o cenário, e verificando a aba **Test Results**.

- **CT01:** retornou 200 OK em 1,44 s (corpo de 2,08 KB) com `access_token`, `token_type` (`bearer`), `expires_in`, `refresh_token` e o objeto `user`. Os 8 testes automáticos passaram.
- **CT02:** retornou 400 Bad Request em 1,29 s com `code: 400`, `error_code: invalid_credentials` e `msg: Invalid login credentials`. Sem token. 6/6 testes passaram.
- **CT03:** retornou 400 Bad Request em 930 ms com `invalid_credentials` / `Invalid login credentials`. Sem token. 5/5 testes passaram.
- **CT04:** retornou 400 Bad Request em 777 ms com `error_code: validation_failed` e `msg: missing email or phone`. 5/5 testes passaram.
- **CT05:** retornou 400 Bad Request em 422 ms com `invalid_credentials` / `Invalid login credentials`. 5/5 testes passaram.
- **CT06:** retornou 401 Unauthorized em 47 ms com `message: Invalid API key` e um `hint` pedindo para conferir a chave anon ou service_role. 5/5 testes passaram.
- **CT07:** retornou 401 Unauthorized em 121 ms com `message: No API key found in request` e um `hint` informando que nenhum header `apikey` foi encontrado. 5/5 testes passaram.

## Erros encontrados

Nenhuma divergência encontrada: em todos os cenários o status e o corpo da resposta coincidiram com o esperado, e todos os testes automáticos do Postman foram aprovados.


# Registro dos Testes

## Como os testes foram registrados

Cada execução foi registrada na planilha.

## Importância da documentação dos testes

Registrar os testes dá **rastreabilidade** (o que foi testado, com quais dados e o que aconteceu), permite **reprodução** por outra pessoa, serve como **base de regressão** para reexecutar após mudanças e comprova a **cobertura** dos cenários. Sem registro, falhas encontradas não podem ser comunicadas nem corrigidas de forma confiável.

## Falhas identificadas

Nenhuma falha identificada: os 7 cenários retornaram o resultado esperado.

# Resultados Obtidos

| Indicador | Valor |
|---|---|
| Total de cenários executados | 7 |
| Cenários com resultado esperado | 7 |
| Cenários com falha | 0 |

**Principais observações técnicas:**

- O login válido retornou **200 OK** com `access_token` do tipo JWT e `token_type` `bearer`;
- Senha incorreta e usuário inexistente devem retornar a **mesma mensagem genérica**, o que é uma boa prática de segurança: impede que um atacante descubra se um e-mail está cadastrado (*user enumeration*). isso foi confirmado: o CT02 e o CT03 retornaram a mesma resposta (`invalid_credentials` / "Invalid login credentials");
- Requisições sem `apikey` ou com chave inválida são barradas **antes** de chegar à lógica de login (resposta 401 do gateway), o que mostra duas camadas de proteção: a chave do projeto e as credenciais do usuário;
- Campos vazios (CT04) geraram um erro de validação próprio (`validation_failed`, "missing email or phone"), diferente do erro de credenciais;
- Um e-mail mal formatado (CT05) não gerou erro de validação de formato: foi tratado como credencial inválida (`invalid_credentials`);
- As respostas 401 do gateway (CT06 e CT07: 47 ms e 121 ms) foram mais rápidas que as respostas do serviço de autenticação (422 ms a 1,44 s), o que é compatível com a rejeição ocorrendo antes de a requisição chegar à lógica de login.

---

# Conclusão

**Os testes foram executados corretamente?** Sim. Os sete cenários (cinco obrigatórios e dois extras) foram executados no Postman, com scripts de validação automática, e registrados na planilha.

**A autenticação funcionou?** Sim. O login válido retornou 200 com `access_token` JWT (role `authenticated`) e todos os cenários inválidos foram rejeitados com 400 ou 401 e mensagens adequadas, sem emissão de token.

**Dificuldades encontradas:** `Como se trata de algo novo sendo estudado, tive dificuldade em compreender alguns termos e processos, mas após pesquisar mais a fundo, pude compreender e finalizar os testes`

**Falhas identificadas:** Nenhuma falha identificada; todos os resultados obtidos coincidiram com os esperados.

**Melhorias que poderiam ser implementadas:**

- Automatizar a coleção no **CI/CD** com o **Newman** (CLI do Postman) para regressão a cada alteração;
- Incluir testes de **limite de tentativas** (rate limiting / força bruta) e de **expiração/renovação do token** (`refresh_token`);
- Testar políticas de senha (tamanho mínimo, complexidade) e e-mails com formatos extremos;
- Incluir testes de segurança (injeção em campos, tokens adulterados ou expirados);
- Usar dados de teste gerados dinamicamente e ambientes separados (desenvolvimento/homologação).

**Importância dos testes caixa cinza:** mostraram-se adequados para APIs de autenticação, pois o conhecimento prévio de endpoint, headers e contrato de resposta permitiu criar casos direcionados e validar com precisão status, mensagens e token, sem necessidade de acesso ao código-fonte.
