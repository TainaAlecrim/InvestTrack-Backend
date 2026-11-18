# Regras de Negócio — InvestTrack

**Versão:** 1.0  
**Status:** baseline para desenvolvimento.

## Categorias e ativos

### RN-001 — Categorias fixas
As únicas categorias são:
- ACTIONS — Ações
- REAL_ESTATE_FUNDS — Fundos Imobiliários
- FIXED_INCOME — Renda Fixa

Não existe manutenção de categorias na V1.

### RN-002 — Ativo pertence a um usuário e uma categoria
Cada ativo pertence a exatamente um usuário e a exatamente uma categoria. Não é possível associar o mesmo registro de ativo a duas categorias.

### RN-003 — Código único por usuário
O código normalizado é único dentro da conta do usuário. Normalização: remover espaços nas extremidades e converter para maiúsculas. A comparação não diferencia maiúsculas/minúsculas. Usuários diferentes podem cadastrar o mesmo código.

### RN-004 — Alteração e exclusão de ativo
- Um ativo com movimentações não pode ser excluído.
- Um ativo com movimentações não pode trocar de categoria.
- Código e nome podem ser corrigidos, desde que não gerem duplicidade.
- Ativo sem movimentações pode ser excluído; os preços associados também serão removidos.

## Movimentações

### RN-005 — Tipos permitidos
Somente BUY (compra) e SELL (venda).

### RN-006 — Valores positivos
Quantidade e preço unitário devem ser estritamente maiores que zero. Valores negativos ou iguais a zero são inválidos.

### RN-007 — Data da operação
Data da operação não pode ser posterior à data atual em America/Sao_Paulo. É permitido registrar operações passadas.

### RN-008 — Saldo cronológico
O saldo deve ser calculado pela ordem:
1. data da operação crescente;
2. data/hora de registro no servidor crescente;
3. identificador como desempate determinístico.

Quando existirem várias operações do mesmo ativo no mesmo dia, a ordem em que forem registradas será considerada a ordem de execução. O usuário deverá registrá-las na mesma sequência em que aconteceram.

### RN-009 — Venda limitada ao saldo
Em cada ponto do histórico, o saldo acumulado não pode ficar negativo. Uma venda que gere saldo negativo deve ser rejeitada.

### RN-010 — Preço médio ponderado móvel
O preço médio é recalculado a cada compra:

Novo preço médio = ((Quantidade anterior × Preço médio anterior) + (Quantidade comprada × Preço da compra)) / (Quantidade anterior + Quantidade comprada)

A venda reduz a quantidade, mas não altera o preço médio das unidades remanescentes.

Se a quantidade restante chegar a zero, o preço médio da posição será zerado.

### RN-011 — Custo das posições atuais
Custo das posições = Quantidade atual × Preço médio atual.

O indicador "Total investido" do dashboard representa o custo de aquisição associado às posições que ainda estão abertas. Não representa a soma histórica de todas as compras.

### RN-012 — Edição e exclusão retroativa
Depois de editar ou excluir uma movimentação, todas as operações posteriores daquele ativo devem ser reprocessadas. A mudança é rejeitada se qualquer venda se tornar inválida.

### RN-013 — Sem taxas
Não considerar corretagem, emolumentos, impostos ou outros custos na V1.

## Preços

### RN-014 — Preço manual
O preço informado deve ser maior que zero e pertence ao ativo do usuário autenticado.

### RN-015 — Data do preço
A data é gerada pelo servidor no fuso America/Sao_Paulo. O usuário não escolhe data retroativa ou futura.

### RN-016 — Um preço por ativo por dia
Existe no máximo um preço por ativo e data. Uma nova atualização no mesmo dia substitui o preço daquele dia; o histórico mantém um registro diário por ativo.

### RN-017 — Preço desatualizado
O último preço informado permanece sendo utilizado até nova atualização. A aplicação deve mostrar a data do último preço. Não afirmar que o valor é cotação em tempo real.

### RN-018 — Ativo sem preço
Ativo com quantidade atual positiva e sem preço informado não será avaliado como zero. Deve aparecer como "Sem preço informado" e ser contado separadamente. Os totais do dashboard serão identificados como parciais enquanto existir ativo sem preço.

## Dashboard e resultado

### RN-019 — Valor atual
Valor atual = Quantidade atual × Último preço informado.

Somar somente ativos com preço informado.

### RN-020 — Resultado não realizado
Resultado não realizado = Valor atual cotado − Custo das posições correspondentes aos ativos cotados.

O resultado percentual é calculado sobre o custo das mesmas posições cotadas. Se o custo for zero, o percentual deve ser nulo.

### RN-021 — Vendas e resultado realizado
Ganhos ou perdas realizados em vendas não serão calculados nem somados ao resultado do dashboard na V1. O dashboard representa somente o resultado não realizado das posições remanescentes.

### RN-022 — Distribuição
Participação por categoria é calculada sobre o valor atual dos ativos cotados. Quando o total cotado for zero, os percentuais devem ser nulos. Ativos sem preço são destacados à parte.

## Snapshots

### RN-023 — Data de referência
Snapshots são gerados às 23:55, America/Sao_Paulo, para a data local corrente.

### RN-024 — Valores históricos
O snapshot usa a quantidade que existia na data e o último preço manual registrado até aquela data. Se não houver atualização naquele dia, o último preço conhecido é carregado adiante.

### RN-025 — Idempotência
Só pode existir um snapshot por usuário, categoria e data. O processamento repetido da mesma data deve atualizar ou confirmar o registro existente, nunca duplicá-lo.

### RN-026 — Snapshot consolidado e por categoria
Para cada usuário e dia, gerar quatro visões: total consolidado, Ações, Fundos Imobiliários e Renda Fixa.

### RN-027 — Correção de histórico
Alteração ou exclusão retroativa de movimentação deve provocar recálculo dos snapshots da data afetada até a data atual. Ao substituir preço de uma data, recalcular snapshots dessa data até o dia anterior ao próximo preço já registrado para o ativo; se não houver preço posterior, recalcular até a data atual. Se o snapshot do dia já existir, ele também deverá ser atualizado.

## Arredondamento

### RN-028 — Precisão
Manter precisão decimal durante os cálculos. Arredondar para duas casas somente na apresentação de valores em R$. Persistir os snapshots com precisão de seis casas decimais.


### RN-029 — Concorrência no mesmo ativo
Inclusão, edição e exclusão de movimentações do mesmo ativo devem ser serializadas com bloqueio da linha do ativo dentro de transação. O histórico deve ser revalidado após obter o bloqueio e antes de persistir. Uma operação concorrente não pode deixar o saldo negativo.
