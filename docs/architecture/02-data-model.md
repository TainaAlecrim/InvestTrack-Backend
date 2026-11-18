# Modelo de Dados — InvestTrack

**Versão:** 1.0  
**Banco:** PostgreSQL  
**ORM:** Entity Framework Core.

## 1. Relacionamentos

- AspNetUsers 1:N Assets.
- Asset 1:N Movements.
- Asset 1:N AssetPrices.
- AspNetUsers 1:N PortfolioSnapshots.

Categorias fixas são valores controlados no código, não tabela com CRUD.

## 2. AspNetUsers / ApplicationUser

Estende a entidade de usuário do ASP.NET Core Identity.

| Campo | Tipo lógico | Regra |
|---|---|---|
| Id | string | Chave primária Identity |
| UserName | string | Usar e-mail normalizado como nome de usuário |
| Email | string | Obrigatório, único |
| NormalizedEmail | string | Índice único |
| PasswordHash | string | Gerenciado pelo Identity |
| CreatedAtUtc | timestamptz | Gerado pelo servidor |
| ActiveSessionId | uuid nullable | Sessão corrente |
| LockoutEnd | timestamptz nullable | Bloqueio temporário gerenciado pelo Identity |

Não criar tabela de perfil separada.

## 3. Asset

| Campo | Tipo PostgreSQL | Regra |
|---|---|---|
| Id | uuid | PK |
| UserId | text | FK para AspNetUsers |
| Code | varchar(30) | Código exibido |
| NormalizedCode | varchar(30) | Único por usuário |
| Name | varchar(100) | Obrigatório |
| Category | smallint | 1, 2 ou 3 |
| CreatedAtUtc | timestamptz | Servidor |
| UpdatedAtUtc | timestamptz | Servidor |

Restrições:
- UNIQUE (UserId, NormalizedCode).
- Index (UserId, Category).
- FK UserId para AspNetUsers.

Categorias:
- 1 = Actions
- 2 = RealEstateFunds
- 3 = FixedIncome

## 4. Movement

| Campo | Tipo PostgreSQL | Regra |
|---|---|---|
| Id | uuid | PK |
| AssetId | uuid | FK Asset |
| Type | smallint | 1 = Buy; 2 = Sell |
| TradeDate | date | Data efetiva informada |
| Quantity | numeric(18,6) | Maior que zero |
| UnitPrice | numeric(18,6) | Maior que zero |
| CreatedAtUtc | timestamptz | Ordem de registro e auditoria |
| UpdatedAtUtc | timestamptz | Última alteração |

A propriedade do usuário é determinada pelo Asset relacionado. Toda consulta deve filtrar por Asset.UserId do usuário autenticado.

Índice: (AssetId, TradeDate, CreatedAtUtc).

Não persistir saldo nem preço médio em Movement. Estes valores são calculados a partir do histórico.

## 5. AssetPrice

| Campo | Tipo PostgreSQL | Regra |
|---|---|---|
| Id | uuid | PK |
| AssetId | uuid | FK Asset |
| PriceDate | date | Data local atribuída pelo servidor |
| UnitPrice | numeric(18,6) | Maior que zero |
| CreatedAtUtc | timestamptz | Registro técnico |
| UpdatedAtUtc | timestamptz | Atualização no mesmo dia |

Restrições:
- UNIQUE (AssetId, PriceDate).
- Index (AssetId, PriceDate DESC).

A atualização no mesmo dia altera UnitPrice e UpdatedAtUtc do registro existente.

## 6. PortfolioSnapshot

| Campo | Tipo PostgreSQL | Regra |
|---|---|---|
| Id | uuid | PK |
| UserId | text | FK AspNetUsers |
| ReferenceDate | date | Data local representada |
| Category | smallint | 0 consolidado; 1, 2 ou 3 categoria |
| InvestedAmount | numeric(18,6) | Custo de todas as posições abertas |
| QuotedCostAmount | numeric(18,6) | Custo das posições que têm preço |
| MarketValue | numeric(18,6) | Valor atual das posições com preço |
| UnrealizedResult | numeric(18,6) | MarketValue - QuotedCostAmount |
| UnpricedAssetCount | integer | Ativos com quantidade positiva sem preço conhecido |
| GeneratedAtUtc | timestamptz | Instante de geração |

Restrições:
- UNIQUE (UserId, ReferenceDate, Category).
- Index (UserId, ReferenceDate).
- Category 0 = consolidado; 1 = Ações; 2 = Fundos Imobiliários; 3 = Renda Fixa.

## 7. Regras de exclusão
- Não permitir excluir Asset com Movement.
- Excluir Asset sem Movement também exclui AssetPrices.
- Não implementar exclusão de usuário na V1.

## 8. Precisão financeira
Usar decimal em C# e numeric(18,6) para quantidade, preço e valores derivados. Evitar double/float. Arredondamento para duas casas ocorre na apresentação, não no meio do cálculo.

## 9. Posições não são tabela na V1
Não criar tabela Positions. A posição é derivada das movimentações: quantidade atual, preço médio móvel e custo das posições. Otimizações futuras só após medição de desempenho.


## 10. Concorrência em movimentações

Todas as operações de inclusão, edição e exclusão de movimentações devem iniciar transação e bloquear a linha do Asset correspondente no PostgreSQL antes de revalidar o histórico. Utilizar bloqueio de linha (SELECT FOR UPDATE) para serializar duas alterações simultâneas no mesmo ativo. Após obter o lock, carregar as movimentações, validar o saldo cronológico e persistir a alteração.

Isso evita que duas vendas concorrentes sejam aceitas com base no mesmo saldo antigo. Operações em ativos diferentes podem ocorrer em paralelo.
