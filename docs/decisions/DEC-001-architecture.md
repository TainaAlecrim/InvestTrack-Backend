# DEC-001 — Arquitetura de monólito modular em camadas

- **Status:** Aprovada para V1
- **Data:** 17/11/2026

## Contexto
O InvestTrack precisa de API, regras de negócio, autenticação e persistência. A V1 deve ser simples de desenvolver e manter.

## Decisão
Construir uma única ASP.NET Core Web API dividida em Api, Domain e Infrastructure. Utilizar PostgreSQL e EF Core. Não adotar microserviços.

## Consequências
- Menos infraestrutura e implantação mais simples.
- Separação de responsabilidades sem distribuir a aplicação.
- Revisar a decisão futura somente se houver evidências de necessidade.
