# Requisitos Funcionais — InvestTrack Backend

**Versão:** 1.0  
**Status:** baseline para desenvolvimento.  
Todos os requisitos marcados como incluídos na V1 devem ser implementados.

## Módulo 1 — Autenticação e usuários

### RF-001 — Cadastro de usuário
O sistema deve permitir cadastro com nome, e-mail e senha.
- Todos os campos são obrigatórios.
- Nome: 2 a 100 caracteres após remoção de espaços nas extremidades.
- E-mail: formato válido, até 254 caracteres e único sem diferenciar maiúsculas/minúsculas.
- Senha: mínimo 8 e máximo 128 caracteres.
- Não exigir combinação específica de letras maiúsculas, números ou símbolos.
- Senha armazenada com hash seguro gerenciado pelo ASP.NET Core Identity.
- Data de criação gerada no servidor em UTC.
- Cadastro concluído retorna sucesso e o cliente segue para login; não autentica automaticamente.
- Não haverá confirmação de senha, verificação de e-mail ou envio de e-mail na V1.

### RF-002 — Login
- Receber e-mail e senha.
- Autenticar credenciais no backend.
- Em caso de sucesso, criar uma nova sessão.
- Não oferecer opção "Lembrar de mim".
- Em caso de falha, retornar mensagem genérica: "E-mail ou senha inválidos."
- Após 5 tentativas inválidas consecutivas, bloquear novas tentativas por 15 minutos para a conta, sem revelar se o e-mail está cadastrado.

### RF-003 — Logout
- Encerrar a sessão autenticada.
- Remover o cookie de autenticação no cliente.
- Invalidar no servidor o identificador de sessão correspondente.
- Requisições subsequentes a endpoints protegidos devem retornar HTTP 401.

### RF-004 — Sessão única
- Cada usuário pode ter apenas uma sessão ativa.
- Um novo login substitui o identificador da sessão anterior.
- O backend deve validar o identificador da sessão em toda requisição autenticada.
- A sessão anterior recebe HTTP 401 com código SESSION_REPLACED na próxima requisição protegida.
- A mensagem apresentada pelo frontend será: "Sua sessão foi encerrada porque houve um novo login na sua conta."
- A notificação não será enviada em tempo real; será retornada quando a sessão anterior fizer nova requisição.
- Sessão sem atividade expira após 30 minutos; duração máxima absoluta de 12 horas.
- O cookie não será persistente entre sessões do navegador.

### RF-005 — Isolamento de dados
- Todo dado privado deve estar associado ao usuário autenticado.
- Consultas, inclusões, alterações e exclusões devem usar o usuário obtido do contexto autenticado.
- O backend não deve aceitar UserId informado pelo cliente como fonte de autorização.
- Recurso inexistente ou pertencente a outro usuário deve responder HTTP 404.
- O isolamento deve ser testado com pelo menos dois usuários diferentes.

### RF-006 — Gerenciamento do perfil
- Permitir consultar nome, e-mail e data de cadastro.
- Permitir alterar somente o nome na operação de atualização de perfil.
- Permitir alterar senha informando senha atual e nova senha.
- Validar a senha atual antes de efetuar a troca.
- Após alteração de senha, invalidar a sessão atual e exigir novo login.
- E-mail não pode ser alterado na V1.

### RF-007 — Exclusão de conta (fora da V1)
A versão 1.0 não deve disponibilizar endpoint ou interface para o usuário excluir a própria conta. Não implementar fluxo de exclusão nesta entrega.

### RF-008 — Data de cadastro
- A data de criação deve ser persistida pelo servidor.
- Deve ser retornada no perfil e apresentada no fuso America/Sao_Paulo.
- O cliente não pode definir nem alterar essa data.

### Fora da V1 — Exclusão de conta
Não implementar endpoint ou interface de exclusão de conta na versão 1.0.

## Módulo 2 — Categorias e ativos

### RF-009 — Consultar categorias fixas
O backend deve disponibilizar exatamente estas categorias:
- ACTIONS — Ações
- REAL_ESTATE_FUNDS — Fundos Imobiliários
- FIXED_INCOME — Renda Fixa

As categorias não podem ser criadas, renomeadas ou excluídas pelo usuário.

### RF-010 — Cadastrar ativo
O usuário autenticado pode cadastrar um ativo com:
- código: obrigatório, de 1 a 30 caracteres;
- nome: obrigatório, de 2 a 100 caracteres;
- categoria: obrigatória e limitada às três categorias fixas.

O sistema deve remover espaços nas extremidades, normalizar o código para maiúsculas e impedir códigos duplicados na mesma conta, sem diferenciar maiúsculas/minúsculas. O mesmo código pode existir em contas diferentes.

### RF-011 — Consultar ativos
- Listar somente ativos do usuário autenticado.
- Permitir filtro por categoria.
- Permitir busca parcial por código ou nome.
- Retornar quantidade atual e informação sobre existência de preço informado, quando aplicável.
- Ordenação padrão por código crescente.
- Paginação obrigatória: página padrão 1, tamanho padrão 20 e máximo 100.

### RF-012 — Alterar ativo
- Permitir alterar código e nome, respeitando validações de cadastro.
- Permitir alterar categoria somente enquanto o ativo não possuir movimentações.
- Não permitir alteração da propriedade do ativo para outro usuário.

