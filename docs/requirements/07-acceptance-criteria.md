# Critérios de Aceite — InvestTrack Backend

**Versão:** 1.0  
Cada cenário deve ser coberto por teste automatizado quando aplicável. Testes manuais podem complementar, mas não substituir testes de regras críticas.

## Autenticação e usuário

### CA-001 — Cadastro válido
**Dado** nome, e-mail e senha válidos e e-mail ainda livre, **quando** enviar cadastro, **então** a API cria o usuário, registra a data e retorna sucesso sem autenticar automaticamente.

### CA-002 — E-mail duplicado
**Dado** e-mail já cadastrado, **quando** tentar registrar o mesmo e-mail com variação de maiúsculas/minúsculas, **então** a API rejeita o cadastro.

### CA-003 — Senha inválida
**Dado** senha com menos de 8 caracteres, **quando** tentar cadastrar, **então** a API retorna erro de validação.

### CA-004 — Login inválido
**Dado** credenciais incorretas, **quando** tentar login, **então** a API retorna mensagem genérica sem confirmar se o e-mail existe.

### CA-005 — Sessão única
**Dado** usuário autenticado na sessão A, **quando** realizar login na sessão B, **então** requisições protegidas da sessão A retornam SESSION_REPLACED e a sessão B funciona.

### CA-006 — Logout
**Dado** usuário autenticado, **quando** executar logout, **então** o cookie é encerrado e requisições protegidas subsequentes retornam 401.

### CA-007 — Isolamento
**Dado** usuários A e B, **quando** A tentar consultar, editar ou excluir recurso de B, **então** a API retorna 404 e não altera dados.

### CA-008 — Perfil
**Dado** usuário autenticado, **quando** consultar perfil, **então** recebe nome, e-mail e data de cadastro; ao alterar nome, somente o nome muda.

### CA-009 — Senha
**Dado** usuário autenticado e senha atual correta, **quando** trocar senha, **então** senha é atualizada e sessão é invalidada. Senha atual incorreta não altera nada.

## Categorias e ativos

### CA-010 — Categorias
**Quando** consultar categorias, **então** a API retorna exatamente Ações, Fundos Imobiliários e Renda Fixa.

### CA-011 — Código duplicado
**Dado** usuário com ativo PETR4, **quando** cadastrar petr4 novamente, **então** API rejeita duplicidade.

### CA-012 — Mesmo código em contas diferentes
**Dado** usuário A com PETR4, **quando** usuário B cadastrar PETR4, **então** cadastro de B é permitido e dados permanecem isolados.

### CA-013 — Exclusão de ativo com movimentação
**Dado** ativo com ao menos uma movimentação, **quando** tentar excluí-lo, **então** API retorna conflito ASSET_HAS_MOVEMENTS.

### CA-014 — Mudança de categoria
**Dado** ativo com movimentações, **quando** tentar trocar sua categoria, **então** API rejeita a alteração.

## Movimentações e cálculo

### CA-015 — Compra
**Dado** ativo próprio e dados positivos válidos, **quando** registrar compra, **então** saldo e preço médio são atualizados.

### CA-016 — Venda válida
**Dado** quantidade disponível suficiente na data, **quando** registrar venda, **então** saldo diminui e preço médio das unidades restantes permanece igual.

### CA-017 — Venda inválida
**Dado** saldo insuficiente na data da operação, **quando** registrar venda, **então** API rejeita e não persiste a movimentação.

### CA-018 — Preço médio ponderado
**Dado** compra de 10 unidades a R$ 10 e compra de 10 unidades a R$ 20, **quando** calcular posição, **então** quantidade é 20 e preço médio é R$ 15.

### CA-019 — Venda parcial
**Dado** 20 unidades com preço médio de R$ 15, **quando** vender 5 unidades, **então** quantidade é 15 e preço médio permanece R$ 15.

### CA-020 — Venda total
**Dado** posição de 15 unidades, **quando** vender as 15 unidades, **então** quantidade e preço médio da posição ficam zerados.

### CA-021 — Edição retroativa
**Dado** movimentações existentes, **quando** editar uma movimentação e uma venda posterior ficar sem saldo, **então** a edição é rejeitada integralmente.

### CA-022 — Exclusão retroativa
**Dado** movimentações existentes, **quando** excluir uma compra necessária para uma venda posterior, **então** a exclusão é rejeitada integralmente.

## Preços e dashboard

### CA-023 — Preço manual
**Dado** ativo próprio e preço positivo, **quando** informar preço, **então** a API grava preço e data local.

### CA-024 — Atualização no mesmo dia
**Dado** preço já informado para o ativo na data atual, **quando** informar novo preço no mesmo dia, **então** o registro diário é atualizado sem duplicação.

### CA-025 — Ativo sem preço
**Dado** ativo com quantidade positiva e sem preço, **quando** consultar dashboard, **então** o ativo é contado como sem preço e não é avaliado artificialmente em zero.

### CA-026 — Resultado não realizado
**Dado** posição com custo conhecido e preço manual, **quando** consultar dashboard, **então** resultado é valor atual menos custo das unidades restantes, sem incluir resultado realizado de vendas.

### CA-027 — Distribuição
**Dado** valores cotados em categorias distintas, **quando** consultar dashboard, **então** percentuais são calculados sobre o total cotado e somam aproximadamente 100%, considerando arredondamento de apresentação.

## Snapshots

### CA-028 — Snapshot diário
**Dado** usuário e posições cadastradas, **quando** executar o processamento diário para uma data, **então** são gravados snapshots consolidados e por categoria.

### CA-029 — Idempotência
**Dado** snapshot já criado para usuário/categoria/data, **quando** executar o job novamente para a mesma data, **então** não é criado registro duplicado.

### CA-030 — Preço anterior carregado
**Dado** ativo com preço informado em dia anterior e sem nova atualização, **quando** gerar snapshot posterior, **então** o último preço conhecido é usado e a data de atualização continua visível.

### CA-031 — Reprocessamento de histórico
**Dado** snapshots já existentes e movimentação retroativa válida alterada, **quando** salvar a alteração, **então** snapshots da data afetada em diante são recalculados.

## Qualidade mínima para entrega

- Todos os critérios CA-001 a CA-031 cobertos por testes apropriados.
- Build sem erros.
- Migrations aplicáveis em banco vazio.
- API documentada com OpenAPI.
- Nenhum segredo versionado.
- Nenhum teste de isolamento ou cálculo financeiro crítico pode ficar ignorado.
