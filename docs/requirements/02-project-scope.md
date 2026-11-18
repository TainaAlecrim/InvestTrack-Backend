# Escopo do Projeto — Versão 1.0

**Versão:** 1.0  
**Status:** baseline aprovado pelo responsável pelo produto; detalhamento técnico definido para implementação.

## 1. Plataforma e idioma

- Aplicação exclusivamente Web.
- Backend disponibilizado por API HTTP/JSON.
- Interface futura será mantida em repositório separado.
- Idioma único: português do Brasil (pt-BR).
- Datas exibidas no padrão DD/MM/AAAA.
- Moeda exibida em Real brasileiro (R$).
- Fuso de referência para operações diárias: America/Sao_Paulo.
- Instantes técnicos armazenados em UTC.

## 2. Funcionalidades incluídas

### 2.1 Usuários e autenticação
- Cadastro com nome, e-mail e senha.
- Login e logout.
- Uma única sessão ativa por usuário.
- Isolamento dos dados por usuário autenticado.
- Consulta e alteração do nome.
- Alteração de senha mediante senha atual.
- Exibição da data de criação da conta.

### 2.2 Categorias e ativos
- Três categorias fixas: Ações, Fundos Imobiliários e Renda Fixa.
- Cadastro manual de ativos pelo usuário.
- Listagem, consulta, edição e exclusão de ativos, respeitando as regras de integridade.
- Um ativo pertence a uma única categoria e a uma única conta de usuário.

### 2.3 Movimentações
- Registro de compra e venda.
- Quantidade, preço unitário e data da operação.
- Histórico com filtros por ativo, tipo e período.
- Edição e exclusão.
- Revalidação de todo o histórico afetado após alterações.

### 2.4 Preços
- Cadastro manual do preço atual de cada ativo.
- Histórico de preços informados, com data registrada pelo sistema.
- Nenhuma integração externa de cotação.

### 2.5 Acompanhamento
- Quantidade atual.
- Preço médio ponderado.
- Custo das posições atuais.
- Valor atualizado das posições.
- Resultado não realizado.
- Distribuição por categoria.
- Dashboard consolidado.
- Snapshots diários e consulta da evolução patrimonial.

## 3. Funcionalidades fora da V1

- Aplicativos nativos Android e iOS.
- Criação, renomeação ou exclusão de categorias/carteiras.
- Categorias personalizadas.
- Importação CSV ou Excel.
- Integração com corretoras, bancos, B3 ou provedores de cotação.
- Atualização automática de preços de mercado.
- Execução de ordens ou movimentação financeira.
- Taxas, corretagem, emolumentos e impostos.
- Recuperação automática de senha por e-mail.
- Verificação de e-mail.
- Login social.
- Autenticação multifator.
- Alteração de e-mail.
- Exclusão de conta pelo usuário.
- Interface em outros idiomas.
- Compartilhamento de carteira entre usuários.
- Exportação de relatórios.

## 4. Limites importantes

- O usuário informa operações já realizadas em outra instituição.
- O preço manual não deve ser apresentado como cotação oficial ou em tempo real.
- O dashboard representa somente os dados cadastrados no InvestTrack.
- O resultado exibido será o resultado não realizado das posições que continuam abertas. Ganhos e perdas realizados em vendas anteriores não serão consolidados no resultado do dashboard na V1.
- Ativos sem preço informado serão identificados e não serão tratados como se tivessem preço zero para fins de valorização.

## 5. Premissas de implementação

- Um usuário possui as três categorias fixas desde o início, mesmo que estejam vazias.
- As categorias são valores controlados pelo backend; não haverá CRUD de carteiras.
- Os ativos e movimentações são isolados por usuário.
- O frontend será um consumidor da API e não terá autoridade para calcular saldos ou validar propriedade dos dados.
- PostgreSQL será o banco de dados da aplicação.

## 6. Evoluções possíveis após a V1

Importação CSV é a primeira candidata à V2. Outras evoluções só serão planejadas depois da entrega e avaliação da V1.
