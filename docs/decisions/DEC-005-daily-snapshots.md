# DEC-005 — Snapshots diários de patrimônio

- **Status:** Aprovada para V1
- **Data:** 17/11/2026

## Contexto
O dashboard precisa mostrar evolução patrimonial mesmo que movimentações e preços sejam editados posteriormente.

## Decisão
Gerar um snapshot consolidado e um por categoria diariamente às 23:55 America/Sao_Paulo. Executar por BackgroundService na API na V1, com recuperação de dias ausentes e chave única por usuário/data/categoria. Utilizar o último preço informado até a data de referência.

## Consequências
- Histórico é reconstruível a partir de movimentações e preços datados.
- Alterações retroativas exigem recálculo dos snapshots afetados.
- A V1 pressupõe uma instância ativa da API.
