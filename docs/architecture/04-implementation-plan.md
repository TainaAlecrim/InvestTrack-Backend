# Plano de Implementação — InvestTrack Backend

**Objetivo:** orientar a implementação em ordem de dependências para evitar retrabalho.

## Fase 0 — Preparação da solução
1. Instalar/usar SDK .NET 10.
2. Criar solução InvestTrack.sln.
3. Criar projetos Api, Domain e Infrastructure.
4. Criar projetos UnitTests e IntegrationTests.
5. Configurar referências entre projetos.
6. Adicionar editorconfig, gitignore e build inicial.
7. Configurar PostgreSQL local via Docker Compose.

**Pronto quando:** solution compila e API responde endpoint de health simples.

## Fase 1 — Persistência e identidade
1. Criar ApplicationUser com CreatedAtUtc e ActiveSessionId.
2. Configurar ASP.NET Core Identity e DbContext.
3. Configurar PostgreSQL e migrations.
4. Criar register, login, logout, perfil e alteração de senha.
5. Configurar cookie, antiforgery, CORS, sessão única e lockout.
6. Criar testes de integração para autenticação.

**Pronto quando:** CA-001 a CA-009 passam.

## Fase 2 — Categorias e ativos
1. Criar enum PortfolioCategory.
2. Disponibilizar GET /categories.
3. Criar entidade Asset e mapeamento.
4. Criar CRUD de ativos com ownership e paginação.
5. Adicionar índices e restrição de código único por usuário.
6. Testar isolamento, duplicidade, categoria e exclusão.

**Pronto quando:** CA-010 a CA-014 passam.

## Fase 3 — Movimentações e posição
1. Criar Movement e configurações EF.
2. Implementar calculador de posição no Domain.
3. Implementar registro de compra.
4. Implementar venda com validação cronológica.
5. Implementar consulta e filtros.
6. Implementar edição e exclusão transacionais.
7. Testar cálculos, sequência no mesmo dia, rollback e concorrência.

**Pronto quando:** CA-015 a CA-022 passam.

## Fase 4 — Preços manuais
1. Criar AssetPrice.
2. Implementar atualização diária com upsert.
3. Implementar histórico de preços.
4. Testar ativo próprio, preço positivo e atualização no mesmo dia.

**Pronto quando:** CA-023 e CA-024 passam.

## Fase 5 — Dashboard
1. Implementar serviço de avaliação das posições.
2. Calcular custo total das posições abertas.
3. Calcular valor cotado e resultado não realizado.
4. Identificar ativos sem preço.
5. Calcular distribuição por categoria.
6. Testar parciais e divisão por zero.

**Pronto quando:** CA-025 a CA-027 passam.

## Fase 6 — Histórico diário
1. Criar PortfolioSnapshot e índices únicos.
2. Implementar serviço de snapshot para uma data específica.
3. Implementar BackgroundService às 23:55 local.
4. Implementar recuperação de datas ausentes.
5. Implementar idempotência.
6. Recalcular snapshots após movimentação retroativa ou correção de preço, até a próxima cotação registrada para o ativo.
7. Criar endpoint de consulta da evolução.

**Pronto quando:** CA-028 a CA-031 passam.

## Fase 7 — Qualidade e entrega
1. Revisar endpoints no Swagger.
2. Executar testes.
3. Validar migrations em banco vazio.
4. Revisar logs e tratamento de erros.
5. Validar configuração segura e ausência de segredos.
6. Atualizar README com instruções para executar a API.
7. Criar release 1.0.0.

## Branches sugeridas
- main: versão estável.
- feature/authentication
- feature/assets
- feature/movements
- feature/manual-prices
- feature/dashboard
- feature/portfolio-history

Para trabalho individual, usar branches curtas e fazer merge em main após testes. Não é necessário criar branch para cada arquivo.
