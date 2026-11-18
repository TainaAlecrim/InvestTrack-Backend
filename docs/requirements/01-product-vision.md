# Visão do Produto — InvestTrack

**Versão:** 1.0  
**Status:** baseline de requisitos  
**Data:** 17/11/2026

## 1. Propósito

O InvestTrack é uma aplicação Web para controle e acompanhamento de investimentos que o próprio usuário já realizou fora da plataforma. O usuário cadastra ativos, informa compras e vendas concluídas e atualiza manualmente os preços. O backend calcula posições, custo de aquisição, valor atualizado e indicadores patrimoniais.

O InvestTrack é uma ferramenta de organização e acompanhamento. Não é uma corretora, não presta serviço de execução de ordens e não movimenta recursos financeiros.

## 2. Problema que o produto resolve

O usuário pode ter investimentos distribuídos em diferentes categorias e precisar reunir em um único lugar:
- quais ativos possui;
- quanto comprou e vendeu;
- qual quantidade permanece em carteira;
- qual é o custo médio de aquisição;
- quanto valem as posições com base nos últimos preços informados;
- como o patrimônio acompanhado evoluiu ao longo do tempo.

## 3. Público-alvo

Pessoas que desejam registrar manualmente e acompanhar seus próprios investimentos, sem conectar contas de corretoras ou bancos.

## 4. Objetivos da primeira versão

1. Oferecer cadastro, autenticação e isolamento dos dados por usuário.
2. Organizar ativos em três categorias fixas.
3. Registrar compras e vendas já realizadas.
4. Calcular quantidade atual e preço médio ponderado.
5. Permitir atualização manual dos preços.
6. Apresentar resumo patrimonial e distribuição por categoria.
7. Registrar snapshots diários para alimentar o histórico patrimonial.

## 5. Princípios do produto

- **Simplicidade:** entregar somente o necessário para uma primeira versão consistente.
- **Transparência:** deixar claro quando um valor usa preço desatualizado ou quando há ativo sem preço.
- **Integridade:** impedir quantidades negativas e operações incompatíveis com o histórico.
- **Privacidade:** um usuário nunca acessa dados de outro.
- **Rastreabilidade:** manter histórico das movimentações e dos preços informados.

## 6. Não objetivos

A versão 1.0 não:
- executa compras ou vendas;
- movimenta dinheiro;
- importa dados de corretoras;
- consulta cotações externas ou em tempo real;
- fornece recomendação de investimento;
- calcula impostos;
- calcula taxas, corretagem ou emolumentos;
- substitui extratos ou informes oficiais de instituições financeiras.

## 7. Indicadores de sucesso da versão 1.0

A versão será considerada funcional quando um usuário conseguir criar uma conta, registrar ativos e movimentações, atualizar preços manualmente, consultar o dashboard e visualizar a evolução diária, sem conseguir acessar dados de outros usuários.

## 8. Terminologia

| Termo | Significado no projeto |
|---|---|
| Ativo | Instrumento cadastrado pelo usuário, como uma ação, FII ou aplicação de renda fixa |
| Categoria | Uma das três divisões fixas: Ações, Fundos Imobiliários ou Renda Fixa |
| Movimentação | Compra ou venda que já aconteceu fora do InvestTrack |
| Preço informado | Último preço unitário cadastrado manualmente pelo usuário |
| Custo das posições | Custo de aquisição associado às unidades que ainda estão em carteira |
| Resultado não realizado | Valor atual das posições menos o custo das posições |
| Snapshot diário | Registro calculado do patrimônio em uma data específica |
