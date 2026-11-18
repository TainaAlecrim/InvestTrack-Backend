# InvestTrack Backend

API responsável pelas regras de negócio, autenticação, persistência e disponibilização dos dados do InvestTrack.

> **Status:** planejamento e especificação. A implementação ainda não foi iniciada.

## Objetivo

O InvestTrack é uma aplicação web para controle e acompanhamento de investimentos já realizados pelo usuário. O sistema não executa ordens de compra ou venda, não movimenta dinheiro e não se conecta a corretoras ou provedores de cotações na versão 1.0.

## Escopo da versão 1.0

- Aplicação consumida por uma interface Web.
- Cadastro, autenticação e perfil de usuário.
- Três categorias fixas: Ações, Fundos Imobiliários e Renda Fixa.
- Cadastro manual de ativos.
- Registro manual de compras e vendas.
- Atualização manual de preços.
- Consulta, edição e exclusão de movimentações.
- Dashboard e histórico diário do patrimônio.
- Isolamento dos dados de cada usuário.

Funcionalidades como aplicativo móvel, importação CSV, integração com corretoras/cotações, taxas operacionais e recuperação automática de senha estão fora do escopo inicial.

## Direção técnica inicial

As tecnologias abaixo são a proposta de trabalho e serão formalizadas nos documentos de arquitetura antes da implementação:

- C# e .NET 10
- ASP.NET Core Web API
- PostgreSQL
- Entity Framework Core
- ASP.NET Core Identity
- xUnit
- OpenAPI/Swagger

A arquitetura prevista é um monólito modular organizado em camadas. Microserviços e abstrações adicionais não fazem parte do plano inicial.

## Estrutura planejada

```text
InvestTrack-Backend/
├── docs/
│   ├── requirements/
│   ├── architecture/
│   ├── decisions/
│   └── diagrams/
├── src/
│   ├── InvestTrack.Api/
│   ├── InvestTrack.Domain/
│   └── InvestTrack.Infrastructure/
├── tests/
│   ├── InvestTrack.UnitTests/
│   └── InvestTrack.IntegrationTests/
├── .gitignore
└── README.md
```

As pastas `src/` e `tests/` serão criadas quando iniciarmos a implementação. Não há projetos .NET nesta etapa.

## Documentação

- [Requisitos](docs/requirements/README.md)
- [Arquitetura](docs/architecture/README.md)
- [Decisões técnicas](docs/decisions/README.md)
- [Diagramas](docs/diagrams/README.md)

## Princípios do projeto

1. Manter a primeira versão simples e com escopo controlado.
2. Aplicar regras de negócio no backend.
3. Garantir isolamento dos dados por usuário.
4. Priorizar código testável, legível e sustentável.
5. Documentar decisões relevantes antes de implementá-las.

## Estado atual

Estamos na etapa de levantamento e especificação dos requisitos. O desenvolvimento começará após a revisão dos requisitos, regras de negócio, arquitetura e contratos da API.
