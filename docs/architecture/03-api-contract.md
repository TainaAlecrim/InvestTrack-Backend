# Contrato Inicial da API — InvestTrack

**Versão:** 1.0  
**Base URL local:** https://localhost:<porta>/api/v1  
**Formato:** JSON.  
**Documentação interativa:** OpenAPI/Swagger em ambiente de desenvolvimento.

## Convenções
- IDs: UUID.
- Data de operação: YYYY-MM-DD.
- Instantes: ISO 8601 UTC.
- Decimais: número JSON.
- Endpoints privados exigem cookie de autenticação.
- Requisições POST/PUT/PATCH/DELETE exigem header antiforgery.
- Nunca receber UserId em DTOs.
- Listagens usam page (padrão 1) e pageSize (padrão 20, máximo 100).
- Erros usam ProblemDetails com code estável.

## 1. Autenticação e usuário

| Método | Rota | Acesso | Descrição |
|---|---|---|---|
| GET | /auth/antiforgery-token | Público | Emite token antiforgery |
| POST | /auth/register | Público | Cadastra usuário |
| POST | /auth/login | Público | Autentica e cria sessão |
| POST | /auth/logout | Autenticado | Encerra sessão |
| GET | /users/me | Autenticado | Consulta perfil |
| PATCH | /users/me | Autenticado | Altera nome |
| PUT | /users/me/password | Autenticado | Altera senha e invalida sessão |

### Cadastro — POST /auth/register
Request:
{
  "name": "Ana",
  "email": "ana@example.com",
  "password": "senha-forte"
}

Response: 201 Created. Não devolver hash ou dados internos do Identity.

### Login — POST /auth/login
Request:
{
  "email": "ana@example.com",
  "password": "senha-forte"
}

Response: 200 OK, perfil mínimo e cookie HttpOnly configurado pelo servidor. Não retornar token de sessão no corpo.

### Perfil — GET /users/me
Response:
{
  "name": "Ana",
  "email": "ana@example.com",
  "createdAt": "2026-11-17T12:00:00Z"
}

## 2. Categorias

| Método | Rota | Acesso | Descrição |
|---|---|---|---|
| GET | /categories | Público | Retorna as três categorias fixas |

## 3. Ativos

| Método | Rota | Acesso | Descrição |
|---|---|---|---|
| GET | /assets | Autenticado | Lista ativos; filtros category, search, page, pageSize |
| POST | /assets | Autenticado | Cadastra ativo |
| GET | /assets/{assetId} | Autenticado | Detalha ativo |
| PUT | /assets/{assetId} | Autenticado | Atualiza campos permitidos |
| DELETE | /assets/{assetId} | Autenticado | Exclui somente se não possuir movimentações |

POST /assets:
{
  "code": "PETR4",
  "name": "Petrobras PN",
  "category": "ACTIONS"
}

## 4. Movimentações

| Método | Rota | Acesso | Descrição |
|---|---|---|---|
| GET | /movements | Autenticado | Lista com filtros assetId, type, from, to, page, pageSize |
| POST | /movements | Autenticado | Registra compra ou venda |
| GET | /movements/{movementId} | Autenticado | Consulta uma movimentação |
| PUT | /movements/{movementId} | Autenticado | Altera e revalida histórico |
| DELETE | /movements/{movementId} | Autenticado | Exclui e revalida histórico |

POST /movements:
{
  "assetId": "00000000-0000-0000-0000-000000000001",
  "type": "BUY",
  "tradeDate": "2026-11-17",
  "quantity": 10,
  "unitPrice": 25.50
}

## 5. Posição e preço

| Método | Rota | Acesso | Descrição |
|---|---|---|---|
| GET | /assets/{assetId}/position | Autenticado | Quantidade, preço médio e custo |
| POST | /assets/{assetId}/prices | Autenticado | Informa/atualiza preço do dia |
| GET | /assets/{assetId}/prices | Autenticado | Histórico diário de preços |

POST /assets/{assetId}/prices:
{
  "unitPrice": 27.80
}

A data é determinada pelo servidor; não aceitar priceDate no request.

## 6. Dashboard e evolução

| Método | Rota | Acesso | Descrição |
|---|---|---|---|
| GET | /dashboard | Autenticado | Indicadores consolidados e distribuição |
| GET | /portfolio-history | Autenticado | Snapshots por from, to e category opcional |

Exemplo simplificado de dashboard:
{
  "investedAmount": 15000.00,
  "quotedCostAmount": 12000.00,
  "marketValue": 12600.00,
  "unrealizedResult": 600.00,
  "unrealizedResultPercentage": 5.0,
  "unpricedAssetCount": 1,
  "isMarketValuePartial": true,
  "categories": []
}

Os nomes e a estrutura definitiva dos DTOs devem ser mantidos no projeto e refletidos no Swagger.

## 7. Códigos de erro de negócio

| HTTP | Código | Situação |
|---|---|---|
| 400 | VALIDATION_ERROR | Campo inválido |
| 401 | INVALID_CREDENTIALS | Login inválido |
| 401 | SESSION_REPLACED | Sessão substituída |
| 401 | SESSION_EXPIRED | Sessão expirada |
| 404 | RESOURCE_NOT_FOUND | Recurso inexistente ou de outro usuário |
| 409 | EMAIL_ALREADY_REGISTERED | E-mail já cadastrado |
| 409 | ASSET_CODE_ALREADY_EXISTS | Código duplicado na conta |
| 409 | ASSET_HAS_MOVEMENTS | Ativo possui movimentações |
| 409 | MOVEMENT_WOULD_CREATE_NEGATIVE_BALANCE | Alteração deixa venda sem saldo |
| 422 | INSUFFICIENT_QUANTITY | Quantidade insuficiente para venda |
| 429 | LOGIN_TEMPORARILY_LOCKED | Tentativas de login excedidas |

Toda resposta de erro deve usar ProblemDetails e adicionar o código estável em extensão code.
