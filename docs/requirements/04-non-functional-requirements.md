# Requisitos Não Funcionais — InvestTrack

**Versão:** 1.0  
**Status:** baseline para desenvolvimento.

## RNF-001 — Segurança de senha
- Utilizar ASP.NET Core Identity para hashing e validação de credenciais.
- Nunca armazenar ou registrar senha em texto puro.
- Não implementar algoritmo próprio de hash.
- Regras de senha: mínimo 8, máximo 128 caracteres; não exigir classe específica de caracteres.
- Bloquear a conta por 15 minutos após 5 tentativas inválidas consecutivas.

## RNF-002 — Transporte seguro
- HTTPS obrigatório em produção.
- Cookies de autenticação com HttpOnly, Secure e política SameSite adequada à implantação same-site.
- Não armazenar credenciais ou tokens de sessão em localStorage.
- Requisições que alteram dados devem possuir proteção antiforgery quando autenticadas por cookie.

## RNF-003 — Autorização e isolamento
- Autenticação obrigatória em todos os endpoints privados.
- Propriedade dos recursos validada no backend.
- Dados de um usuário nunca podem ser inferidos ou alterados por meio de IDs fornecidos por outro usuário.
- Responder 404 para recursos de terceiros ou inexistentes.

## RNF-004 — Sessão única
- Identificador da sessão ativa mantido no servidor.
- Validar identificador em cada requisição autenticada.
- Sessão anterior invalidada após novo login.
- Expiração por inatividade em 30 minutos e expiração absoluta em 12 horas.

## RNF-005 — Validação
- Validar todos os dados recebidos da API no backend.
- Nunca confiar em cálculos, saldos, propriedade ou totais enviados pelo frontend.
- Mensagens de erro não devem revelar dados internos, existência de conta ou detalhes de implementação.

## RNF-006 — Formato e precisão
- Usar decimal no C# e numeric(18,6) no PostgreSQL para quantidades, preços unitários e valores calculados persistidos.
- Não utilizar float ou double para cálculos financeiros.
- Exibir moeda com duas casas decimais no frontend; manter precisão de cálculo no backend.
- Armazenar instantes técnicos em UTC.
- Usar DateOnly para data de operação, data de preço e data de snapshot.
- Fuso de negócio: America/Sao_Paulo.

## RNF-007 — API e erros
- API REST com JSON.
- Versionar rotas sob /api/v1.
- Utilizar códigos HTTP coerentes.
- Formato de erro padronizado com ProblemDetails, incluindo código de negócio estável.
- Nunca retornar stack trace ou detalhes internos em produção.

## RNF-008 — Desempenho
- Endpoints de listagem devem usar paginação.
- Tamanho padrão de página: 20; máximo: 100.
- Aplicar índices para chaves estrangeiras, filtros frequentes e restrições únicas.
- Objetivo inicial em ambiente de referência: 95% das consultas comuns concluídas em até 500 ms, excluindo processamento de snapshots e consultas históricas extensas.
- A meta é um objetivo de teste inicial, não um SLA de disponibilidade.

## RNF-009 — Consistência transacional
- Inclusão, edição e exclusão de movimentações, recálculo de saldos e snapshots afetados devem ocorrer em transação.
- Uma operação rejeitada não pode deixar dados parcialmente persistidos.
- Restrições únicas relevantes devem ser garantidas também no banco de dados.

## RNF-010 — Observabilidade
- Utilizar logging estruturado.
- Registrar falhas técnicas e eventos operacionais relevantes.
- Nunca registrar senhas, cookies, tokens, cabeçalhos de autorização ou dados financeiros completos sem necessidade.
- Incluir identificador de correlação da requisição quando disponível.

## RNF-011 — Configuração e segredos
- Connection strings e segredos fora do código-fonte.
- Utilizar configuração por ambiente e User Secrets no desenvolvimento local.
- Fornecer .env.example somente com nomes e valores fictícios, caso variáveis de ambiente sejam utilizadas.
- Não versionar arquivos de segredo.

## RNF-012 — Persistência e migrações
- PostgreSQL como banco de dados.
- EF Core Migrations para evolução do esquema.
- Migrações devem ser versionadas no repositório.
- Banco local deve poder ser iniciado com Docker Compose.

## RNF-013 — Testabilidade
- Regras de negócio devem ser testáveis sem iniciar a aplicação Web.
- Testes unitários para cálculos e validações.
- Testes de integração para autenticação, persistência, autorização e endpoints.
- Testes de integração devem usar PostgreSQL, não substituir o banco por SQLite em memória.

## RNF-014 — Manutenibilidade
- Solução organizada em camadas com responsabilidades claras.
- Evitar microserviços, CQRS, MediatR, repositórios genéricos e abstrações que não resolvam uma necessidade concreta da V1.
- Manter documentação e contratos sincronizados com a implementação.
