# Arquitetura do Sistema — InvestTrack Backend

**Versão:** 1.0  
**Status:** baseline técnica para a V1.

## 1. Estilo arquitetural

Monólito modular organizado em camadas: uma única aplicação ASP.NET Core Web API, um banco PostgreSQL e um processo interno para snapshots diários.

Não criar microserviços, filas, CQRS, MediatR ou repositórios genéricos na V1.

## 2. Projetos da solução

- **InvestTrack.Api:** endpoints HTTP, DTOs, autenticação, autorização, cookies, antiforgery, CORS, OpenAPI, middleware, DI e tratamento de erros.
- **InvestTrack.Domain:** entidades, enums, regras de negócio, cálculos financeiros e exceções de domínio. Não depende de ASP.NET Core, EF Core ou PostgreSQL.
- **InvestTrack.Infrastructure:** DbContext, mapeamentos EF Core, persistência PostgreSQL, ASP.NET Core Identity e geração/persistência de snapshots.
- **InvestTrack.UnitTests:** testes unitários do domínio e cálculos.
- **InvestTrack.IntegrationTests:** testes de integração com PostgreSQL.

## 2.1 Estrutura interna inicial dos projetos

Manter a organização pequena e crescer somente quando necessário:

InvestTrack.Api:
- Controllers/ ou Endpoints/ por recurso.
- Contracts/Requests e Contracts/Responses para DTOs.
- Middleware/ para tratamento de erros e sessão.
- Configuration/ para registro de serviços, CORS e autenticação.

InvestTrack.Domain:
- Entities/
- Enums/
- Services/ para cálculos e regras que envolvam mais de uma entidade.
- Exceptions/

InvestTrack.Infrastructure:
- Persistence/ para DbContext e configurações EF.
- Persistence/Migrations/
- Identity/ para configuração e validação de sessão.
- BackgroundJobs/ para o processamento diário.

Não criar uma pasta ou classe genérica sem responsabilidade concreta. Começar com poucos arquivos e extrair serviços quando houver lógica reutilizável ou necessidade de teste isolado.

## 3. Dependências permitidas

- Api pode referenciar Domain e Infrastructure.
- Infrastructure pode referenciar Domain.
- Domain não referencia Api nem Infrastructure.
- Tests podem referenciar os projetos que estão testando.

## 4. Fluxo de requisição

1. Cliente envia HTTP.
2. Middleware valida cookie e sessão.
3. Endpoint valida DTO e obtém usuário do contexto autenticado.
4. Regra de domínio/caso de uso é executado.
5. Infrastructure lê ou grava no PostgreSQL.
6. API converte resultado para DTO e HTTP.
7. Erros conhecidos viram ProblemDetails; detalhes internos não são expostos.

## 5. Autenticação Web

Decisão V1: ASP.NET Core Identity com autenticação por cookie HttpOnly.
- Não utilizar JWT ou refresh token na V1.
- Cookie Secure em produção, HttpOnly e SameSite=Lax para implantação same-site.
- Frontend envia cookies nas requisições à API.
- CORS aceita somente a origem explicitamente configurada do frontend.
- Toda requisição que altera estado valida token antiforgery enviado em header.
- Não armazenar credenciais ou tokens de sessão em localStorage.
- Frontend e API devem ficar sob o mesmo site registrável, por exemplo, em subdomínios da mesma raiz.

### Sessão única
A entidade de usuário terá ActiveSessionId. No login, criar GUID novo e persistir. O cookie receberá claim com esse identificador. Em toda requisição autenticada, validar igualdade com o valor persistido e validar os tempos de sessão. Novo login substitui o GUID e invalida a sessão anterior.

- Expiração por inatividade: 30 minutos com sliding expiration.
- Limite absoluto: 12 horas, validado pelo horário de emissão.
- Cookie não persistente entre sessões do navegador.
- Sessão anterior recebe HTTP 401 e código SESSION_REPLACED na próxima requisição. Não há notificação em tempo real.

## 6. Persistência

- PostgreSQL.
- Entity Framework Core.
- ASP.NET Core Identity para usuários e senhas.
- EF Core Migrations versionadas.
- IDs das entidades de domínio: UUID.
- Identity usa chave string conforme configuração padrão.
- Chaves estrangeiras de entidades de domínio referenciam o Id string do usuário.

## 7. Processo diário de snapshots

Na V1, executar um BackgroundService hospedado na própria API:
- verifica diariamente 23:55 em America/Sao_Paulo;
- gera snapshots consolidados e por categoria;
- ao iniciar, preenche datas ausentes até a data corrente;
- usa restrição única no banco para idempotência;
- registra falhas em log e tenta novamente.

Premissa da V1: uma única instância ativa da API. Se houver múltiplas réplicas no futuro, mover o agendamento para worker/scheduler dedicado ou adicionar lock distribuído.

## 8. Estratégia de testes

### Unitários
- normalização e validação de ativos;
- cálculo de quantidade e preço médio;
- validação cronológica de vendas;
- avaliação de posições e resultado;
- cálculo de snapshots.

### Integração
- cadastro e login;
- sessão única e logout;
- isolamento entre usuários;
- persistência de ativos, preços e movimentações;
- transações e rollback;
- geração e recálculo de snapshots.

Testes de integração devem usar PostgreSQL real em ambiente de teste, preferencialmente iniciado em container.

## 9. Decisões para manter simplicidade
- Sem camada de aplicação separada na V1: casos de uso podem ser serviços explícitos na API ou Domain conforme responsabilidade.
- Sem GenericRepository: utilizar DbContext e consultas específicas.
- Sem AutoMapper obrigatório: mapear DTOs manualmente enquanto o volume for pequeno.
- Sem cache distribuído, mensageria ou múltiplos bancos.
