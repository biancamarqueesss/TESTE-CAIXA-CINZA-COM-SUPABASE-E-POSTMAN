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
Como o projeto foi criado:

1. Acesso ao painel em <https://supabase.com/dashboard>
2. Criação de uma conta
3. Clicando em "New project", na organização pessoal.
- Name: `Teste-caixa-cinza-BiancaMarques`
  Tipo de autenticação utilizada: Foi utilizada a autenticação por e-mail e senha
  O login ocorre via fluxo `grant_type=password`, no qual o cliente envia e-mail e senha e recebe um **JWT** (`access_token`) e um `refresh_token`.

Como o usuário foi criado:
O usuário de teste foi criado manualmente em **Authentication → Users → Add user → Create new user**, com a opção **Auto Confirm User** marcada (para que o e-mail não precise ser confirmado antes do login):
- E-mail do usuário: aluno.teste.cinza@example.com
- senha: Teste@Cinza2026

Credenciais da API:
As credenciais necessárias para os testes foram obtidas em **Project Settings → API Keys** (a URL também aparece em **Connect**)

Objetivo da configuração:
Criar um serviço de autenticação real e controlado, com um usuário válido conhecido, para que fosse possível testar tanto o caminho de sucesso (login válido) quanto os caminhos de falha (senha errada, usuário inexistente, campos vazios etc.) em uma API HTTP de verdade.
Evidências:
