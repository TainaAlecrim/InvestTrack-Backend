# InvestTrack Backend

API responsável pelas regras de negócio, autenticação, persistência e disponibilização dos dados do InvestTrack.

> **Status:** especificação da V1 concluída; implementação ainda não iniciada.

## Objetivo

O InvestTrack é uma aplicação Web para controle e acompanhamento de investimentos já realizados pelo usuário. O sistema não executa ordens de compra ou venda, não movimenta dinheiro e não se conecta a corretoras ou provedores de cotações na versão 1.0.

## Escopo da versão 1.0

- Cadastro, autenticação e perfil de usuário.
- Três categorias fixas: Ações, Fundos Imobiliários e Renda Fixa.
- Cadastro manual de ativos.
- Registro manual de compras e vendas.
- Atualização manual de preços.
- Consulta, edição e exclusão de movimentações.
- Cálculo de posição por custo médio ponderado móvel.
- Dashboard e histórico diário do patrimônio.
- Isolamento dos dados de cada usuário.

Importação CSV, integração com corretoras/cotações, aplicativos móveis, taxas operacionais e recuperação automática de senha estão fora do escopo inicial.

## Tecnologias definidas

- C# e .NET 10
- ASP.NET Core Web API
- ASP.NET Core Identity com cookie HttpOnly
- PostgreSQL
- Entity Framework Core
- xUnit
- OpenAPI/Swagger
- Docker Compose para ambiente local

## Documentação para desenvolvimento

### Requisitos
- [Visão do produto](docs/requirements/01-product-vision.md)
- [Escopo da V1](docs/requirements/02-project-scope.md)
- [Requisitos funcionais](docs/requirements/03-functional-requirements.md)
- [Requisitos não funcionais](docs/requirements/04-non-functional-requirements.md)
- [Regras de negócio](docs/requirements/05-business-rules.md)
- [Casos de uso](docs/requirements/06-use-cases.md)
- [Critérios de aceite](docs/requirements/07-acceptance-criteria.md)

### Arquitetura
- [Arquitetura do sistema](docs/architecture/01-system-architecture.md)
- [Modelo de dados](docs/architecture/02-data-model.md)
- [Contrato da API](docs/architecture/03-api-contract.md)
- [Plano de implementação](docs/architecture/04-implementation-plan.md)

### Decisões técnicas
- [Registro de decisões](docs/decisions/README.md)

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

As pastas src e tests serão criadas na Fase 0 do plano de implementação. Não há projetos .NET nesta etapa.

## Princípios do projeto

1. Manter a primeira versão simples e com escopo controlado.
2. Aplicar regras de negócio no backend.
3. Garantir isolamento dos dados por usuário.
4. Priorizar código testável, legível e sustentável.
5. Não criar infraestrutura ou abstrações sem necessidade concreta.
6. Seguir os requisitos e critérios de aceite documentados.

## Como começar

Leia, nesta ordem:
1. Escopo da V1.
2. Requisitos funcionais e não funcionais.
3. Regras de negócio.
4. Arquitetura e modelo de dados.
5. Plano de implementação.

A primeira tarefa de desenvolvimento será a Fase 0: preparar a solução .NET 10 e o PostgreSQL local. Não iniciar módulos funcionais antes do ambiente e dos testes básicos estarem prontos.
