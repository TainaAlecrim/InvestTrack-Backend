# Arquitetura

Documentação técnica que orienta a implementação do backend.

## Documentos

- [01-system-architecture.md](01-system-architecture.md) — camadas, dependências, autenticação e processo diário.
- [02-data-model.md](02-data-model.md) — entidades, campos, relações, índices e restrições.
- [03-api-contract.md](03-api-contract.md) — endpoints, payloads, convenções e erros.
- [04-implementation-plan.md](04-implementation-plan.md) — ordem de construção e definição de pronto por fase.

## Direção

A V1 será um monólito modular em camadas, com ASP.NET Core Web API, PostgreSQL, EF Core e ASP.NET Core Identity. Não criar microserviços ou infraestrutura distribuída.