### RF-013 — Excluir ativo
- Permitir excluir ativo somente quando não existir movimentação associada.
- Se existir movimentação, retornar HTTP 409 com código ASSET_HAS_MOVEMENTS.
- A exclusão deve remover também os registros de preço do ativo, pois não haverá movimentações vinculadas.

## Módulo 3 — Movimentações

### RF-014 — Registrar compra
Registrar compra com ativo, quantidade, preço unitário e data da operação.
- Quantidade e preço unitário devem ser maiores que zero.
- Data não pode ser futura.
- Ativo deve pertencer ao usuário autenticado.
- O backend deve recalcular o saldo e preço médio.

### RF-015 — Registrar venda
Registrar venda com ativo, quantidade, preço unitário e data da operação.
- Aplicar as mesmas validações de quantidade, preço e data da compra.
- A venda não pode exceder a quantidade disponível na data da operação.
- Se a regra for violada, rejeitar a operação sem persistir alterações.

### RF-016 — Consultar movimentações
- Listar somente movimentações do usuário autenticado.
- Permitir filtros por ativo, tipo de movimentação e intervalo de datas.
- Intervalo de datas é inclusivo.
- Ordenar por data da operação decrescente e, em empate, por data de registro decrescente.
- Paginação padrão 1/20, máximo 100.

### RF-017 — Alterar movimentação
- Permitir alterar tipo, ativo, quantidade, preço unitário e data.
- Revalidar o histórico completo do ativo após a alteração.
- Se a alteração tornar qualquer venda posterior inválida, rejeitar toda a alteração com HTTP 409 e código MOVEMENT_WOULD_CREATE_NEGATIVE_BALANCE.
- A operação deve ser atômica.

### RF-018 — Excluir movimentação
- Permitir excluir movimentação do usuário autenticado.
- Revalidar todas as movimentações posteriores do ativo.
- Se a exclusão fizer com que uma venda posterior exceda o saldo, rejeitar a exclusão com HTTP 409.
- A operação deve ser atômica.

### RF-019 — Calcular posição
O backend deve calcular, por ativo:
- quantidade atual;
- preço médio ponderado das unidades restantes;
- custo das posições atuais.

O cálculo deve ser derivado das movimentações válidas, sem confiar em saldo enviado pelo frontend.

## Módulo 4 — Preços manuais

### RF-020 — Atualizar preço atual
- Permitir informar manualmente o preço unitário atual de um ativo.
- Preço deve ser maior que zero.
- A data do preço será definida pelo servidor no fuso America/Sao_Paulo.
- O usuário não pode informar data retroativa ou futura.
- Para cada ativo, haverá no máximo um registro de preço por dia; nova atualização no mesmo dia substitui o valor daquele dia.
- O preço e sua data mais recente devem ser apresentados na consulta do ativo.

### RF-021 — Consultar histórico de preços
- Retornar os preços informados para um ativo do usuário autenticado.
- Ordenar da data mais recente para a mais antiga.
- Não expor preços de ativos pertencentes a outros usuários.

## Módulo 5 — Dashboard e patrimônio

### RF-022 — Consultar dashboard
O dashboard deve retornar:
- custo das posições atuais (Total investido);
- valor atual calculado dos ativos com preço conhecido;
- resultado não realizado em valor e percentual;
- quantidade de ativos sem preço informado;
- valor identificado como parcial quando houver ativos sem preço.

### RF-023 — Distribuição por categoria
- Calcular a participação de Ações, Fundos Imobiliários e Renda Fixa com base no valor atualizado dos ativos que possuem preço informado.
- Se não houver valor total cotado, retornar percentuais nulos em vez de dividir por zero.
- Identificar ativos sem preço separadamente; não atribuir preço zero a eles.

### RF-024 — Gerar snapshot diário
- Gerar registros diários automaticamente às 23:55 no fuso America/Sao_Paulo.
- Gerar um snapshot consolidado e um por categoria para cada usuário.
- O processamento deve ser idempotente: no máximo um registro por usuário, categoria e data.
- Utilizar o último preço disponível até a data do snapshot.
- Incluir quantidade de ativos sem preço.
- Ao reiniciar após indisponibilidade, gerar snapshots das datas ausentes, usando movimentações e preços registrados até cada data.

### RF-025 — Consultar evolução patrimonial
- Retornar snapshots por intervalo de datas.
- Intervalo inclusivo, ordenado da data mais antiga para a mais recente.
- Permitir consulta consolidada ou de uma das três categorias.
- Não gerar valores artificiais para dias futuros.
- Informar quando houver datas sem snapshot disponível.

### RF-026 — Recalcular histórico afetado
Após alteração ou exclusão de movimentação, o backend deve recalcular snapshots da data da operação alterada até a data atual. O recálculo deve preservar as datas e preços históricos já registrados.

## Regras comuns da API

- Todos os endpoints de negócio exigem autenticação, exceto endpoints de autenticação e consulta pública de categorias.
- IDs são UUIDs.
- Datas de operação são enviadas como YYYY-MM-DD.
- Valores decimais são enviados como números JSON, não como texto formatado em moeda.
- Não aceitar UserId em DTOs de criação ou alteração.
- Erros de validação e negócio devem retornar ProblemDetails com código estável para tratamento pelo frontend.
