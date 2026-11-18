# Casos de Uso — InvestTrack

**Versão:** 1.0

## UC-01 — Criar conta
**Ator:** visitante. **Pré-condição:** e-mail ainda não cadastrado.

1. Visitante envia nome, e-mail e senha.
2. API valida campos e unicidade do e-mail.
3. API cria a conta e grava a data de criação em UTC.
4. API retorna sucesso.
5. Frontend apresenta confirmação e direciona para login.

Exceções: campos inválidos (400), e-mail já utilizado (409), serviço indisponível (5xx).

## UC-02 — Entrar
**Ator:** usuário cadastrado.

1. Usuário envia e-mail e senha.
2. API valida credenciais.
3. API cria identificador de sessão novo e invalida o anterior.
4. API emite cookie de autenticação seguro.
5. Frontend abre o dashboard.

Exceções: credenciais inválidas (401), conta temporariamente bloqueada (429 ou resposta de autenticação definida pela API), falha técnica (5xx).

## UC-03 — Sessão substituída
1. Sessão A está ativa.
2. Usuário realiza login na sessão B.
3. API grava o identificador de B como sessão ativa.
4. Sessão A faz nova requisição protegida.
5. API rejeita A com SESSION_REPLACED.
6. Frontend de A apresenta mensagem informando que houve novo login.

## UC-04 — Sair
1. Usuário solicita logout.
2. API invalida a sessão atual.
3. API encerra o cookie.
4. Frontend retorna à tela de login.

## UC-05 — Criar ativo
1. Usuário informa código, nome e categoria.
2. API valida categoria e dados.
3. API verifica duplicidade de código na conta.
4. API grava o ativo associado ao usuário autenticado.
5. API retorna o ativo criado.

Exceção: código duplicado na mesma conta (409).

## UC-06 — Registrar compra
1. Usuário seleciona um ativo próprio.
2. Informa quantidade, preço unitário e data.
3. API valida propriedade e valores.
4. API persiste a compra.
5. API recalcula quantidade e preço médio do ativo.
6. API recalcula snapshots afetados, quando aplicável.
7. API retorna a movimentação e posição atualizada.

## UC-07 — Registrar venda
1. Usuário seleciona um ativo próprio.
2. Informa quantidade, preço unitário e data.
3. API reprocessa o histórico cronologicamente.
4. Se houver saldo suficiente na data, persiste a venda.
5. API recalcula a posição e snapshots afetados.

Exceção: venda acima do saldo disponível (422 ou 409 conforme contrato do endpoint; código de negócio INSUFFICIENT_QUANTITY).

## UC-08 — Atualizar preço
1. Usuário informa o preço atual de um ativo próprio.
2. API valida o valor.
3. API registra o preço para a data local atual; se já houver preço no dia, substitui-o.
4. API retorna preço e data da atualização.

## UC-09 — Consultar dashboard
1. Frontend solicita dashboard.
2. API calcula posições com base nas movimentações.
3. API aplica o último preço conhecido de cada ativo.
4. API retorna custo atual, valor cotado, resultado não realizado, distribuição e ativos sem preço.

## UC-10 — Consultar evolução patrimonial
1. Frontend envia data inicial, data final e, opcionalmente, categoria.
2. API consulta snapshots do usuário.
3. API retorna os pontos em ordem cronológica.
4. Frontend apresenta o gráfico.

## UC-11 — Alterar senha
1. Usuário informa senha atual e nova senha.
2. API valida senha atual e política da nova senha.
3. API atualiza o hash.
4. API invalida a sessão atual.
5. Usuário precisa entrar novamente.

## UC-12 — Consultar ou alterar perfil
1. Usuário consulta perfil e recebe nome, e-mail e data de cadastro.
2. Para alterar nome, envia o novo nome.
3. API atualiza somente o nome do usuário autenticado.
